---
id: mojix-connected-components
title: Connected components
permalink: docs/mojix-connected-components.html
prev: mojix-action-creators
---

### With only dispatch prop (thunk and regular dispatch)
TODO


### With state props (mapStateToProps)
- Create StateProps interface
- mapStateToProps signature is:
  `const mapStateToProps = (state: IStoreState, ownProps: IOwnProps): IStateProps`
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
    errorMessage: string
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

### With mapDispatchToProps and bindActionCreators

Use this when you don't want your component to know about `dispatch`, just methods

```typescript
// PetPage.tsx

import { Dispatch } from 'redux';
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



