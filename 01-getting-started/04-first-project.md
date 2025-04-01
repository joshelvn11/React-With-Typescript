# 1.4 Creating Your First React+TypeScript Project

## Project Initialization

### 1. Create a New Project

```bash
# Create a new React project with TypeScript template
npx create-react-app my-first-react-app --template typescript

# Navigate to the project directory
cd my-first-react-app

# Start the development server
npm start
```

### 2. Project Structure Overview

The generated project will have this structure:
```bash
my-first-react-app/
├── node_modules/
├── public/
│   ├── favicon.ico
│   ├── index.html
│   ├── manifest.json
│   └── robots.txt
├── src/
│   ├── App.tsx
│   ├── App.css
│   ├── App.test.tsx
│   ├── index.tsx
│   ├── index.css
│   ├── logo.svg
│   ├── react-app-env.d.ts
│   └── setupTests.ts
├── package.json
├── tsconfig.json
└── README.md
```

## Understanding Key Files

### 1. Entry Point (src/index.tsx)
```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

### 2. Main App Component (src/App.tsx)
```typescript
import React from 'react';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
      </header>
    </div>
  );
}

export default App;
```

### 3. TypeScript Configuration (tsconfig.json)
```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": ["src"]
}
```

## Creating Your First Component

### 1. Create a New Component Directory
```bash
mkdir src/components
```

### 2. Create a Welcome Component
```typescript
// src/components/Welcome.tsx
import React from 'react';

interface WelcomeProps {
  name: string;
}

const Welcome: React.FC<WelcomeProps> = ({ name }) => {
  return (
    <div className="welcome">
      <h1>Welcome to React with TypeScript!</h1>
      <p>Hello, {name}!</p>
    </div>
  );
};

export default Welcome;
```

### 3. Update App.tsx to Use the New Component
```typescript
import React from 'react';
import './App.css';
import Welcome from './components/Welcome';

function App() {
  return (
    <div className="App">
      <Welcome name="Developer" />
    </div>
  );
}

export default App;
```

## Adding Styling

### 1. Create Component Styles
```css
/* src/components/Welcome.css */
.welcome {
  text-align: center;
  padding: 2rem;
}

.welcome h1 {
  color: #2c3e50;
  margin-bottom: 1rem;
}

.welcome p {
  color: #7f8c8d;
  font-size: 1.2rem;
}
```

### 2. Import Styles in Component
```typescript
import './Welcome.css';
```

## Adding Interactivity

### 1. Create an Interactive Component
```typescript
// src/components/Counter.tsx
import React, { useState } from 'react';

const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  return (
    <div className="counter">
      <h2>Counter: {count}</h2>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
      <button onClick={() => setCount(count - 1)}>
        Decrement
      </button>
    </div>
  );
};

export default Counter;
```

### 2. Add Counter to App
```typescript
import Counter from './components/Counter';

// Add inside App component
<Counter />
```

## Running and Testing

### 1. Development Server
```bash
npm start
```
- Opens browser to http://localhost:3000
- Hot reloading enabled
- Error reporting in browser

### 2. Running Tests
```bash
npm test
```
- Runs test suite
- Interactive test runner
- Watch mode enabled

### 3. Building for Production
```bash
npm run build
```
- Creates optimized production build
- Generates static files
- Ready for deployment

## Common Issues and Solutions

### 1. TypeScript Errors
```typescript
// Common error: Missing type definition
const [data, setData] = useState(null); // Error

// Solution: Add proper type
const [data, setData] = useState<string | null>(null);
```

### 2. Import Issues
```typescript
// Common error: Module not found
import { Component } from './Component'; // Error

// Solution: Check file extension
import { Component } from './Component.tsx';
```

### 3. Component Props
```typescript
// Common error: Missing required props
<Welcome /> // Error

// Solution: Provide required props
<Welcome name="Developer" />
```

## Practice Exercise

Create a simple Todo List application:

1. **Create TodoItem Component**
```typescript
// src/components/TodoItem.tsx
import React from 'react';

interface TodoItemProps {
  text: string;
  completed: boolean;
  onToggle: () => void;
}

const TodoItem: React.FC<TodoItemProps> = ({ text, completed, onToggle }) => {
  return (
    <li 
      style={{ textDecoration: completed ? 'line-through' : 'none' }}
      onClick={onToggle}
    >
      {text}
    </li>
  );
};

export default TodoItem;
```

2. **Create TodoList Component**
```typescript
// src/components/TodoList.tsx
import React, { useState } from 'react';
import TodoItem from './TodoItem';

interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

const TodoList: React.FC = () => {
  const [todos, setTodos] = useState<Todo[]>([
    { id: 1, text: 'Learn React', completed: false },
    { id: 2, text: 'Learn TypeScript', completed: false },
  ]);

  const toggleTodo = (id: number) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  return (
    <ul>
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          text={todo.text}
          completed={todo.completed}
          onToggle={() => toggleTodo(todo.id)}
        />
      ))}
    </ul>
  );
};

export default TodoList;
```

3. **Update App.tsx**
```typescript
import React from 'react';
import './App.css';
import TodoList from './components/TodoList';

function App() {
  return (
    <div className="App">
      <h1>Todo List</h1>
      <TodoList />
    </div>
  );
}

export default App;
```

## Key Takeaways

- Create React App provides a solid foundation for TypeScript projects
- Components should be properly typed with interfaces
- State management requires proper type definitions
- CSS modules can be used for component-specific styling
- Testing is built into the project setup

## Common Pitfalls to Avoid

1. **Type Definition Issues**
   - Always define interfaces for props
   - Use proper type annotations for state
   - Handle null/undefined cases

2. **Component Structure**
   - Keep components focused and single-purpose
   - Use proper file organization
   - Follow naming conventions

3. **State Management**
   - Use appropriate state hooks
   - Handle state updates properly
   - Consider prop drilling vs context

## Further Reading

- [Create React App Documentation](https://create-react-app.dev/docs/getting-started)
- [React TypeScript Cheatsheet](https://github.com/typescript-cheatsheets/react)
- [React Testing Library Documentation](https://testing-library.com/docs/react-testing-library/intro) 