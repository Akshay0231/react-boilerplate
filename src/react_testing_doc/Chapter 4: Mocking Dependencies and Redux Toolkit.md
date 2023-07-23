# Chapter 4: Mocking Dependencies and Redux Toolkit

## 4.1 Introduction

In this chapter, we will delve into the concept of mocking dependencies to isolate components during testing. Mocking is a powerful technique that allows us to replace real dependencies, such as Redux actions, reducers, selectors, and external API calls, with custom implementations specifically designed for testing. We will focus on mocking Redux Toolkit functionalities to enable more focused unit tests and explore best practices for mocking external dependencies and API calls.

## 4.2 Understanding the Concept of Mocking

Mocking is the process of creating fake implementations of functions, modules, or services to simulate their behavior during testing. By mocking dependencies, we can isolate the component being tested from its external dependencies, ensuring that the test focuses solely on the component's logic and behavior.

In the context of React testing, mocking is particularly useful for:

- Reducing test execution time: Mocked dependencies can be designed to return predetermined values, bypassing time-consuming processes like API calls.
- Simplifying test setup: Mocks allow us to control the behavior of external dependencies, making it easier to test different scenarios without the need for real data.
- Achieving deterministic tests: With mocks, tests produce consistent and predictable results, making it easier to identify and debug issues.

**Expert's Note:** When using mocking, it's essential to strike a balance between isolating the component for testing and ensuring the mock represents the real behavior of the dependency accurately. Over-mocking might lead to tests that pass but do not reflect the actual behavior of the application, resulting in false positives.

## 4.3 Mocking Redux Toolkit Actions, Reducers, and Selectors

### 4.3.1 Mocking Redux Actions

To mock Redux actions, we can use Jest's `jest.fn()` to create a function that simulates the action creator. The function can return a predefined value or a mocked action object when invoked.

Example of mocking a Redux action:

```javascript
// actions/authActions.js
export const loginSuccess = (user) => ({
  type: 'LOGIN_SUCCESS',
  payload: user,
});

// __tests__/actions/authActions.test.js
import { loginSuccess } from '../../actions/authActions';

describe('authActions', () => {
  it('should create a loginSuccess action', () => {
    const user = { id: 1, username: 'testuser' };
    const expectedAction = { type: 'LOGIN_SUCCESS', payload: user };
    const action = loginSuccess(user);
    expect(action).toEqual(expectedAction);
  });
});
```

### 4.3.2 Mocking Redux Reducers

To mock Redux reducers, we can create a custom implementation of the reducer function that returns the desired state when invoked with a specific action.

Example of mocking a Redux reducer:

```javascript
// reducers/authReducer.js
const initialState = {
  user: null,
};

const authReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'LOGIN_SUCCESS':
      return { ...state, user: action.payload };
    default:
      return state;
  }
};

// __tests__/reducers/authReducer.test.js
import authReducer from '../../reducers/authReducer';

describe('authReducer', () => {
  it('should handle LOGIN_SUCCESS action', () => {
    const user = { id: 1, username: 'testuser' };
    const action = { type: 'LOGIN_SUCCESS', payload: user };
    const expectedState = { user: user };
    const newState = authReducer(undefined, action);
    expect(newState).toEqual(expectedState);
  });
});
```

### 4.3.3 Mocking Redux Selectors

To mock Redux selectors, we can create a custom implementation of the selector function that returns the desired data when invoked.

Example of mocking a Redux selector:

```javascript
// selectors/authSelectors.js
export const selectUser = (state) => state.auth.user;

// __tests__/selectors/authSelectors.test.js
import { selectUser } from '../../selectors/authSelectors';

describe('authSelectors', () => {
  it('should select the user from the state', () => {
    const user = { id: 1, username: 'testuser' };
    const state = { auth: { user } };
    const selectedUser = selectUser(state);
    expect(selectedUser).toEqual(user);
  });
});
```

## 4.4 Best Practices for Mocking External Dependencies and API Calls

Assume we have a simple React component called `UserList` that fetches a list of users from an external API and displays them. We'll be using `axios` for API calls in these examples.

```javascript
// components/UserList.js

import React, { useState, useEffect } from 'react';
import axios from 'axios';

const UserList = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await axios.get('https://api.example.com/users');
        setUsers(response.data);
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    };
    fetchUsers();
  }, []);

  return (
    <div>
      <h2>User List</h2>
      <ul>
        {users.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
};

export default UserList;
```

Now, let's go through the best practices with examples:

### 1. Create Realistic Mocks:

```javascript
// __mocks__/axios.js (In the same folder as the UserList component)

const mockUsers = [
  { id: 1, name: 'John Doe' },
  { id: 2, name: 'Jane Smith' },
];

export default {
  get: jest.fn().mockResolvedValue({ data: mockUsers }),
};
```

In this example, we create a realistic mock for the `axios` library that returns a predefined list of users when `axios.get()` is called. This way, we avoid real API calls during testing, making the tests faster and more reliable.

### 2. Use Test Data:

```javascript
// __mocks__/axios.js

const mockUsers = [
  { id: 1, name: 'John Doe' },
  { id: 2, name: 'Jane Smith' },
  // Add more test data here for different scenarios
];

export default {
  get: jest.fn().mockResolvedValue({ data: mockUsers }),
};
```

By adding more test data to the mock, we can test various scenarios, such as empty user lists, error responses, or edge cases.

### 3. Mock Responsiveness:

```javascript
// __mocks__/axios.js

const mockUsers = [
  { id: 1, name: 'John Doe' },
  { id: 2, name: 'Jane Smith' },
];

export default {
  get: jest.fn((url) => {
    if (url === 'https://api.example.com/users') {
      // Simulate success response
      return Promise.resolve({ data: mockUsers });
    } else {
      // Simulate error response
      return Promise.reject(new Error('API not found'));
    }
  }),
};
```

By customizing the mock's behavior based on the URL, we can simulate both success and error responses to test how the component handles different scenarios.

### 4. Clear Mocks Between Tests:

```javascript
// __tests__/components/UserList.test.js

import React from 'react';
import { render, screen } from '@testing-library/react';
import UserList from '../../components/UserList';
import axios from 'axios';

jest.mock('axios');

describe('UserList Component', () => {
  afterEach(() => {
    jest.clearAllMocks();
  });

  it('should fetch and display users', async () => {
    render(<UserList />);

    // Assert API call was made
    expect(axios.get).toHaveBeenCalledTimes(1);
    expect(axios.get).toHaveBeenCalledWith('https://api.example.com/users');

    // Simulate API response
    const mockUsers = [
      { id: 1, name: 'John Doe' },
      { id: 2, name: 'Jane Smith' },
    ];
    axios.get.mockResolvedValueOnce({ data: mockUsers });

    // Assert users are displayed
    const userNames = screen.getAllByRole('listitem').map((li) => li.textContent);
    expect(userNames).toEqual(['John Doe', 'Jane Smith']);
  });
});
```

Clearing mocks between tests using `jest.clearAllMocks()` ensures that the mock state is reset, and the test environment remains clean for each test.

### 5. Consider Unhappy Paths:

```javascript
// __mocks__/axios.js

export default {
  get: jest.fn((url) => {
    if (url === 'https://api.example.com/users') {
      // Simulate success response
      return Promise.resolve({ data: mockUsers });
    } else {
      // Simulate error response
      return Promise.reject(new Error('API not found'));
    }
  }),
};
```

By simulating an error response for URLs other than the one expected, we can test how the component handles unexpected API calls and errors.

### 6. Avoid Over-Mocking:

```javascript
// __mocks__/axios.js

export default {
  // A single mock function can handle all GET requests
  get: jest.fn((url) => {
    if (url === 'https://api.example.com/users') {
      return Promise

.resolve({ data: mockUsers });
    } else if (url === 'https://api.example.com/posts') {
      return Promise.resolve({ data: mockPosts });
    }
  }),
};
```

By handling multiple URLs in a single mock, we avoid over-mocking and keep the mock implementation concise and maintainable.

### 7. Use Jest's `jest.mock()`:

```javascript
// __tests__/components/UserList.test.js

jest.mock('axios', () => ({
  get: jest.fn().mockResolvedValue({ data: mockUsers }),
}));
```

Using `jest.mock()` at the top of your test file allows you to automatically mock entire modules, making it easier to handle complex dependencies.

By following these best practices, you can create realistic and effective mocks for your external dependencies and API calls, enabling comprehensive testing of your React components with predictable and deterministic results.

## 4.5 Conclusion

In this chapter, we explored the concept of mocking dependencies to isolate components for testing in React applications. We learned how to mock Redux actions, reducers, and selectors using Jest's `jest.fn()`, and created custom implementations of these functionalities. Additionally, we discussed best practices for mocking external dependencies and API calls, ensuring effective and realistic unit tests.

Mocking is a powerful technique that empowers us to write focused and reliable tests, providing confidence in the quality and stability of our React applications. As you continue to develop your application, leverage mocking to create comprehensive test suites that validate various scenarios and edge cases. Happy testing!