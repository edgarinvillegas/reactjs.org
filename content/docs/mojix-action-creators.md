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