# Creating Your First React TypeScript Project

## Introduction

In this section, we'll create a practical React TypeScript project from scratch. We'll build a simple task management application that demonstrates core React concepts and TypeScript features.

## Project Setup

Let's create a new project using Vite:

```bash
# Create new project
npm create vite@latest task-manager -- --template react-ts

# Navigate to project directory
cd task-manager

# Install dependencies
npm install

# Install additional dependencies we'll need
npm install @types/node styled-components @types/styled-components
```

## Project Structure

Let's create a well-organized project structure:

```bash
src/
├── components/          # Reusable components
│   ├── common/         # Shared components
│   ├── layout/         # Layout components
│   └── tasks/          # Task-specific components
├── hooks/              # Custom hooks
├── types/              # TypeScript type definitions
├── utils/              # Utility functions
├── styles/             # Global styles
└── App.tsx            # Root component
```

Let's create these directories:

```bash
mkdir -p src/{components/{common,layout,tasks},hooks,types,utils,styles}
```

## Core Files

### 1. Type Definitions (src/types/task.ts)

```typescript
export interface Task {
  id: string;
  title: string;
  description: string;
  completed: boolean;
  createdAt: Date;
}

export type TaskStatus = 'all' | 'active' | 'completed';
```

### 2. Utility Functions (src/utils/taskUtils.ts)

```typescript
import { Task } from '../types/task';

export const generateId = (): string => {
  return Math.random().toString(36).substr(2, 9);
};

export const formatDate = (date: Date): string => {
  return new Intl.DateTimeFormat('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  }).format(date);
};

export const filterTasks = (tasks: Task[], status: TaskStatus): Task[] => {
  switch (status) {
    case 'active':
      return tasks.filter(task => !task.completed);
    case 'completed':
      return tasks.filter(task => task.completed);
    default:
      return tasks;
  }
};
```

### 3. Custom Hook (src/hooks/useTasks.ts)

```typescript
import { useState, useCallback } from 'react';
import { Task, TaskStatus } from '../types/task';
import { generateId } from '../utils/taskUtils';

export const useTasks = () => {
  const [tasks, setTasks] = useState<Task[]>([]);
  const [status, setStatus] = useState<TaskStatus>('all');

  const addTask = useCallback((title: string, description: string) => {
    const newTask: Task = {
      id: generateId(),
      title,
      description,
      completed: false,
      createdAt: new Date(),
    };
    setTasks(prev => [...prev, newTask]);
  }, []);

  const toggleTask = useCallback((id: string) => {
    setTasks(prev =>
      prev.map(task =>
        task.id === id ? { ...task, completed: !task.completed } : task
      )
    );
  }, []);

  const deleteTask = useCallback((id: string) => {
    setTasks(prev => prev.filter(task => task.id !== id));
  }, []);

  return {
    tasks,
    status,
    setStatus,
    addTask,
    toggleTask,
    deleteTask,
  };
};
```

### 4. Components

#### TaskForm Component (src/components/tasks/TaskForm.tsx)

```typescript
import React, { useState } from 'react';
import styled from 'styled-components';

interface TaskFormProps {
  onSubmit: (title: string, description: string) => void;
}

const Form = styled.form`
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 2rem;
`;

const Input = styled.input`
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
`;

const TextArea = styled.textarea`
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  min-height: 100px;
`;

const Button = styled.button`
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;

  &:hover {
    background-color: #0056b3;
  }
`;

export const TaskForm: React.FC<TaskFormProps> = ({ onSubmit }) => {
  const [title, setTitle] = useState('');
  const [description, setDescription] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (!title.trim()) return;
    onSubmit(title, description);
    setTitle('');
    setDescription('');
  };

  return (
    <Form onSubmit={handleSubmit}>
      <Input
        type="text"
        value={title}
        onChange={e => setTitle(e.target.value)}
        placeholder="Task title"
      />
      <TextArea
        value={description}
        onChange={e => setDescription(e.target.value)}
        placeholder="Task description"
      />
      <Button type="submit">Add Task</Button>
    </Form>
  );
};
```

#### TaskList Component (src/components/tasks/TaskList.tsx)

```typescript
import React from 'react';
import styled from 'styled-components';
import { Task } from '../../types/task';
import { formatDate } from '../../utils/taskUtils';

interface TaskListProps {
  tasks: Task[];
  onToggle: (id: string) => void;
  onDelete: (id: string) => void;
}

const List = styled.ul`
  list-style: none;
  padding: 0;
`;

const TaskItem = styled.li`
  display: flex;
  align-items: center;
  padding: 1rem;
  border: 1px solid #eee;
  margin-bottom: 0.5rem;
  border-radius: 4px;

  &:hover {
    background-color: #f8f9fa;
  }
`;

const TaskContent = styled.div`
  flex: 1;
  margin-left: 1rem;
`;

const TaskTitle = styled.h3`
  margin: 0;
  text-decoration: ${props => (props.completed ? 'line-through' : 'none')};
`;

const TaskDescription = styled.p`
  margin: 0.5rem 0;
  color: #666;
`;

const TaskDate = styled.small`
  color: #999;
`;

const Button = styled.button`
  padding: 0.25rem 0.5rem;
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-left: 0.5rem;

  &:hover {
    background-color: #c82333;
  }
`;

export const TaskList: React.FC<TaskListProps> = ({
  tasks,
  onToggle,
  onDelete,
}) => {
  return (
    <List>
      {tasks.map(task => (
        <TaskItem key={task.id}>
          <input
            type="checkbox"
            checked={task.completed}
            onChange={() => onToggle(task.id)}
          />
          <TaskContent>
            <TaskTitle completed={task.completed}>{task.title}</TaskTitle>
            <TaskDescription>{task.description}</TaskDescription>
            <TaskDate>Created: {formatDate(task.createdAt)}</TaskDate>
          </TaskContent>
          <Button onClick={() => onDelete(task.id)}>Delete</Button>
        </TaskItem>
      ))}
    </List>
  );
};
```

### 5. Main App Component (src/App.tsx)

```typescript
import React from 'react';
import styled from 'styled-components';
import { TaskForm } from './components/tasks/TaskForm';
import { TaskList } from './components/tasks/TaskList';
import { useTasks } from './hooks/useTasks';

const Container = styled.div`
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
`;

const Header = styled.header`
  text-align: center;
  margin-bottom: 2rem;
`;

const Title = styled.h1`
  color: #333;
  margin-bottom: 1rem;
`;

const App: React.FC = () => {
  const { tasks, status, setStatus, addTask, toggleTask, deleteTask } = useTasks();

  return (
    <Container>
      <Header>
        <Title>Task Manager</Title>
        <div>
          <button onClick={() => setStatus('all')}>All</button>
          <button onClick={() => setStatus('active')}>Active</button>
          <button onClick={() => setStatus('completed')}>Completed</button>
        </div>
      </Header>
      <TaskForm onSubmit={addTask} />
      <TaskList
        tasks={tasks}
        onToggle={toggleTask}
        onDelete={deleteTask}
      />
    </Container>
  );
};

export default App;
```

## Running the Project

```bash
# Start the development server
npm run dev
```

## Practice Exercises

1. Enhance the Task Manager:
   - Add task categories/tags
   - Implement task editing
   - Add due dates
   - Implement task sorting
   - Add task search functionality

2. Add Error Handling:
   - Implement error boundaries
   - Add form validation
   - Handle API errors (if adding backend)
   - Add loading states

3. Improve the UI/UX:
   - Add animations
   - Implement drag-and-drop reordering
   - Add task statistics
   - Implement dark mode
   - Add responsive design

## Key Takeaways

- Project structure is crucial for maintainability
- TypeScript helps catch errors early
- Custom hooks help manage complex state logic
- Styled-components provide type-safe styling
- Component composition is key to building reusable UI
- Proper type definitions improve code quality

## Next Steps

In the next section, we'll explore JSX with TypeScript in more detail, including advanced patterns and best practices. 