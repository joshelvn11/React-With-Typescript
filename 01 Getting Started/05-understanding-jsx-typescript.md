# Understanding JSX with TypeScript

## Introduction

JSX (JavaScript XML) is a syntax extension for JavaScript that allows you to write HTML-like code within your JavaScript/TypeScript files. When combined with TypeScript, JSX provides powerful type checking and better developer experience. Let's explore how JSX works with TypeScript.

## JSX Basics with TypeScript

### 1. Basic JSX Syntax

```typescript
// Basic JSX element
const element: JSX.Element = <h1>Hello, World!</h1>;

// JSX with attributes
const button: JSX.Element = (
  <button 
    className="primary"
    onClick={() => console.log('clicked')}
    disabled={false}
  >
    Click me
  </button>
);

// JSX with children
const container: JSX.Element = (
  <div className="container">
    <h1>Title</h1>
    <p>Content</p>
  </div>
);
```

### 2. TypeScript Types for JSX

```typescript
// React.FC (Function Component) type
const Greeting: React.FC<{ name: string }> = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};

// React.ReactElement type
const element: React.ReactElement = <div>Hello</div>;

// React.ReactNode type (more flexible)
const node: React.ReactNode = (
  <div>
    <h1>Title</h1>
    {null}
    {undefined}
    {true}
    {false}
    {123}
    {'string'}
  </div>
);
```

## Advanced JSX Patterns

### 1. Conditional Rendering

```typescript
interface User {
  name: string;
  isLoggedIn: boolean;
}

const UserGreeting: React.FC<{ user: User }> = ({ user }) => {
  // Using ternary operator
  return user.isLoggedIn ? (
    <h1>Welcome back, {user.name}!</h1>
  ) : (
    <h1>Please log in</h1>
  );

  // Using logical AND operator
  return user.isLoggedIn && <h1>Welcome back, {user.name}!</h1>;

  // Using if statement
  if (user.isLoggedIn) {
    return <h1>Welcome back, {user.name}!</h1>;
  }
  return <h1>Please log in</h1>;
};
```

### 2. Lists and Keys

```typescript
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

const TodoList: React.FC<{ todos: Todo[] }> = ({ todos }) => {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => console.log(todo.id)}
          />
          <span>{todo.text}</span>
        </li>
      ))}
    </ul>
  );
};
```

### 3. Event Handling

```typescript
interface ButtonProps {
  onClick: (event: React.MouseEvent<HTMLButtonElement>) => void;
  children: React.ReactNode;
}

const Button: React.FC<ButtonProps> = ({ onClick, children }) => {
  return (
    <button
      onClick={(e: React.MouseEvent<HTMLButtonElement>) => {
        e.preventDefault();
        onClick(e);
      }}
    >
      {children}
    </button>
  );
};

// Usage with different event types
const Form: React.FC = () => {
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    // Handle form submission
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} />
      <Button onClick={() => console.log('clicked')}>Submit</Button>
    </form>
  );
};
```

### 4. Ref Handling

```typescript
const TextInput: React.FC = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
};
```

### 5. Portals

```typescript
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  children: React.ReactNode;
}

const Modal: React.FC<ModalProps> = ({ isOpen, onClose, children }) => {
  if (!isOpen) return null;

  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        {children}
      </div>
    </div>,
    document.body
  );
};
```

### 6. Fragments

```typescript
const Table: React.FC = () => {
  return (
    <>
      <tr>
        <td>First</td>
        <td>Second</td>
      </tr>
      <tr>
        <td>Third</td>
        <td>Fourth</td>
      </tr>
    </>
  );
};
```

## Type-Safe JSX Patterns

### 1. Polymorphic Components

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

// Usage
interface User {
  id: number;
  name: string;
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
```

## Practice Exercises

1. Create a type-safe form component that:
   - Handles different input types (text, number, checkbox, etc.)
   - Provides proper TypeScript types for all props
   - Includes validation
   - Handles form submission with proper typing

2. Build a polymorphic card component that:
   - Can render as different HTML elements
   - Accepts different types of content
   - Has proper TypeScript types for all variations
   - Includes proper prop spreading

3. Implement a type-safe data table component that:
   - Handles different data types
   - Supports sorting and filtering
   - Includes pagination
   - Has proper TypeScript types for all features

## Key Takeaways

- JSX is a syntax extension that allows writing HTML-like code in TypeScript
- TypeScript provides type safety for JSX elements and props
- React.FC and other React types help ensure type safety
- Event handling in JSX requires proper TypeScript types
- Polymorphic and generic components provide flexible, type-safe patterns
- Proper typing helps catch errors early and improves maintainability

## Next Steps

In the next section, we'll summarize the key concepts covered in this chapter and provide exercises to reinforce your understanding. 