---
id: mojix-action-creators
title: Action creators
permalink: docs/mojix-action-creators.html
next: mojix-connected-components.html
---

### Regular actions

Creation with createAction (redux-actions library).

The following example will create an action like:
```js
{
    type: 'SET_USERNAME_ACTION',
    payload: 'automationuser'
}
```

(Having a payload field complies with the FSA (Flux Standard Action) standard.)

To do this, we do 4 things:
- Define a string constant
- Define a payload type
- Create the action
- Export a dispatchable action

```typescript
// actions.ts
import { createAction } from 'redux-actions';

export const SET_USERNAME_ACTION = 'SET_USERNAME_ACTION';
export type TSetUsernamePayload = string; // Because the payload is a string

export const setUsernameAction = createAction(SET_USERNAME_ACTION, (username: string): TSetUsernamePayload => {
   return username;
});

```

This `setUsernameAction` can be used in reducers created with redux-actions' `handleActions()`.

Unfortunately, actions created by createAction lose parameter names when dispatched,
that's why we re-export it with proper typing as `setUsernameOperation`:


```typescript
// operations.ts

import { setUsernameAction, TSetUsernamePayload } from './actions';

export const setUsernameOperation = setUsernameAction as (username: string) => IAction<TSetUsernamePayload>;

```

Now this action aka 'operation' can be dispatched like `dispatch(setUsernameOperation('automationuser'))`


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
         dispatch(setLoginErrorOperation(exc.message));  //Normal action dispatch
         throw exc;
      }
   };
}
```