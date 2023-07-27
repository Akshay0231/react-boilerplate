# Chapter 9: Testing Routing and Navigation

## 9.1 Introduction

In this chapter, we will explore how to test routing and navigation in a React application using React Router. Routing allows us to create different views and components based on the current URL, enabling users to navigate through the application seamlessly. We will cover how to test React Router-based navigation, simulate route changes, and test route-specific components. Additionally, we will address route guarding and authentication in tests to ensure proper handling of protected routes.

## 9.2 Example: Setting Up React Router

Let's set up a basic React application with React Router to demonstrate routing and navigation testing:

First, install the required packages:

```bash
npm install react-router-dom @testing-library/react-router-dom
```

Now, let's create a simple React component and set up the routing in `App.js`:

```javascript
// App.js

import React from 'react';
import { BrowserRouter as Router, Route, Link } from 'react-router-dom';
import Home from './components/Home';
import About from './components/About';
import Contact from './components/Contact';

const App = () => {
  return (
    <Router>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
          <li>
            <Link to="/contact">Contact</Link>
          </li>
        </ul>
      </nav>

      <Route exact path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/contact" component={Contact} />
    </Router>
  );
};

export default App;
```

In this example, we have three components: `Home`, `About`, and `Contact`. The navigation links are set up using `Link` components from React Router.

## 9.3 Testing React Router-Based Navigation and Routing

To test React Router-based navigation and routing, we will use `@testing-library/react-router-dom` package. It provides utilities to test navigation and route rendering in a React application.

Let's write tests to ensure the correct rendering of components based on different routes:

```javascript
// __tests__/App.test.js

import React from 'react';
import { render, screen } from '@testing-library/react';
import { BrowserRouter as Router } from 'react-router-dom';
import App from '../App';

describe('App Component', () => {
  it('should render Home component for the default route', () => {
    render(
      <Router>
        <App />
      </Router>
    );

    const homeComponent = screen.getByText('Home Component');
    expect(homeComponent).toBeInTheDocument();
  });

  it('should render About component when navigating to /about route', () => {
    render(
      <Router initialEntries={['/about']}>
        <App />
      </Router>
    );

    const aboutComponent = screen.getByText('About Component');
    expect(aboutComponent).toBeInTheDocument();
  });

  it('should render Contact component when navigating to /contact route', () => {
    render(
      <Router initialEntries={['/contact']}>
        <App />
      </Router>
    );

    const contactComponent = screen.getByText('Contact Component');
    expect(contactComponent).toBeInTheDocument();
  });
});
```

In the above tests, we use the `Router` component from `react-router-dom` and provide `initialEntries` prop to simulate navigating to different routes. We then use `screen.getByText()` to check if the expected components are rendered for each route.

## 9.4 Simulating Route Changes and Testing Route-Specific Components

To test route changes and route-specific components, we can use the `render` function provided by `@testing-library/react` in combination with `MemoryRouter` from `react-router-dom`. The `MemoryRouter` allows us to programmatically change the URL in the test.

Let's write tests to simulate route changes and verify if the correct components are rendered:

```javascript
// __tests__/components/Home.test.js

import React from 'react';
import { render, screen } from '@testing-library/react';
import { MemoryRouter, Route } from 'react-router-dom';
import Home from '../../components/Home';

describe('Home Component', () => {
  it('should render the Home component', () => {
    render(
      <MemoryRouter initialEntries={['/']}>
        <Route path="/" component={Home} />
      </MemoryRouter>
    );

    const homeComponent = screen.getByText('Home Component');
    expect(homeComponent).toBeInTheDocument();
  });
});
```

Similarly, we can write tests for the `About` and `Contact` components using `MemoryRouter` to simulate route changes.

## 9.5 Addressing Route Guarding and Authentication in Tests

When dealing with protected routes and route guarding based on authentication, we need to consider different scenarios in our tests. Route guarding prevents unauthorized users from accessing certain routes, redirecting them to a login page or other appropriate actions.

For testing route guarding, we can create a mock authentication provider and use it to control the user's authentication status. We can then test whether the expected redirection or rendering behavior is triggered based on the user's authentication status.

Let's consider an example of a route guard that restricts access to the `Contact` component based on the user's authentication status:

```javascript
// components/Contact.js

import React from 'react';
import { Redirect } from 'react-router-dom';

const Contact = ({ isAuthenticated }) => {
  if (!isAuthenticated) {
    return <Redirect to="/login" />;
  }

  return <div>Contact Component</div>;
};

export default Contact;
```

Now, let's write tests to verify the behavior of the `Contact` component based on the user's authentication status:

```javascript
// __tests__/components/Contact.test.js

import React from 'react';
import { render, screen } from '@testing-library/react';
import { MemoryRouter, Route } from 'react-router-dom';
import Contact from '../../components/Contact';

describe('Contact Component', () => {
  it('should render the Contact component when user is authenticated', () => {
    render(
      <MemoryRouter initialEntries={['/contact']}>
        <Route
          path="/contact"
          render={(props) => <Contact isAuthenticated={true} {...props} />}
        />
      </MemoryRouter>
    );

    const contactComponent = screen.getByText('Contact Component');
    expect(contactComponent).toBeInTheDocument();
  });

  it('should redirect to

 /login when user is not authenticated', () => {
    render(
      <MemoryRouter initialEntries={['/contact']}>
        <Route
          path="/contact"
          render={(props) => <Contact isAuthenticated={false} {...props} />}
        />
      </MemoryRouter>
    );

    const redirectComponent = screen.queryByText('Contact Component');
    expect(redirectComponent).not.toBeInTheDocument();
    expect(window.location.pathname).toBe('/login');
  });
});
```

In the above tests, we use `MemoryRouter` to simulate route changes to the `Contact` component. We pass the `isAuthenticated` prop to the `Contact` component to control its behavior based on the user's authentication status. We then use `screen.getByText()` and `screen.queryByText()` to check if the expected component or redirection occurs.

**Expert's Note:** When testing routing and navigation, ensure that you cover different routes and components. Also, pay attention to route guarding and authentication scenarios to ensure that protected routes behave as expected for both authenticated and non-authenticated users.

## 9.6 Conclusion

In this chapter, we learned how to test routing and navigation in a React application using React Router. We explored how to simulate route changes, test route-specific components, and address route guarding and authentication in tests. Proper testing of routing and navigation ensures that users can navigate through the application seamlessly and that protected routes are appropriately restricted. By following the examples and best practices provided in this chapter, you can create robust tests for your React applications' routing and navigation. Happy testing!