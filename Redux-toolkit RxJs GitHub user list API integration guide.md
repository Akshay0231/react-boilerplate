# React 18 App with Redux Toolkit and RxJS Observables Documentation

This documentation provides a step-by-step guide to integrating Redux Toolkit and RxJS Observables into a React 18 application for fetching a list of GitHub users. The combination of Redux Toolkit and RxJS Observables offers a powerful solution for managing application state and handling asynchronous operations.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Project Setup](#project-setup)
3. [Redux Toolkit Configuration](#redux-toolkit-configuration)
4. [Reducers](#reducers)
5. [Combine Reducers](#combine-reducers)
6. [UserList Component Setup](#userlist-component-setup)
7. [API Data Fetching with RxJS](#api-data-fetching-with-rxjs)
8. [Connect Redux Store](#connect-redux-store)
9. [Summary](#summary)

## Prerequisites

- Basic knowledge of React, Redux, and RxJS.
- Familiarity with creating React components and managing state.

## Project Setup

1. Create a new React 18 project using your preferred tool (e.g., Create React App).
2. Ensure you have React 18 and Redux Toolkit installed as project dependencies.

## Redux Toolkit Configuration

1. Create a file named `store.js` in the source directory.
2. Import the necessary functions from Redux Toolkit and create your Redux store.
3. Example:

```javascript
// store.js
import { configureStore } from '@reduxjs/toolkit';
import rootReducer from './reducers';

const store = configureStore({
  reducer: rootReducer,
});

export default store;
```

## Reducers

1. Create a directory named `reducers` in the source directory.
2. Inside the `reducers` directory, create a file for each Redux slice (e.g., `userListSlice.js`).
3. Define your Redux slice using Redux Toolkit.
4. Example:

```javascript
// reducers/userListSlice.js
import { createSlice } from '@reduxjs/toolkit';

const userListSlice = createSlice({
  name: 'userList',
  initialState: {
    data: [],
    loading: false,
    error: null,
  },
  reducers: {
    setUserList: (state, action) => {
      state.data = action.payload;
      state.loading = false;
      state.error = null;
    },
    setLoading: state => {
      state.loading = true;
      state.error = null;
    },
    setError: (state, action) => {
      state.loading = false;
      state.error = action.payload;
    },
  },
});

export const { setUserList, setLoading, setError } = userListSlice.actions;
export default userListSlice.reducer;
```

## Combine Reducers

1. Create a file named `rootReducer.js` inside the `reducers` directory.
2. Use Redux Toolkit's `combineReducers` function to combine your reducers.
3. Example:

```javascript
// reducers/rootReducer.js
import { combineReducers } from '@reduxjs/toolkit';
import userListReducer from './userListSlice';

const rootReducer = combineReducers({
  userList: userListReducer,
});

export default rootReducer;
```

## UserList Component Setup

1. Create a file named `UserList.js` in the components directory.
2. Implement the UserList component JSX code.
3. Example:

```javascript
// components/UserList.js
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { fetchUserList } from './logic';

const UserList = () => {
  const dispatch = useDispatch();
  const userList = useSelector(state => state.userList);
  const { data, loading, error } = userList;

  useEffect(() => {
    dispatch(fetchUserList());
  }, [dispatch]);

  const handleFetchClick = () => {
    dispatch(fetchUserList());
  };

  return (
    <div>
      <h1>User List:</h1>
      {loading ? (
        <p>Loading...</p>
      ) : (
        <ul>
          {data.map(user => (
            <li key={user.id}>{user.login}</li>
          ))}
        </ul>
      )}
      {error && <p>Error: {error.message}</p>}
      <button onClick={handleFetchClick} disabled={loading}>
        {loading ? 'Fetching...' : 'Fetch User List'}
      </button>
    </div>
  );
};

export default UserList;
```

## API Data Fetching with RxJS

1. Create a file named `logic.js` inside the components directory.
2. Implement the logic for fetching the user list from the GitHub API using RxJS Observables and dispatching Redux actions.
3. Example:

```javascript
// components/logic.js
import { from } from 'rxjs';
import { tap, catchError } from 'rxjs/operators';
import { setUserList, setLoading, setError } from '../reducers/userListSlice';

export const fetchUserList = () => dispatch => {
  dispatch(setLoading());

  const apiObservable = from(fetch('https://api.github.com/users'));

  apiObservable
    .pipe(
      tap(response => {
        if (response.ok) {
          return response.json();
        } else {
          throw new Error('Failed to fetch user list.');
        }
      }),
      tap(data => {
        dispatch(setUserList(data));
      }),
      catchError(error => {
        dispatch(setError(error));
        return [];
      })
    )
    .subscribe();
};
```

## Connect Redux Store

1. Open the `index.js` file in the source directory.
2. Import the Redux Provider component and your Redux store.
3. Wrap your root component with the Provider component, passing the store as a prop.
4. Example:

```javascript
// index.js
import React from 'react';
import { render } from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import UserList from './components/UserList';

render(
  <Provider store={store}>
    <UserList />
  </Provider>,
  document.getElementById('root')
);
```


## Make sure to adjust the instructions, component names, and file paths to match your project's structure. This documentation provides a comprehensive guide for integrating Redux Toolkit and RxJS Observables in a React 18 app, making it easier for you and your team to understand and follow the implementation process.
