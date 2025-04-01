# Lifecycle Methods and Hooks

## Introduction

Understanding component lifecycle and hooks is crucial for building efficient React applications. In this section, we'll explore how to use lifecycle methods and hooks effectively with TypeScript.

## useEffect Hook

### 1. Basic Usage

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

const UserProfile: React.FC<{ userId: number }> = ({ userId }) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        setLoading(true);
        const data = await fetchUserData(userId);
        setUser(data);
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Failed to fetch user'));
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]); // Dependency array

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
};
```

### 2. Cleanup Function

```typescript
interface WindowSize {
  width: number;
  height: number;
}

const useWindowSize = (): WindowSize => {
  const [size, setSize] = useState<WindowSize>({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    window.addEventListener('resize', handleResize);

    // Cleanup function
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []); // Empty dependency array

  return size;
};
```

### 3. Multiple Effects

```typescript
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

const TodoList: React.FC = () => {
  const [todos, setTodos] = useState<Todo[]>([]);
  const [filter, setFilter] = useState<'all' | 'active' | 'completed'>('all');

  // Effect for fetching todos
  useEffect(() => {
    const fetchTodos = async () => {
      const data = await fetchTodosFromAPI();
      setTodos(data);
    };
    fetchTodos();
  }, []);

  // Effect for saving todos
  useEffect(() => {
    const saveTodos = async () => {
      await saveTodosToAPI(todos);
    };
    saveTodos();
  }, [todos]);

  // Effect for updating document title
  useEffect(() => {
    const completedCount = todos.filter(todo => todo.completed).length;
    document.title = `Todos (${completedCount} completed)`;
  }, [todos]);

  return (
    <div>
      {/* Component JSX */}
    </div>
  );
};
```

## useMemo Hook

### 1. Basic Usage

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  category: string;
}

const ProductList: React.FC<{ products: Product[] }> = ({ products }) => {
  const [filter, setFilter] = useState<string>('');
  const [sortBy, setSortBy] = useState<'price' | 'name'>('name');

  const filteredAndSortedProducts = useMemo(() => {
    return products
      .filter(product =>
        product.name.toLowerCase().includes(filter.toLowerCase())
      )
      .sort((a, b) => {
        if (sortBy === 'price') {
          return a.price - b.price;
        }
        return a.name.localeCompare(b.name);
      });
  }, [products, filter, sortBy]);

  return (
    <div>
      <input
        type="text"
        value={filter}
        onChange={e => setFilter(e.target.value)}
        placeholder="Filter products..."
      />
      <select value={sortBy} onChange={e => setSortBy(e.target.value as 'price' | 'name')}>
        <option value="name">Sort by name</option>
        <option value="price">Sort by price</option>
      </select>
      <ul>
        {filteredAndSortedProducts.map(product => (
          <li key={product.id}>
            {product.name} - ${product.price}
          </li>
        ))}
      </ul>
    </div>
  );
};
```

### 2. Complex Calculations

```typescript
interface DataPoint {
  timestamp: number;
  value: number;
}

interface ChartData {
  labels: string[];
  values: number[];
  min: number;
  max: number;
  average: number;
}

const useChartData = (data: DataPoint[]): ChartData => {
  return useMemo(() => {
    const labels = data.map(point => 
      new Date(point.timestamp).toLocaleDateString()
    );
    const values = data.map(point => point.value);
    const min = Math.min(...values);
    const max = Math.max(...values);
    const average = values.reduce((a, b) => a + b, 0) / values.length;

    return {
      labels,
      values,
      min,
      max,
      average,
    };
  }, [data]);
};
```

## useCallback Hook

### 1. Basic Usage

```typescript
interface ButtonProps {
  onClick: () => void;
  children: React.ReactNode;
}

const Button: React.FC<ButtonProps> = React.memo(({ onClick, children }) => {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
});

const ParentComponent: React.FC = () => {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    setCount(prev => prev + 1);
  }, []); // Empty dependency array

  return (
    <div>
      <p>Count: {count}</p>
      <Button onClick={handleClick}>Increment</Button>
    </div>
  );
};
```

### 2. Complex Usage

```typescript
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

interface TodoItemProps {
  todo: Todo;
  onToggle: (id: number) => void;
  onDelete: (id: number) => void;
}

const TodoItem: React.FC<TodoItemProps> = React.memo(({
  todo,
  onToggle,
  onDelete,
}) => {
  return (
    <li>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={() => onToggle(todo.id)}
      />
      <span>{todo.text}</span>
      <button onClick={() => onDelete(todo.id)}>Delete</button>
    </li>
  );
});

const TodoList: React.FC = () => {
  const [todos, setTodos] = useState<Todo[]>([]);

  const handleToggle = useCallback((id: number) => {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  }, []);

  const handleDelete = useCallback((id: number) => {
    setTodos(prev => prev.filter(todo => todo.id !== id));
  }, []);

  return (
    <ul>
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={handleToggle}
          onDelete={handleDelete}
        />
      ))}
    </ul>
  );
};
```

## Custom Hooks with Lifecycle

### 1. Data Fetching Hook

```typescript
interface UseFetchResult<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
}

const useFetch = <T>(url: string): UseFetchResult<T> => {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      const response = await fetch(url);
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      const result = await response.json();
      setData(result);
    } catch (err) {
      setError(err instanceof Error ? err : new Error('Failed to fetch data'));
    } finally {
      setLoading(false);
    }
  }, [url]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return { data, loading, error, refetch: fetchData };
};
```

### 2. Local Storage Hook

```typescript
const useLocalStorage = <T>(key: string, initialValue: T) => {
  // Get from local storage then
  // parse stored json or return initialValue
  const readValue = useCallback((): T => {
    if (typeof window === 'undefined') {
      return initialValue;
    }

    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.warn(`Error reading localStorage key "${key}":`, error);
      return initialValue;
    }
  }, [key, initialValue]);

  const [storedValue, setStoredValue] = useState<T>(readValue);

  // Return a wrapped version of useState's setter function that ...
  // ... persists the new value to localStorage.
  const setValue = useCallback((value: T | ((val: T) => T)) => {
    try {
      // Allow value to be a function so we have same API as useState
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      
      // Save state
      setStoredValue(valueToStore);
      
      // Save to local storage
      if (typeof window !== 'undefined') {
        window.localStorage.setItem(key, JSON.stringify(valueToStore));
      }
    } catch (error) {
      console.warn(`Error setting localStorage key "${key}":`, error);
    }
  }, [key, storedValue]);

  useEffect(() => {
    setStoredValue(readValue());
  }, [readValue]);

  return [storedValue, setValue] as const;
};
```

## Practice Exercises

1. Create a custom hook for handling API requests that:
   - Manages loading state
   - Handles errors
   - Supports retries
   - Includes request cancellation
   - Has proper TypeScript types

2. Implement a debounced search hook that:
   - Delays API calls
   - Handles race conditions
   - Supports cancellation
   - Includes proper cleanup
   - Has proper TypeScript types

3. Build a form validation hook that:
   - Handles field validation
   - Manages form state
   - Supports async validation
   - Includes error messages
   - Has proper TypeScript types

## Key Takeaways

- useEffect handles side effects and cleanup
- useMemo optimizes expensive calculations
- useCallback prevents unnecessary re-renders
- Custom hooks encapsulate reusable logic
- Proper dependency arrays are crucial
- Cleanup functions prevent memory leaks
- TypeScript ensures type safety in hooks

## Next Steps

In the next section, we'll explore event handling patterns in React with TypeScript, including form events, custom events, and event delegation. 