---
id: engage-action-creators
title: Action creators & Reducers
permalink: docs/engage-action-creators.html
next: engage-connected-components.html
---

### Regular actions

The following example will create an action like:
```js
{
    type: 'SET_USERNAME_ACTION',
    payload: 'automationuser'
}
```
And another action like:
```js
{
    type: 'SELECT_ENV_ACTION',
    payload: 'qa1'
}
```


(Having a payload field complies with the FSA (Flux Standard Action) standard.)

To do this, we do 4 things:
- Define a string constant
- Create the action with createAction
- Create the ActionsUnion (only useful for reducers)

```typescript
// actions.ts
import { ActionsUnion, createAction } from 'lib/reduxHelpers';

export const SET_USERNAME_ACTION = 'SET_USERNAME_ACTION';
export const SELECT_ENV_ACTION = 'SELECT_ENV_ACTION';

export const setUsernameAction = (username: string) => createAction(SET_USERNAME_ACTION, username);
export const selectEnvAction = (env: string) => createAction(SELECT_ENV_ACTION, env);

export const Actions = {
   setUsernameAction,
   selectEnvAction
};

export type Actions = ActionsUnion<typeof Actions>;

```

It's straightforward to dispatch these actions: `dispatch(setUsernameAction('automationuser'))`


### Thunks

#### Simple thunks

They don't return anything, that's why they return `IThunkAction<void>`

```typescript
// operations.ts
export function persistThunk(timestamp: number): IThunkAction<void> {
   return (dispatch, getState) => {
      setTimeout(() => {
        dispatch({ type: 'SAVE_TIMESTAMP', payload: timestamp })
      }, 2000);
   };
}
```


#### Promise thunks

They produce sideeffects and dispatch actions asynchronously.

The following example

```typescript
export function loginThunk(username: string, password: string): IThunkAction<Promise<Session>> {
   return async (dispatch, getState) => {
      try {
         // response is of type Session
         const response = await Api.login(username, password);
         dispatch(setToken(response.token));  //Normal action dispatch
         return response;
      } catch (exc) {
         dispatch(setLoginErrorAction(exc.message));  //Normal action dispatch
         throw exc;
      }
   };
}
```

## Reducers

Reducers are very straightforward after having created the action creators.

- Create interface/type for the piece of state being reduced
- Create reducer function of type `Reducer<StateType, ActionTypes>`,
   It receives 2 parameters,  `state` and `action`.

Consider the action creator example above. A reducer for these actions would be like:

```typescript
import { Reducer } from 'redux';

import * as fromActions from './actions';       // Assume actions.ts from the example above

interface ISessionState {
   readonly loggedInUser: string;
   readonly env: string;
}

const initialState: ISessionState = {
  loggedInUser: '',
  env: ''
};

const reducer: Reducer<ISessionState, fromActions.Actions> = (state = initialState, action) => {
   switch (action.type) {
      case fromActions.SET_USERNAME_ACTION: {
         return {
            ...state,
            loggedInUser: action.payload  // This payload is a string, check setUsernameAction()
         };
      }
      case fromActions.SELECT_ENV_ACTION: {     // We open braces so we can define local variables
         const environment = action.payload; // Same, Check selectEnvAction() definition
         return { ...state, env: environment };
      }
      default:
         return initialState;
   }
};

export default reducer;
```

Note that in the example, `action.payload` is a string, but it can actually be whatever you want.
