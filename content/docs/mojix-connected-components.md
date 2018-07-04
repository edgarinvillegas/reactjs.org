---
id: mojix-connected-components
title: Connected components
permalink: docs/mojix-connected-components.html
prev: mojix-action-creators
---

### With only dispatch prop (thunk and regular dispatch)
TODO. The current patterns we have don't expose `dispatch` to the components.

### With state props (mapStateToProps)
- Create StateProps interface
- mapStateToProps signature is:
  `const mapStateToProps = (state: IStoreState, ownProps: IOwnProps, mergeProps: IMergeProps): IStateProps`
- Component will receive & StateProps as well

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
    name: string;
    errorMessage: string;
}

// mapStateToProps always has this signature.
function mapStateToProps(state: IStoreState, ownProps: IOwnProps): IStateProps {
    return {
        name: state.teamName,
        errorMessage: state.errorCode ? 'There was an error' : ''
    }
}

export default connect(mapStateToProps)(Team);

```

### With mapDispatchToProps

Use this when you don't want your component to know about `dispatch`, just methods. This is the preferred way.

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

// These have the same signatu
interface IDispatchProps {
    trackVisit: () => void,
    // Could also be persist: ReturnType<typeof persistThunk> if type is the same
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

### With mergeProps


Use this when you want to have full control over the props your component receives.
Check the official docs here: https://github.com/reduxjs/react-redux/blob/master/docs/api.md

Merge props has the last word of the props that the component will receive. It's very powerful
but with a great power, comes a great responsibility.

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
      extra: stateProps.length > 0
   };
}

export default connect(mapStateToProps, mapDispatchToProps, mergeProps)(HomeScene);

```


