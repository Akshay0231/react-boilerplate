# Chapter 8: Testing Forms and User Interactions

## 8.1 Introduction

In this chapter, we will focus on testing forms and user interactions in a React application. We will use a simple example of a "Create Account" form, similar to Google's account creation form, containing input fields for First Name, Last Name, Username, Password, and Confirm Password. We will cover how to test user interactions with the form, simulate user input and form submissions, and implement best practices for testing form validation and error handling.

## 8.2 Example: Google Create Account Form

Let's create a basic React component for the "Create Account" form:

```javascript
// components/CreateAccountForm.js

import React, { useState } from 'react';

const CreateAccountForm = () => {
  const [formData, setFormData] = useState({
    firstName: '',
    lastName: '',
    username: '',
    password: '',
    confirmPassword: '',
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData((prevFormData) => ({
      ...prevFormData,
      [name]: value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // Add logic to handle form submission, e.g., API call to create account
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="firstName">First Name:</label>
        <input
          type="text"
          id="firstName"
          name="firstName"
          value={formData.firstName}
          onChange={handleChange}
          required
        />
      </div>
      <div>
        <label htmlFor="lastName">Last Name:</label>
        <input
          type="text"
          id="lastName"
          name="lastName"
          value={formData.lastName}
          onChange={handleChange}
          required
        />
      </div>
      <div>
        <label htmlFor="username">Username:</label>
        <input
          type="text"
          id="username"
          name="username"
          value={formData.username}
          onChange={handleChange}
          required
        />
      </div>
      <div>
        <label htmlFor="password">Password:</label>
        <input
          type="password"
          id="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          required
        />
      </div>
      <div>
        <label htmlFor="confirmPassword">Confirm Password:</label>
        <input
          type="password"
          id="confirmPassword"
          name="confirmPassword"
          value={formData.confirmPassword}
          onChange={handleChange}
          required
        />
      </div>
      <button type="submit">Create Account</button>
    </form>
  );
};

export default CreateAccountForm;
```

## 8.3 Testing User Interactions with Forms, Buttons, and UI Elements

To test user interactions with the form, we'll use the `@testing-library/react` library, which provides utilities to interact with the rendered components and simulate user behavior.

Let's write tests to simulate user input and button clicks for the "Create Account" form:

```javascript
// __tests__/components/CreateAccountForm.test.js

import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import CreateAccountForm from '../../components/CreateAccountForm';

describe('CreateAccountForm Component', () => {
  it('should update form fields on user input', () => {
    render(<CreateAccountForm />);

    const firstNameInput = screen.getByLabelText('First Name:');
    const lastNameInput = screen.getByLabelText('Last Name:');
    const usernameInput = screen.getByLabelText('Username:');
    const passwordInput = screen.getByLabelText('Password:');
    const confirmPasswordInput = screen.getByLabelText('Confirm Password:');

    fireEvent.change(firstNameInput, { target: { value: 'John' } });
    fireEvent.change(lastNameInput, { target: { value: 'Doe' } });
    fireEvent.change(usernameInput, { target: { value: 'john_doe' } });
    fireEvent.change(passwordInput, { target: { value: 'password123' } });
    fireEvent.change(confirmPasswordInput, { target: { value: 'password123' } });

    expect(firstNameInput.value).toBe('John');
    expect(lastNameInput.value).toBe('Doe');
    expect(usernameInput.value).toBe('john_doe');
    expect(passwordInput.value).toBe('password123');
    expect(confirmPasswordInput.value).toBe('password123');
  });

  it('should call handleSubmit when "Create Account" button is clicked', () => {
    const handleSubmit = jest.fn();
    render(<CreateAccountForm onSubmit={handleSubmit} />);

    const createAccountButton = screen.getByText('Create Account');
    fireEvent.click(createAccountButton);

    expect(handleSubmit).toHaveBeenCalledTimes(1);
  });
});
```

In the above tests, we use `screen.getByLabelText()` to get input elements based on their associated labels. We use `fireEvent.change()` to simulate user input by providing the `target.value` to each input field. After setting the values, we use assertions to ensure that the form fields are correctly updated.

In the second test, we use `screen.getByText()` to get the "Create Account" button and `fireEvent.click()` to simulate a button click. We also use `jest.fn()` to create a mock function `handleSubmit` that will be called when the button is clicked. We then assert that the `handleSubmit` function has been called once.

## 8.4 Best Practices for Testing Form Validation and Error Handling

Testing form validation and error handling requires simulating different scenarios where users input invalid data and verifying how the form responds to such inputs.

Let's add form validation logic to our example "Create Account" form and write tests to cover form validation scenarios:

```javascript
// components/CreateAccountForm.js

import React, { useState } from 'react';

const CreateAccountForm = () => {
  const [formData, setFormData] = useState({
    firstName: '',
    lastName: '',
    username: '',
    password: '',
    confirmPassword: '',
  });

  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData((prevFormData) => ({
      ...prevFormData,
      [name]: value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    const errors = {};
    if (!formData.firstName) {
      errors.firstName = 'First Name is required';
    }
    if (!formData.lastName) {
      errors.lastName = 'Last Name is required';
    }
    if (!formData.username) {
      errors.username = 'Username is required';
    }
    if (!formData.password) {
      errors.password = 'Password is required';
    } else if (formData.password !== formData.confirmPassword) {
      errors.confirmPassword = 'Passwords do not match';
    }

    setErrors(errors);

    if (Object.keys(errors).length === 0) {
      // Add logic to handle form submission, e.g., API call to create account
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="firstName">First Name:</label>
        <input
          type="text"
          id="firstName"
          name="firstName"
          value={formData.firstName

}
          onChange={handleChange}
          required
        />
        {errors.firstName && <div>{errors.firstName}</div>}
      </div>
      <div>
        <label htmlFor="lastName">Last Name:</label>
        <input
          type="text"
          id="lastName"
          name="lastName"
          value={formData.lastName}
          onChange={handleChange}
          required
        />
        {errors.lastName && <div>{errors.lastName}</div>}
      </div>
      <div>
        <label htmlFor="username">Username:</label>
        <input
          type="text"
          id="username"
          name="username"
          value={formData.username}
          onChange={handleChange}
          required
        />
        {errors.username && <div>{errors.username}</div>}
      </div>
      <div>
        <label htmlFor="password">Password:</label>
        <input
          type="password"
          id="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          required
        />
        {errors.password && <div>{errors.password}</div>}
      </div>
      <div>
        <label htmlFor="confirmPassword">Confirm Password:</label>
        <input
          type="password"
          id="confirmPassword"
          name="confirmPassword"
          value={formData.confirmPassword}
          onChange={handleChange}
          required
        />
        {errors.confirmPassword && <div>{errors.confirmPassword}</div>}
      </div>
      <button type="submit">Create Account</button>
    </form>
  );
};

export default CreateAccountForm;
```

Now, let's write tests to cover form validation and error handling scenarios:

```javascript
// __tests__/components/CreateAccountForm.test.js

import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import CreateAccountForm from '../../components/CreateAccountForm';

describe('CreateAccountForm Component', () => {
  it('should display required field errors when submitting an empty form', () => {
    render(<CreateAccountForm />);

    const createAccountButton = screen.getByText('Create Account');
    fireEvent.click(createAccountButton);

    expect(screen.getByText('First Name is required')).toBeInTheDocument();
    expect(screen.getByText('Last Name is required')).toBeInTheDocument();
    expect(screen.getByText('Username is required')).toBeInTheDocument();
    expect(screen.getByText('Password is required')).toBeInTheDocument();
    expect(screen.getByText('Passwords do not match')).toBeInTheDocument();
  });

  it('should clear the errors after filling the required fields', () => {
    render(<CreateAccountForm />);

    const createAccountButton = screen.getByText('Create Account');
    fireEvent.click(createAccountButton);

    expect(screen.getByText('First Name is required')).toBeInTheDocument();
    expect(screen.getByText('Last Name is required')).toBeInTheDocument();
    expect(screen.getByText('Username is required')).toBeInTheDocument();
    expect(screen.getByText('Password is required')).toBeInTheDocument();
    expect(screen.getByText('Passwords do not match')).toBeInTheDocument();

    const firstNameInput = screen.getByLabelText('First Name:');
    const lastNameInput = screen.getByLabelText('Last Name:');
    const usernameInput = screen.getByLabelText('Username:');
    const passwordInput = screen.getByLabelText('Password:');
    const confirmPasswordInput = screen.getByLabelText('Confirm Password:');

    fireEvent.change(firstNameInput, { target: { value: 'John' } });
    fireEvent.change(lastNameInput, { target: { value: 'Doe' } });
    fireEvent.change(usernameInput, { target: { value: 'john_doe' } });
    fireEvent.change(passwordInput, { target: { value: 'password123' } });
    fireEvent.change(confirmPasswordInput, { target: { value: 'password123' } });

    expect(screen.queryByText('First Name is required')).toBeNull();
    expect(screen.queryByText('Last Name is required')).toBeNull();
    expect(screen.queryByText('Username is required')).toBeNull();
    expect(screen.queryByText('Password is required')).toBeNull();
    expect(screen.queryByText('Passwords do not match')).toBeNull();
  });
});
```

In the above tests, we simulate submitting an empty form and check if the required field errors are displayed. After that, we fill in the required fields and ensure that the errors are cleared.

**Expert's Note:** When testing forms, ensure that you cover various validation scenarios, such as empty fields, invalid inputs, and successful submissions. Also, remember to test error handling, edge cases, and possible form interactions to provide comprehensive coverage.

## 8.5 Conclusion

In this chapter, we learned how to test forms and user interactions in a React application. We covered how to simulate user input, button clicks, and form submissions using `@testing-library/react`. Additionally, we implemented best practices for testing form validation and error handling to ensure the robustness of our forms.

Testing forms and user interactions is crucial to validate the functionality and user experience of your application. By following the examples and best practices provided in this chapter, you can create effective and reliable tests for your React forms. Happy testing!