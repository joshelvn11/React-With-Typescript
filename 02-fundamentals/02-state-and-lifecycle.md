# 2.2 State and Lifecycle

## Understanding State in React with TypeScript

### 1. Basic State Management

```typescript
// Simple state with useState
const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};

// State with complex types
interface User {
  id: number;
  name: string;
  email: string;
}

const UserProfile: React.FC = () => {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState<boolean>(true);

  return (
    <div>
      {isLoading ? (
        <p>Loading...</p>
      ) : user ? (
        <div>
          <h2>{user.name}</h2>
          <p>{user.email}</p>
        </div>
      ) : (
        <p>No user found</p>
      )}
    </div>
  );
};
```

### 2. State Updates

```typescript
// Functional updates
const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  const increment = () => {
    setCount(prevCount => prevCount + 1);
  };

  const decrement = () => {
    setCount(prevCount => prevCount - 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  );
};

// Multiple state updates
interface FormState {
  username: string;
  email: string;
  password: string;
}

const RegistrationForm: React.FC = () => {
  const [formData, setFormData] = useState<FormState>({
    username: '',
    email: '',
    password: ''
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  return (
    <form>
      <input
        type="text"
        name="username"
        value={formData.username}
        onChange={handleChange}
      />
      {/* Other form fields */}
    </form>
  );
};
```

## Component Lifecycle with TypeScript

### 1. useEffect Hook

```typescript
// Basic effect
const DataFetcher: React.FC = () => {
  const [data, setData] = useState<any[]>([]);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('https://api.example.com/data');
        const json = await response.json();
        setData(json);
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Failed to fetch'));
      }
    };

    fetchData();
  }, []); // Empty dependency array

  if (error) return <div>Error: {error.message}</div>;
  return <div>{/* Render data */}</div>;
};

// Effect with cleanup
const Timer: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount(prev => prev + 1);
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  return <div>Count: {count}</div>;
};
```

### 2. Effect Dependencies

```typescript
interface User {
  id: number;
  name: string;
}

const UserProfile: React.FC<{ userId: number }> = ({ userId }) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState<boolean>(true);

  useEffect(() => {
    const fetchUser = async () => {
      setLoading(true);
      try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        setUser(data);
      } catch (error) {
        console.error('Error fetching user:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]); // Re-run when userId changes

  if (loading) return <div>Loading...</div>;
  if (!user) return <div>User not found</div>;

  return <div>Welcome, {user.name}!</div>;
};
```

## Advanced State Management Patterns

### 1. Reducer Pattern

```typescript
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

type TodoAction =
  | { type: 'ADD_TODO'; payload: string }
  | { type: 'TOGGLE_TODO'; payload: number }
  | { type: 'DELETE_TODO'; payload: number };

interface TodoState {
  todos: Todo[];
}

const todoReducer = (state: TodoState, action: TodoAction): TodoState => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            id: Date.now(),
            text: action.payload,
            completed: false
          }
        ]
      };
    case 'TOGGLE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        )
      };
    case 'DELETE_TODO':
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload)
      };
    default:
      return state;
  }
};

const TodoList: React.FC = () => {
  const [state, dispatch] = useReducer(todoReducer, { todos: [] });
  const [input, setInput] = useState<string>('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (input.trim()) {
      dispatch({ type: 'ADD_TODO', payload: input });
      setInput('');
    }
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          value={input}
          onChange={e => setInput(e.target.value)}
        />
        <button type="submit">Add Todo</button>
      </form>
      <ul>
        {state.todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => dispatch({ type: 'TOGGLE_TODO', payload: todo.id })}
            />
            <span style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}>
              {todo.text}
            </span>
            <button onClick={() => dispatch({ type: 'DELETE_TODO', payload: todo.id })}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
};
```

### 2. Context API with TypeScript

```typescript
interface Theme {
  primary: string;
  secondary: string;
  background: string;
}

interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<Theme>({
    primary: '#007bff',
    secondary: '#6c757d',
    background: '#ffffff'
  });

  const toggleTheme = () => {
    setTheme(prev => ({
      ...prev,
      background: prev.background === '#ffffff' ? '#000000' : '#ffffff'
    }));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

const ThemedButton: React.FC = () => {
  const context = useContext(ThemeContext);
  
  if (!context) {
    throw new Error('ThemedButton must be used within a ThemeProvider');
  }

  const { theme, toggleTheme } = context;

  return (
    <button
      style={{ backgroundColor: theme.primary, color: theme.background }}
      onClick={toggleTheme}
    >
      Toggle Theme
    </button>
  );
};
```

## Performance Optimization

### 1. Memoization

```typescript
interface ExpensiveComponentProps {
  data: number[];
  multiplier: number;
}

const ExpensiveComponent: React.FC<ExpensiveComponentProps> = ({ data, multiplier }) => {
  const result = useMemo(() => {
    console.log('Computing expensive calculation');
    return data.reduce((acc, curr) => acc + curr, 0) * multiplier;
  }, [data, multiplier]);

  return <div>Result: {result}</div>;
};

const ParentComponent: React.FC = () => {
  const [data] = useState<number[]>([1, 2, 3, 4, 5]);
  const [multiplier, setMultiplier] = useState<number>(1);

  return (
    <div>
      <ExpensiveComponent data={data} multiplier={multiplier} />
      <button onClick={() => setMultiplier(prev => prev + 1)}>
        Increase Multiplier
      </button>
    </div>
  );
};
```

### 2. Callback Memoization

```typescript
interface ButtonProps {
  onClick: () => void;
  label: string;
}

const Button: React.FC<ButtonProps> = React.memo(({ onClick, label }) => {
  console.log('Button rendered');
  return <button onClick={onClick}>{label}</button>;
});

const ParentComponent: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  const handleClick = useCallback(() => {
    setCount(prev => prev + 1);
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <Button onClick={handleClick} label="Increment" />
    </div>
  );
};
```

## Practice Exercise

Create a `TaskManager` component with the following requirements:

1. Implement task creation, editing, and deletion
2. Use reducer pattern for state management
3. Add task filtering and sorting
4. Implement task categories
5. Add due dates and priority levels
6. Include task completion status

```typescript
interface Task {
  id: string;
  title: string;
  description: string;
  category: string;
  dueDate: Date;
  priority: 'low' | 'medium' | 'high';
  completed: boolean;
}

type TaskAction =
  | { type: 'ADD_TASK'; payload: Omit<Task, 'id'> }
  | { type: 'UPDATE_TASK'; payload: Task }
  | { type: 'DELETE_TASK'; payload: string }
  | { type: 'TOGGLE_TASK'; payload: string }
  | { type: 'SET_FILTER'; payload: string }
  | { type: 'SET_SORT'; payload: 'priority' | 'dueDate' | 'title' };

interface TaskState {
  tasks: Task[];
  filter: string;
  sortBy: 'priority' | 'dueDate' | 'title';
}

const taskReducer = (state: TaskState, action: TaskAction): TaskState => {
  switch (action.type) {
    case 'ADD_TASK':
      return {
        ...state,
        tasks: [
          ...state.tasks,
          {
            ...action.payload,
            id: crypto.randomUUID()
          }
        ]
      };
    case 'UPDATE_TASK':
      return {
        ...state,
        tasks: state.tasks.map(task =>
          task.id === action.payload.id ? action.payload : task
        )
      };
    case 'DELETE_TASK':
      return {
        ...state,
        tasks: state.tasks.filter(task => task.id !== action.payload)
      };
    case 'TOGGLE_TASK':
      return {
        ...state,
        tasks: state.tasks.map(task =>
          task.id === action.payload
            ? { ...task, completed: !task.completed }
            : task
        )
      };
    case 'SET_FILTER':
      return { ...state, filter: action.payload };
    case 'SET_SORT':
      return { ...state, sortBy: action.payload };
    default:
      return state;
  }
};

const TaskManager: React.FC = () => {
  const [state, dispatch] = useReducer(taskReducer, {
    tasks: [],
    filter: 'all',
    sortBy: 'priority'
  });

  const [newTask, setNewTask] = useState<Omit<Task, 'id'>>({
    title: '',
    description: '',
    category: '',
    dueDate: new Date(),
    priority: 'medium',
    completed: false
  });

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    dispatch({ type: 'ADD_TASK', payload: newTask });
    setNewTask({
      title: '',
      description: '',
      category: '',
      dueDate: new Date(),
      priority: 'medium',
      completed: false
    });
  };

  const filteredAndSortedTasks = useMemo(() => {
    let filtered = state.tasks;
    
    if (state.filter !== 'all') {
      filtered = filtered.filter(task => task.category === state.filter);
    }

    return [...filtered].sort((a, b) => {
      switch (state.sortBy) {
        case 'priority':
          const priorityOrder = { high: 0, medium: 1, low: 2 };
          return priorityOrder[a.priority] - priorityOrder[b.priority];
        case 'dueDate':
          return a.dueDate.getTime() - b.dueDate.getTime();
        case 'title':
          return a.title.localeCompare(b.title);
        default:
          return 0;
      }
    });
  }, [state.tasks, state.filter, state.sortBy]);

  return (
    <div className="task-manager">
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          value={newTask.title}
          onChange={e => setNewTask({ ...newTask, title: e.target.value })}
          placeholder="Task title"
        />
        <textarea
          value={newTask.description}
          onChange={e => setNewTask({ ...newTask, description: e.target.value })}
          placeholder="Task description"
        />
        <select
          value={newTask.category}
          onChange={e => setNewTask({ ...newTask, category: e.target.value })}
        >
          <option value="">Select category</option>
          <option value="work">Work</option>
          <option value="personal">Personal</option>
          <option value="shopping">Shopping</option>
        </select>
        <input
          type="date"
          value={newTask.dueDate.toISOString().split('T')[0]}
          onChange={e => setNewTask({ ...newTask, dueDate: new Date(e.target.value) })}
        />
        <select
          value={newTask.priority}
          onChange={e => setNewTask({ ...newTask, priority: e.target.value as Task['priority'] })}
        >
          <option value="low">Low</option>
          <option value="medium">Medium</option>
          <option value="high">High</option>
        </select>
        <button type="submit">Add Task</button>
      </form>

      <div className="filters">
        <select
          value={state.filter}
          onChange={e => dispatch({ type: 'SET_FILTER', payload: e.target.value })}
        >
          <option value="all">All Categories</option>
          <option value="work">Work</option>
          <option value="personal">Personal</option>
          <option value="shopping">Shopping</option>
        </select>

        <select
          value={state.sortBy}
          onChange={e => dispatch({ type: 'SET_SORT', payload: e.target.value as TaskState['sortBy'] })}
        >
          <option value="priority">Sort by Priority</option>
          <option value="dueDate">Sort by Due Date</option>
          <option value="title">Sort by Title</option>
        </select>
      </div>

      <div className="task-list">
        {filteredAndSortedTasks.map(task => (
          <div key={task.id} className={`task ${task.completed ? 'completed' : ''}`}>
            <input
              type="checkbox"
              checked={task.completed}
              onChange={() => dispatch({ type: 'TOGGLE_TASK', payload: task.id })}
            />
            <h3>{task.title}</h3>
            <p>{task.description}</p>
            <div className="task-meta">
              <span className="category">{task.category}</span>
              <span className="due-date">
                Due: {task.dueDate.toLocaleDateString()}
              </span>
              <span className={`priority ${task.priority}`}>
                {task.priority}
              </span>
            </div>
            <button
              onClick={() => dispatch({ type: 'DELETE_TASK', payload: task.id })}
            >
              Delete
            </button>
          </div>
        ))}
      </div>
    </div>
  );
};
```

## Key Takeaways

1. Use appropriate state management patterns based on complexity
2. Implement proper TypeScript types for state and actions
3. Leverage React hooks for lifecycle management
4. Use memoization for performance optimization
5. Consider context for global state management
6. Implement proper error handling and loading states

## Common Pitfalls to Avoid

1. **State Management**
   - Overusing global state
   - Not properly typing state
   - Missing dependency arrays in effects

2. **Performance**
   - Unnecessary re-renders
   - Missing memoization
   - Inefficient state updates

3. **Lifecycle**
   - Memory leaks in effects
   - Missing cleanup functions
   - Incorrect dependency arrays

## Further Reading

- [React Hooks Documentation](https://reactjs.org/docs/hooks-intro.html)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [React Performance Optimization](https://reactjs.org/docs/optimizing-performance.html) 