# Components and Props in Detail

## Introduction

Components are the building blocks of React applications. In this section, we'll explore how to create and use components with TypeScript, focusing on props typing and component patterns.

## Component Types

### 1. Function Components

Function components are the modern way to write React components. Here's how to properly type them:

```typescript
// Basic function component
const Greeting: React.FC<{ name: string }> = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};

// With children prop
const Card: React.FC<{ title: string; children: React.ReactNode }> = ({
  title,
  children,
}) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      {children}
    </div>
  );
};

// With optional props
interface UserCardProps {
  name: string;
  email: string;
  avatar?: string; // Optional prop
  role?: 'admin' | 'user' | 'guest'; // Optional prop with specific values
}

const UserCard: React.FC<UserCardProps> = ({
  name,
  email,
  avatar,
  role = 'user', // Default value
}) => {
  return (
    <div className="user-card">
      {avatar && <img src={avatar} alt={name} />}
      <h3>{name}</h3>
      <p>{email}</p>
      <span className="role">{role}</span>
    </div>
  );
};
```

### 2. Class Components

While function components are preferred, it's important to understand class components, especially when working with legacy code:

```typescript
interface CounterProps {
  initialValue?: number;
}

interface CounterState {
  count: number;
}

class Counter extends React.Component<CounterProps, CounterState> {
  constructor(props: CounterProps) {
    super(props);
    this.state = {
      count: props.initialValue || 0,
    };
  }

  increment = () => {
    this.setState(prevState => ({
      count: prevState.count + 1,
    }));
  };

  render() {
    return (
      <div>
        <h2>Count: {this.state.count}</h2>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

## Advanced Props Patterns

### 1. Polymorphic Components

Polymorphic components can render as different HTML elements while maintaining type safety:

```typescript
interface PolymorphicProps<T extends keyof JSX.IntrinsicElements> {
  as?: T;
  children: React.ReactNode;
}

const PolymorphicComponent = <T extends keyof JSX.IntrinsicElements>({
  as: Component = 'div' as T,
  children,
  ...props
}: PolymorphicProps<T> & JSX.IntrinsicElements[T]) => {
  return <Component {...props}>{children}</Component>;
};

// Usage
<PolymorphicComponent as="button">Click me</PolymorphicComponent>
<PolymorphicComponent as="h1">Title</PolymorphicComponent>
```

### 2. Generic Components

Generic components can work with different data types while maintaining type safety:

```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

const List = <T extends unknown>({ items, renderItem }: ListProps<T>) => {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
};

// Usage with different types
interface User {
  id: number;
  name: string;
}

interface Product {
  id: string;
  title: string;
  price: number;
}

const UserList: React.FC = () => {
  const users: User[] = [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' },
  ];

  return (
    <List
      items={users}
      renderItem={user => <span>{user.name}</span>}
    />
  );
};

const ProductList: React.FC = () => {
  const products: Product[] = [
    { id: '1', title: 'Product 1', price: 100 },
    { id: '2', title: 'Product 2', price: 200 },
  ];

  return (
    <List
      items={products}
      renderItem={product => (
        <span>{product.title} - ${product.price}</span>
      )}
    />
  );
};
```

### 3. Component Composition

Component composition is a powerful pattern for building complex UIs:

```typescript
interface CardProps {
  children: React.ReactNode;
}

const Card: React.FC<CardProps> = ({ children }) => {
  return <div className="card">{children}</div>;
};

interface CardHeaderProps {
  children: React.ReactNode;
}

const CardHeader: React.FC<CardHeaderProps> = ({ children }) => {
  return <div className="card-header">{children}</div>;
};

interface CardBodyProps {
  children: React.ReactNode;
}

const CardBody: React.FC<CardBodyProps> = ({ children }) => {
  return <div className="card-body">{children}</div>;
};

// Usage
const UserProfile: React.FC = () => {
  return (
    <Card>
      <CardHeader>
        <h2>User Profile</h2>
      </CardHeader>
      <CardBody>
        <p>Name: John Doe</p>
        <p>Email: john@example.com</p>
      </CardBody>
    </Card>
  );
};
```

## Props Best Practices

### 1. Destructuring Props

```typescript
// Instead of this
const UserProfile: React.FC<UserProfileProps> = (props) => {
  return (
    <div>
      <h1>{props.name}</h1>
      <p>{props.email}</p>
    </div>
  );
};

// Do this
const UserProfile: React.FC<UserProfileProps> = ({ name, email }) => {
  return (
    <div>
      <h1>{name}</h1>
      <p>{email}</p>
    </div>
  );
};
```

### 2. Default Props

```typescript
interface ButtonProps {
  variant?: 'primary' | 'secondary';
  size?: 'small' | 'medium' | 'large';
  disabled?: boolean;
}

const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'medium',
  disabled = false,
  children,
}) => {
  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

### 3. Prop Types Documentation

```typescript
interface UserCardProps {
  /** The user's full name */
  name: string;
  /** The user's email address */
  email: string;
  /** The user's profile picture URL */
  avatar?: string;
  /** The user's role in the system */
  role?: 'admin' | 'user' | 'guest';
  /** Callback function when the card is clicked */
  onClick?: (userId: string) => void;
}

const UserCard: React.FC<UserCardProps> = ({
  name,
  email,
  avatar,
  role = 'user',
  onClick,
}) => {
  // Component implementation
};
```

## Practice Exercises

1. Create a polymorphic `Container` component that:
   - Can render as different HTML elements
   - Accepts different types of content
   - Has proper TypeScript types
   - Includes proper prop spreading

2. Build a generic `DataList` component that:
   - Can display different types of data
   - Supports custom rendering of items
   - Includes sorting and filtering
   - Has proper TypeScript types

3. Implement a `Form` component system with:
   - Type-safe form fields
   - Validation support
   - Error handling
   - Proper TypeScript types

## Key Takeaways

- Function components are the preferred way to write React components
- TypeScript provides powerful type checking for props
- Polymorphic and generic components offer flexibility with type safety
- Component composition is key to building complex UIs
- Proper prop typing improves code maintainability
- Documentation and default props enhance component usability

## Next Steps

In the next section, we'll explore state management patterns in React with TypeScript, including hooks and state management libraries. 