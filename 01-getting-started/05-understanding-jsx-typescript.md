# 1.5 Understanding JSX with TypeScript

## What is JSX?

JSX (JavaScript XML) is a syntax extension for JavaScript that allows you to write HTML-like code within your JavaScript/TypeScript files. It's a fundamental part of React development and works seamlessly with TypeScript's type system.

### Basic JSX Syntax

```typescript
// Basic JSX element
const element = <h1>Hello, World!</h1>;

// JSX with attributes
const button = <button className="primary" disabled={true}>Click me</button>;

// JSX with children
const container = (
  <div className="container">
    <h1>Title</h1>
    <p>Content</p>
  </div>
);
```

## TypeScript and JSX

### 1. JSX Types

```typescript
// JSX.Element type
const element: JSX.Element = <div>Hello</div>;

// React.ReactNode type (more flexible)
const node: React.ReactNode = <div>Hello</div>;

// React.ReactElement type (more specific)
const reactElement: React.ReactElement = <div>Hello</div>;
```

### 2. Component Return Types

```typescript
// Explicit return type
const Component: React.FC = () => {
  return <div>Hello</div>;
};

// Implicit return type
const Component = () => {
  return <div>Hello</div>;
};

// With props
interface Props {
  name: string;
}

const Component: React.FC<Props> = ({ name }) => {
  return <div>Hello, {name}!</div>;
};
```

## JSX Expressions

### 1. Embedding Expressions

```typescript
const name = "John";
const age = 30;

const element = (
  <div>
    <h1>User Information</h1>
    <p>Name: {name}</p>
    <p>Age: {age}</p>
    <p>Is adult: {age >= 18 ? 'Yes' : 'No'}</p>
  </div>
);
```

### 2. Conditional Rendering

```typescript
interface User {
  name: string;
  isLoggedIn: boolean;
}

const UserGreeting: React.FC<{ user: User }> = ({ user }) => {
  return (
    <div>
      {user.isLoggedIn ? (
        <h1>Welcome back, {user.name}!</h1>
      ) : (
        <h1>Please log in</h1>
      )}
    </div>
  );
};
```

### 3. List Rendering

```typescript
interface Item {
  id: number;
  text: string;
}

const List: React.FC<{ items: Item[] }> = ({ items }) => {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.text}</li>
      ))}
    </ul>
  );
};
```

## JSX Attributes and Props

### 1. HTML Attributes

```typescript
// Basic HTML attributes
const element = (
  <div
    className="container"
    id="main"
    style={{ backgroundColor: 'blue' }}
  >
    Content
  </div>
);

// Event handlers
const Button: React.FC = () => {
  const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    console.log('Button clicked!');
  };

  return <button onClick={handleClick}>Click me</button>;
};
```

### 2. Custom Props

```typescript
interface CardProps {
  title: string;
  content: string;
  onClick?: () => void;
}

const Card: React.FC<CardProps> = ({ title, content, onClick }) => {
  return (
    <div className="card" onClick={onClick}>
      <h2>{title}</h2>
      <p>{content}</p>
    </div>
  );
};
```

## Advanced JSX Patterns

### 1. Fragment Syntax

```typescript
// Using Fragment
const Component: React.FC = () => {
  return (
    <>
      <h1>Title</h1>
      <p>Content</p>
    </>
  );
};

// With key prop
const List: React.FC = () => {
  return (
    <React.Fragment key="list">
      <li>Item 1</li>
      <li>Item 2</li>
    </React.Fragment>
  );
};
```

### 2. Portals

```typescript
import { createPortal } from 'react-dom';

const Modal: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  return createPortal(
    <div className="modal">
      {children}
    </div>,
    document.body
  );
};
```

### 3. Children Prop

```typescript
interface ContainerProps {
  children: React.ReactNode;
}

const Container: React.FC<ContainerProps> = ({ children }) => {
  return (
    <div className="container">
      {children}
    </div>
  );
};

// Usage
const App: React.FC = () => {
  return (
    <Container>
      <h1>Title</h1>
      <p>Content</p>
    </Container>
  );
};
```

## Type-Safe Event Handling

### 1. Event Types

```typescript
const Form: React.FC = () => {
  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    // Handle form submission
  };

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const value = event.target.value;
    // Handle input change
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
};
```

### 2. Generic Event Handlers

```typescript
const handleInputChange = <T extends HTMLElement>(
  event: React.ChangeEvent<T>
) => {
  const value = (event.target as HTMLInputElement).value;
  // Handle input change
};
```

## Practice Exercise

Create a type-safe form component with the following requirements:

1. Create a form with name, email, and age fields
2. Implement proper type checking for all inputs
3. Handle form submission with type-safe event handling
4. Display validation errors
5. Show success message on valid submission

```typescript
// src/components/TypeSafeForm.tsx
import React, { useState } from 'react';

interface FormData {
  name: string;
  email: string;
  age: number;
}

interface ValidationErrors {
  name?: string;
  email?: string;
  age?: string;
}

const TypeSafeForm: React.FC = () => {
  const [formData, setFormData] = useState<FormData>({
    name: '',
    email: '',
    age: 0
  });

  const [errors, setErrors] = useState<ValidationErrors>({});
  const [isSubmitted, setIsSubmitted] = useState(false);

  const validateForm = (data: FormData): ValidationErrors => {
    const newErrors: ValidationErrors = {};
    
    if (!data.name) {
      newErrors.name = 'Name is required';
    }

    if (!data.email) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(data.email)) {
      newErrors.email = 'Email is invalid';
    }

    if (data.age < 0 || data.age > 120) {
      newErrors.age = 'Age must be between 0 and 120';
    }

    return newErrors;
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    const newErrors = validateForm(formData);
    
    if (Object.keys(newErrors).length === 0) {
      setIsSubmitted(true);
      setErrors({});
    } else {
      setErrors(newErrors);
    }
  };

  const handleChange = (
    event: React.ChangeEvent<HTMLInputElement>
  ) => {
    const { name, value } = event.target;
    setFormData(prev => ({
      ...prev,
      [name]: name === 'age' ? Number(value) : value
    }));
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="name">Name:</label>
        <input
          type="text"
          id="name"
          name="name"
          value={formData.name}
          onChange={handleChange}
        />
        {errors.name && <span className="error">{errors.name}</span>}
      </div>

      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>

      <div>
        <label htmlFor="age">Age:</label>
        <input
          type="number"
          id="age"
          name="age"
          value={formData.age}
          onChange={handleChange}
        />
        {errors.age && <span className="error">{errors.age}</span>}
      </div>

      <button type="submit">Submit</button>

      {isSubmitted && (
        <div className="success">
          Form submitted successfully!
        </div>
      )}
    </form>
  );
};

export default TypeSafeForm;
```

## Key Takeaways

- JSX is a syntax extension that allows writing HTML-like code in JavaScript/TypeScript
- TypeScript provides type safety for JSX elements and components
- React.FC is a type for functional components
- Event handling in React with TypeScript requires proper event types
- JSX expressions can contain any valid JavaScript/TypeScript expressions
- TypeScript helps catch common JSX-related errors at compile time

## Common Pitfalls to Avoid

1. **Type Definition Issues**
   - Forgetting to define prop types
   - Using incorrect event types
   - Missing type annotations for state

2. **JSX Syntax**
   - Using self-closing tags incorrectly
   - Forgetting to close all tags
   - Using HTML attributes instead of React props

3. **Event Handling**
   - Not properly typing event handlers
   - Forgetting to prevent default form submission
   - Incorrect event type usage

## Further Reading

- [React JSX Documentation](https://reactjs.org/docs/introducing-jsx.html)
- [TypeScript JSX Documentation](https://www.typescriptlang.org/docs/handbook/jsx.html)
- [React TypeScript Cheatsheet](https://github.com/typescript-cheatsheets/react) 