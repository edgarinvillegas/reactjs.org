---
id: engage-connected-components
title: Connected components
permalink: docs/engage-connected-components.html
prev: engage-action-creators
---

### With dispatch
This is useful when you want to call `dispatch` inside the component.

To achieve this,
- Add `IThunkDispatchProp` to your props definition.
`const MyComponent: SFC<IOwnProps & IThunkDispatchProp> = ...`.  This will allow to use
This will allow props to have `dispatch`.

- Call connect as usual. You are not forced to provide parameters to it. Remember that just calling `connect` allows to have `dispatch`.
`connect()(MyComponent)`

```typescript
// PetPage.tsx

import { connect } from 'react-redux';
import { IThunkDispatchProp } from 'store/reduxTypes';

interface IOwnProps {
    name: string;
}

const PetPage: SFC<IOwnProps & IThunkDispatchProp> = (props) => {
    const { name, dispatch } = props;

    const handleClick = () => {
        dispatch(trackVisitAction('Pet Page'));
    };

    const handlePersistClick = () => {
        const timestamp = new Date().getTime();
        dispatch(persistThunk(timestamp));
    };

    return (
        <div>
            <h1>{name}</h1>
            <button onClick={handleTrackClick}> Track </button>
            <button onClick={handlePersistClick} Persist </button>
        </div>
    );
};

// mapStateToProps always has this signature.

export default connect()(PetPage);

```

*Note that we don't use this pattern currently* We use dispatch props one instead, keep reading.

### With state props (mapStateToProps)
- Create IStateProps interface
- mapStateToProps signature is:
  `const mapStateToProps = (state: IStoreState, ownProps: IOwnProps): IStateProps`
- Component will receive OwnProps & IStateProps as well

```typescript
interface IOwnProps {
    name: string;
}

// props will be of type IOwnProps & IStateProps (the 'merge')
const Team: SFC<IOwnProps & IStateProps> = (props) => {
    const { name, errorMessage } = props;
    return (
        <div>
            <h1>{name}</h1>
            <span> {errorMessage} </span>
        </div>
    )
};

// Props that will come from state
interface IStateProps {
    errorMessage: string;
}

// mapStateToProps always has this signature.
function mapStateToProps(state: IStoreState, ownProps: IOwnProps): IStateProps {
    return {
        errorMessage: state.errorCode ? 'There was an error' : ''
    }
}

export default connect(mapStateToProps)(Team);

```


### With dispatch props (mapDispatchToProps)

Use this when you don't want your component to know about `dispatch`, it will be abstracted so that the component only sees regular methods. This is our preferred way.
The dispatch props will always be functions.

```typescript
// PetPage.tsx

import { connect } from 'react-redux';

interface IOwnProps {
    name: string;
}

// props will be of type IOwnProps & IDispatchProps (the 'merge')
const PetPage: SFC<IOwnProps & IDispatchProps> = (props) => {
    const { name, trackVisit, persist } = props;

    const handleClick = () => {
        props.trackVisit();
    };

    const handlePersistClick = () => {
        props.persist(new Date().getTime())
    };

    return (
        <div>
            <h1>{name}</h1>
            <button onClick={handleTrackClick}> Track </button>
            <button onClick={handlePersistClick} Persist </button>
        </div>
    )
};

// These signatures will usually be similar or equal to the action creators
interface IDispatchProps {
    trackVisit: () => void,
    persist: (timestamp: number) => void
}

// mapStateToProps always has this signature.
function mapDispatchToProps (dispatch: IThunkDispatch): IDispatchProps {
    return {
        trackVisit: () => { dispatch(trackVisitAction('Pet Page')) },
        persist: (timestamp) => { dispatch(persistThunk(timestamp)) }
    }
}

export default connect(undefined, mapDispatchToProps)(PetPage);

```

### With props merging (mergeProps)


Use this when you want to have full control over the props your component receives, based on the state props, dispatch props and own props.
Check the official docs here: https://github.com/reduxjs/react-redux/blob/master/docs/api.md

Merge props has the last word of the props that the component will receive. It's very powerful
but with a great power, comes a great responsibility.

Steps:
- Create interface `IMergeProps`. This can be called just `IProps`. This interface will have all the final props of the component.
- Create `mergeProps` function. Its signature is:

 `(stateProps: IStateProps, dispatchProps: IDispatchProps, ownProps: IOwnProps) => IMergeProps`

This function will return the final props of the component. You're free to ignore, rename, add properties, etc. Common case is merging, or adding a dispatcher that needs a state prop.

- Send it as 3rd parameter of `connect`.

- Component props will go just us `SFC<IMergeProps>`, no more `IOwnProps` or other types here.


```typescript
// PetPage.tsx

import { connect } from 'react-redux';

interface IOwnProps {
    name: string;
}

// props will be of type IMergeProps
const PetPage: SFC<IMergeProps> = (props) => {
    const { name, errorMessage, trackVisit, persist, extra } = props;

    const handleClick = () => {
        props.trackVisit();
    };

    const handlePersistClick = () => {
        props.persist(new Date().getTime())
    };

    return (
        <div>
            <h1>{name}</h1>
            <span>{extra && errorMessage}</span>
            <button onClick={handleTrackClick}> Track </button>
            <button onClick={handlePersistClick} Persist </button>
        </div>
    )
};

// Props that will come from state
interface IStateProps {
    errorMessage: string
}

// mapStateToProps always has this signature.
function mapStateToProps(state: IStoreState, ownProps: IOwnProps): IStateProps {
    return {
        errorMessage: state.errorCode ? 'There was an error' : ''
    }
}

// These have the same signatu
interface IDispatchProps {
    trackVisit: () => void,
    persist: (timestamp: number) => void
}

// mapStateToProps always has this signature.
function mapDispatchToProps (dispatch: IThunkDispatch): IDispatchProps {
    return {
        trackVisit: () => { dispatch(trackVisitAction('Pet Page')) },
        persist: (timestamp) => { dispatch(persistThunk(timestamp)) }
    }
}

//This interface will vary a lot depending on what you return from mergeProps
interface IMergeProps extends IDispatchProps & IStateProps & IOwnProps {
    extra: boolean;
}

// mapStateToProps always has this signature.
function mergeProps(stateProps: IStateProps, dispatchProps: IDispatchProps, ownProps: IOwnProps): IMergeProps {
   return {
      ...ownProps,
      ...stateProps,
      ...dispatchProps,
      extra: stateProps.errorMessage.length > 0
   };
}

export default connect(mapStateToProps, mapDispatchToProps, mergeProps)(HomeScene);

```

Note that mergeProps has all the control of the final props. In the last example, if we had:

```typescript
function mergeProps(stateProps: IStateProps, dispatchProps: IDispatchProps, ownProps: IOwnProps): IMergeProps {
   return {
      extra: stateProps.errorMessage.length > 0
   };
}
```


The interface would look like just:
```typescript
interface IMergeProps {
    extra: boolean;
}
```

Component definition would be like:

```typescript
const PetPage: SFC<IMergeProps> = ({ extra }) {
```

Note that the component now only receives the `extra` prop!!

#### Common usages of mergeProps

We have found these cases

- When you have prop name collisions and default merger (see docs) doesn't merge as your needs
- When you need to dispatch an action that needs state props (because state props are not accesible from mapDispatchToProps)

Example `connect` for this last case:

```typescript
function mergeProps(stateProps: IStateProps, { dispatch }: IThunkDispatchProp, ownProps: IOwnProps) {
   return {
      ...ownProps,
      ...stateProps,
      logVisitedPage: () => dispatch(logVisitedPageAction(stateProps.username))
   };
}
```

In this example, mergeProps is also doing the usual job of `mapDispatchToProps`.

To get this `dispatch` argument, connect would look like:

```
export default connect(mapStateToProps, (dispatch: IThunkDispatch) => ({ dispatch }), mergeProps)(HomeScene);
```

Shorter alternative way::

```
export default connect(mapStateToProps, undefined!, mergeProps)(HomeScene);
```

The bang operator (`!`) is just to please the ts compiler.

(note mapDispatchToProps is returning the dispatch method. TODO: evaluate if undefined! is the same).