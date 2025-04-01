# 2.1 Components and Props

## Understanding React Components with TypeScript

### 1. Component Types

```typescript
// Functional Component with explicit return type
const Component: React.FC = () => {
  return <div>Hello World</div>;
};

// Functional Component with implicit return type
const Component = () => {
  return <div>Hello World</div>;
};

// Component with props
interface Props {
  name: string;
  age?: number; // Optional prop
}

const Component: React.FC<Props> = ({ name, age }) => {
  return (
    <div>
      <h1>Hello, {name}</h1>
      {age && <p>Age: {age}</p>}
    </div>
  );
};
```

### 2. Component Return Types

```typescript
// JSX.Element return type
const Component = (): JSX.Element => {
  return <div>Hello</div>;
};

// React.ReactNode return type (more flexible)
const Component = (): React.ReactNode => {
  return <div>Hello</div>;
};

// React.ReactElement return type (more specific)
const Component = (): React.ReactElement => {
  return <div>Hello</div>;
};
```

## Props in TypeScript

### 1. Basic Props

```typescript
interface UserProps {
  name: string;
  email: string;
  role: 'admin' | 'user' | 'guest';
}

const User: React.FC<UserProps> = ({ name, email, role }) => {
  return (
    <div>
      <h2>{name}</h2>
      <p>{email}</p>
      <span>{role}</span>
    </div>
  );
};
```

### 2. Optional Props

```typescript
interface CardProps {
  title: string;
  description?: string; // Optional prop
  imageUrl?: string;    // Optional prop
}

const Card: React.FC<CardProps> = ({ title, description, imageUrl }) => {
  return (
    <div className="card">
      {imageUrl && <img src={imageUrl} alt={title} />}
      <h3>{title}</h3>
      {description && <p>{description}</p>}
    </div>
  );
};
```

### 3. Default Props

```typescript
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'outline';
  size?: 'small' | 'medium' | 'large';
  disabled?: boolean;
}

const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'medium',
  disabled = false,
  children
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

### 4. Children Props

```typescript
interface LayoutProps {
  children: React.ReactNode;
  header?: React.ReactNode;
  footer?: React.ReactNode;
}

const Layout: React.FC<LayoutProps> = ({ children, header, footer }) => {
  return (
    <div className="layout">
      {header && <header>{header}</header>}
      <main>{children}</main>
      {footer && <footer>{footer}</footer>}
    </div>
  );
};
```

## Advanced Props Patterns

### 1. Generic Components

```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

const List = <T extends {}>({ items, renderItem }: ListProps<T>) => {
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
    { id: 2, name: 'Jane' }
  ];

  return (
    <List
      items={users}
      renderItem={(user) => <span>{user.name}</span>}
    />
  );
};
```

### 2. Component Props with Events

```typescript
interface FormProps {
  onSubmit: (data: FormData) => void;
  onCancel: () => void;
  initialValues?: Partial<FormData>;
}

interface FormData {
  username: string;
  email: string;
  password: string;
}

const Form: React.FC<FormProps> = ({
  onSubmit,
  onCancel,
  initialValues = {}
}) => {
  const [formData, setFormData] = useState<FormData>({
    username: '',
    email: '',
    password: '',
    ...initialValues
  });

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSubmit(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
      <button type="submit">Submit</button>
      <button type="button" onClick={onCancel}>Cancel</button>
    </form>
  );
};
```

### 3. Higher-Order Components with TypeScript

```typescript
interface WithLoadingProps {
  isLoading: boolean;
}

const withLoading = <P extends object>(
  WrappedComponent: React.ComponentType<P>
) => {
  return function WithLoadingComponent(props: P & WithLoadingProps) {
    const { isLoading, ...rest } = props;

    if (isLoading) {
      return <div>Loading...</div>;
    }

    return <WrappedComponent {...(rest as P)} />;
  };
};

// Usage
interface UserProfileProps {
  name: string;
  email: string;
}

const UserProfile: React.FC<UserProfileProps> = ({ name, email }) => (
  <div>
    <h2>{name}</h2>
    <p>{email}</p>
  </div>
);

const UserProfileWithLoading = withLoading(UserProfile);
```

## Best Practices

### 1. Prop Types Organization

```typescript
// Separate types file
// types.ts
export interface User {
  id: number;
  name: string;
  email: string;
}

export interface UserCardProps {
  user: User;
  onEdit?: (user: User) => void;
  onDelete?: (userId: number) => void;
}

// Component file
// UserCard.tsx
import { UserCardProps } from './types';

const UserCard: React.FC<UserCardProps> = ({ user, onEdit, onDelete }) => {
  // Component implementation
};
```

### 2. Component Composition

```typescript
interface CardProps {
  children: React.ReactNode;
  className?: string;
}

const Card: React.FC<CardProps> = ({ children, className }) => (
  <div className={`card ${className || ''}`}>{children}</div>
);

interface CardHeaderProps {
  title: string;
  subtitle?: string;
}

const CardHeader: React.FC<CardHeaderProps> = ({ title, subtitle }) => (
  <div className="card-header">
    <h3>{title}</h3>
    {subtitle && <p>{subtitle}</p>}
  </div>
);

// Usage
const UserCard: React.FC<UserCardProps> = ({ user }) => (
  <Card>
    <CardHeader title={user.name} subtitle={user.email} />
    {/* Other card content */}
  </Card>
);
```

## Common Pitfalls and Solutions

### 1. Type Inference Issues

```typescript
// Problem: Type inference might be too strict
const Component = ({ data }) => {
  return <div>{data.name}</div>; // Error: data is any
};

// Solution: Explicitly type the props
interface Props {
  data: {
    name: string;
    age: number;
  };
}

const Component: React.FC<Props> = ({ data }) => {
  return <div>{data.name}</div>;
};
```

### 2. Optional Props Handling

```typescript
// Problem: Optional props might be undefined
const Component: React.FC<{ name?: string }> = ({ name }) => {
  return <div>{name.toUpperCase()}</div>; // Error: name might be undefined
};

// Solution: Add null check or default value
const Component: React.FC<{ name?: string }> = ({ name = 'Guest' }) => {
  return <div>{name.toUpperCase()}</div>;
};
```

### 3. Event Handler Types

```typescript
// Problem: Incorrect event handler types
const handleClick = (e) => {
  console.log(e.target.value); // Error: e is any
};

// Solution: Use proper event types
const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
  console.log(e.currentTarget.value);
};
```

## Practice Exercise

Create a reusable `DataTable` component with the following requirements:

1. Support generic data types
2. Include sorting functionality
3. Support pagination
4. Allow custom column rendering
5. Include loading state
6. Support row selection

```typescript
interface Column<T> {
  key: keyof T;
  header: string;
  render?: (value: T[keyof T], row: T) => React.ReactNode;
}

interface DataTableProps<T> {
  data: T[];
  columns: Column<T>[];
  onSort?: (key: keyof T, direction: 'asc' | 'desc') => void;
  onSelect?: (rows: T[]) => void;
  isLoading?: boolean;
  pageSize?: number;
}

const DataTable = <T extends { id: string | number }>({
  data,
  columns,
  onSort,
  onSelect,
  isLoading = false,
  pageSize = 10
}: DataTableProps<T>) => {
  const [currentPage, setCurrentPage] = useState(1);
  const [selectedRows, setSelectedRows] = useState<T[]>([]);
  const [sortConfig, setSortConfig] = useState<{
    key: keyof T;
    direction: 'asc' | 'desc';
  } | null>(null);

  const handleSort = (key: keyof T) => {
    const direction = 
      sortConfig?.key === key && sortConfig.direction === 'asc' 
        ? 'desc' 
        : 'asc';
    
    setSortConfig({ key, direction });
    onSort?.(key, direction);
  };

  const handleSelect = (row: T) => {
    const newSelected = selectedRows.includes(row)
      ? selectedRows.filter(r => r.id !== row.id)
      : [...selectedRows, row];
    
    setSelectedRows(newSelected);
    onSelect?.(newSelected);
  };

  const paginatedData = data.slice(
    (currentPage - 1) * pageSize,
    currentPage * pageSize
  );

  return (
    <div className="data-table">
      <table>
        <thead>
          <tr>
            <th>
              <input
                type="checkbox"
                checked={selectedRows.length === data.length}
                onChange={() => {
                  const newSelected = selectedRows.length === data.length
                    ? []
                    : data;
                  setSelectedRows(newSelected);
                  onSelect?.(newSelected);
                }}
              />
            </th>
            {columns.map(column => (
              <th
                key={String(column.key)}
                onClick={() => handleSort(column.key)}
              >
                {column.header}
                {sortConfig?.key === column.key && (
                  <span>{sortConfig.direction === 'asc' ? '↑' : '↓'}</span>
                )}
              </th>
            ))}
          </tr>
        </thead>
        <tbody>
          {isLoading ? (
            <tr>
              <td colSpan={columns.length + 1}>Loading...</td>
            </tr>
          ) : (
            paginatedData.map(row => (
              <tr key={row.id}>
                <td>
                  <input
                    type="checkbox"
                    checked={selectedRows.includes(row)}
                    onChange={() => handleSelect(row)}
                  />
                </td>
                {columns.map(column => (
                  <td key={String(column.key)}>
                    {column.render
                      ? column.render(row[column.key], row)
                      : String(row[column.key])}
                  </td>
                ))}
              </tr>
            ))
          )}
        </tbody>
      </table>
      
      <div className="pagination">
        <button
          disabled={currentPage === 1}
          onClick={() => setCurrentPage(p => p - 1)}
        >
          Previous
        </button>
        <span>Page {currentPage}</span>
        <button
          disabled={currentPage * pageSize >= data.length}
          onClick={() => setCurrentPage(p => p + 1)}
        >
          Next
        </button>
      </div>
    </div>
  );
};
```

## Key Takeaways

1. TypeScript provides strong type checking for React components and props
2. Use interfaces to define prop types
3. Leverage generics for reusable components
4. Handle optional props and default values properly
5. Use proper event types for event handlers
6. Consider component composition for complex UIs

## Common Pitfalls to Avoid

1. **Type Definition Issues**
   - Forgetting to define prop types
   - Using `any` type unnecessarily
   - Incorrect event handler types

2. **Optional Props**
   - Not handling undefined cases
   - Missing default values
   - Incorrect null checks

3. **Component Structure**
   - Overly complex components
   - Poor prop organization
   - Missing type exports

## Further Reading

- [React Component Patterns](https://reactpatterns.com/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [React TypeScript Cheatsheet](https://github.com/typescript-cheatsheets/react) 