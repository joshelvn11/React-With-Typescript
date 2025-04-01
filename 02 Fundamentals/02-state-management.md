# State Management Patterns

## Introduction

State management is a crucial aspect of React applications. In this section, we'll explore different state management patterns and how to implement them with TypeScript.

## Local State with useState

### 1. Basic State Management

```typescript
interface CounterState {
  count: number;
  isActive: boolean;
}

const Counter: React.FC = () => {
  const [state, setState] = useState<CounterState>({
    count: 0,
    isActive: true,
  });

  const increment = () => {
    setState(prev => ({
      ...prev,
      count: prev.count + 1,
    }));
  };

  const toggleActive = () => {
    setState(prev => ({
      ...prev,
      isActive: !prev.isActive,
    }));
  };

  return (
    <div>
      <h2>Count: {state.count}</h2>
      <p>Status: {state.isActive ? 'Active' : 'Inactive'}</p>
      <button onClick={increment}>Increment</button>
      <button onClick={toggleActive}>Toggle Status</button>
    </div>
  );
};
```

### 2. Complex State Management

```typescript
interface Todo {
  id: string;
  text: string;
  completed: boolean;
  createdAt: Date;
}

interface TodoState {
  todos: Todo[];
  filter: 'all' | 'active' | 'completed';
  searchTerm: string;
}

const TodoList: React.FC = () => {
  const [state, setState] = useState<TodoState>({
    todos: [],
    filter: 'all',
    searchTerm: '',
  });

  const addTodo = (text: string) => {
    const newTodo: Todo = {
      id: Date.now().toString(),
      text,
      completed: false,
      createdAt: new Date(),
    };

    setState(prev => ({
      ...prev,
      todos: [...prev.todos, newTodo],
    }));
  };

  const toggleTodo = (id: string) => {
    setState(prev => ({
      ...prev,
      todos: prev.todos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      ),
    }));
  };

  const filteredTodos = useMemo(() => {
    return state.todos.filter(todo => {
      const matchesFilter =
        state.filter === 'all' ||
        (state.filter === 'active' && !todo.completed) ||
        (state.filter === 'completed' && todo.completed);

      const matchesSearch = todo.text
        .toLowerCase()
        .includes(state.searchTerm.toLowerCase());

      return matchesFilter && matchesSearch;
    });
  }, [state.todos, state.filter, state.searchTerm]);

  return (
    <div>
      <input
        type="text"
        value={state.searchTerm}
        onChange={e =>
          setState(prev => ({ ...prev, searchTerm: e.target.value }))
        }
        placeholder="Search todos..."
      />
      <select
        value={state.filter}
        onChange={e =>
          setState(prev => ({
            ...prev,
            filter: e.target.value as TodoState['filter'],
          }))
        }
      >
        <option value="all">All</option>
        <option value="active">Active</option>
        <option value="completed">Completed</option>
      </select>
      <ul>
        {filteredTodos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span>{todo.text}</span>
          </li>
        ))}
      </ul>
    </div>
  );
};
```

## Custom Hooks for State Management

### 1. Basic Custom Hook

```typescript
interface UseCounterReturn {
  count: number;
  increment: () => void;
  decrement: () => void;
  reset: () => void;
}

const useCounter = (initialValue: number = 0): UseCounterReturn => {
  const [count, setCount] = useState<number>(initialValue);

  const increment = useCallback(() => {
    setCount(prev => prev + 1);
  }, []);

  const decrement = useCallback(() => {
    setCount(prev => prev - 1);
  }, []);

  const reset = useCallback(() => {
    setCount(initialValue);
  }, [initialValue]);

  return { count, increment, decrement, reset };
};

// Usage
const Counter: React.FC = () => {
  const { count, increment, decrement, reset } = useCounter(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
};
```

### 2. Complex Custom Hook

```typescript
interface Todo {
  id: string;
  text: string;
  completed: boolean;
}

interface UseTodosReturn {
  todos: Todo[];
  addTodo: (text: string) => void;
  toggleTodo: (id: string) => void;
  deleteTodo: (id: string) => void;
  filterTodos: (filter: 'all' | 'active' | 'completed') => Todo[];
}

const useTodos = (): UseTodosReturn => {
  const [todos, setTodos] = useState<Todo[]>([]);

  const addTodo = useCallback((text: string) => {
    const newTodo: Todo = {
      id: Date.now().toString(),
      text,
      completed: false,
    };
    setTodos(prev => [...prev, newTodo]);
  }, []);

  const toggleTodo = useCallback((id: string) => {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  }, []);

  const deleteTodo = useCallback((id: string) => {
    setTodos(prev => prev.filter(todo => todo.id !== id));
  }, []);

  const filterTodos = useCallback(
    (filter: 'all' | 'active' | 'completed'): Todo[] => {
      switch (filter) {
        case 'active':
          return todos.filter(todo => !todo.completed);
        case 'completed':
          return todos.filter(todo => todo.completed);
        default:
          return todos;
      }
    },
    [todos]
  );

  return {
    todos,
    addTodo,
    toggleTodo,
    deleteTodo,
    filterTodos,
  };
};
```

## Context API with TypeScript

### 1. Basic Context

```typescript
interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  const toggleTheme = useCallback(() => {
    setTheme(prev => (prev === 'light' ? 'dark' : 'light'));
  }, []);

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

const useTheme = (): ThemeContextType => {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};

// Usage
const ThemeToggle: React.FC = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <button onClick={toggleTheme}>
      Current theme: {theme}
    </button>
  );
};
```

### 2. Complex Context

```typescript
interface User {
  id: string;
  name: string;
  email: string;
}

interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  isLoading: boolean;
}

interface AuthContextType extends AuthState {
  login: (email: string, password: string) => Promise<void>;
  logout: () => Promise<void>;
  register: (userData: Omit<User, 'id'>) => Promise<void>;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [state, setState] = useState<AuthState>({
    user: null,
    isAuthenticated: false,
    isLoading: false,
  });

  const login = useCallback(async (email: string, password: string) => {
    setState(prev => ({ ...prev, isLoading: true }));
    try {
      // API call to login
      const user = await loginAPI(email, password);
      setState({
        user,
        isAuthenticated: true,
        isLoading: false,
      });
    } catch (error) {
      setState(prev => ({ ...prev, isLoading: false }));
      throw error;
    }
  }, []);

  const logout = useCallback(async () => {
    setState(prev => ({ ...prev, isLoading: true }));
    try {
      // API call to logout
      await logoutAPI();
      setState({
        user: null,
        isAuthenticated: false,
        isLoading: false,
      });
    } catch (error) {
      setState(prev => ({ ...prev, isLoading: false }));
      throw error;
    }
  }, []);

  const register = useCallback(async (userData: Omit<User, 'id'>) => {
    setState(prev => ({ ...prev, isLoading: true }));
    try {
      // API call to register
      const user = await registerAPI(userData);
      setState({
        user,
        isAuthenticated: true,
        isLoading: false,
      });
    } catch (error) {
      setState(prev => ({ ...prev, isLoading: false }));
      throw error;
    }
  }, []);

  return (
    <AuthContext.Provider
      value={{
        ...state,
        login,
        logout,
        register,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
};
```

## Practice Exercises

1. Create a custom hook for form management that:
   - Handles form state
   - Provides validation
   - Manages form submission
   - Includes error handling
   - Has proper TypeScript types

2. Implement a shopping cart context that:
   - Manages cart items
   - Handles adding/removing items
   - Calculates totals
   - Persists cart state
   - Has proper TypeScript types

3. Build a theme management system that:
   - Supports multiple themes
   - Handles theme switching
   - Persists theme preference
   - Includes animations
   - Has proper TypeScript types

## Key Takeaways

- useState is the foundation for local state management
- Custom hooks help encapsulate state logic
- Context API provides global state management
- TypeScript ensures type safety in state management
- Proper state organization improves maintainability
- Performance optimization is crucial for complex state

## Next Steps

In the next section, we'll explore lifecycle methods and hooks in detail, including useEffect, useMemo, and useCallback. 