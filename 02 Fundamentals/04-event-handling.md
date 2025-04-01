# Event Handling Patterns

## Introduction

Event handling is a fundamental part of React applications. In this section, we'll explore how to handle different types of events effectively with TypeScript, ensuring type safety and proper event handling patterns.

## Basic Event Handling

### 1. Mouse Events

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

// Usage with different mouse events
const MouseEvents: React.FC = () => {
  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    console.log('Clicked at:', e.clientX, e.clientY);
  };

  const handleDoubleClick = (e: React.MouseEvent<HTMLDivElement>) => {
    console.log('Double clicked element:', e.currentTarget);
  };

  const handleMouseEnter = (e: React.MouseEvent<HTMLDivElement>) => {
    e.currentTarget.style.backgroundColor = 'lightblue';
  };

  const handleMouseLeave = (e: React.MouseEvent<HTMLDivElement>) => {
    e.currentTarget.style.backgroundColor = 'white';
  };

  return (
    <div>
      <Button onClick={handleClick}>Click me</Button>
      <div
        onDoubleClick={handleDoubleClick}
        onMouseEnter={handleMouseEnter}
        onMouseLeave={handleMouseLeave}
      >
        Hover and double click me
      </div>
    </div>
  );
};
```

### 2. Form Events

```typescript
interface FormData {
  username: string;
  email: string;
  password: string;
}

const LoginForm: React.FC = () => {
  const [formData, setFormData] = useState<FormData>({
    username: '',
    email: '',
    password: '',
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value,
    }));
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    // Handle form submission
    console.log('Form submitted:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="username"
        value={formData.username}
        onChange={handleChange}
      />
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
      />
      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleChange}
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

## Advanced Event Handling

### 1. Custom Events

```typescript
interface CustomEventData {
  type: 'success' | 'error' | 'warning';
  message: string;
  timestamp: number;
}

interface EventEmitterProps {
  onEvent: (event: CustomEventData) => void;
}

const EventEmitter: React.FC<EventEmitterProps> = ({ onEvent }) => {
  const emitEvent = (type: CustomEventData['type'], message: string) => {
    const event: CustomEventData = {
      type,
      message,
      timestamp: Date.now(),
    };
    onEvent(event);
  };

  return (
    <div>
      <button onClick={() => emitEvent('success', 'Operation successful!')}>
        Emit Success
      </button>
      <button onClick={() => emitEvent('error', 'Operation failed!')}>
        Emit Error
      </button>
      <button onClick={() => emitEvent('warning', 'Operation pending!')}>
        Emit Warning
      </button>
    </div>
  );
};

const EventListener: React.FC = () => {
  const handleCustomEvent = (event: CustomEventData) => {
    console.log('Received event:', event);
    // Handle different event types
    switch (event.type) {
      case 'success':
        // Handle success
        break;
      case 'error':
        // Handle error
        break;
      case 'warning':
        // Handle warning
        break;
    }
  };

  return <EventEmitter onEvent={handleCustomEvent} />;
};
```

### 2. Event Delegation

```typescript
interface ListItem {
  id: string;
  text: string;
  status: 'active' | 'completed';
}

const List: React.FC = () => {
  const [items, setItems] = useState<ListItem[]>([]);

  const handleListClick = (e: React.MouseEvent<HTMLUListElement>) => {
    const target = e.target as HTMLElement;
    const listItem = target.closest('li');
    
    if (!listItem) return;

    const itemId = listItem.getAttribute('data-id');
    if (!itemId) return;

    // Handle different actions based on clicked element
    if (target.classList.contains('delete-btn')) {
      setItems(prev => prev.filter(item => item.id !== itemId));
    } else if (target.classList.contains('status-btn')) {
      setItems(prev =>
        prev.map(item =>
          item.id === itemId
            ? {
                ...item,
                status: item.status === 'active' ? 'completed' : 'active',
              }
            : item
        )
      );
    }
  };

  return (
    <ul onClick={handleListClick}>
      {items.map(item => (
        <li key={item.id} data-id={item.id}>
          <span>{item.text}</span>
          <button className="status-btn">
            {item.status === 'active' ? 'Complete' : 'Activate'}
          </button>
          <button className="delete-btn">Delete</button>
        </li>
      ))}
    </ul>
  );
};
```

## Form Validation with Events

### 1. Real-time Validation

```typescript
interface ValidationRule {
  type: 'required' | 'email' | 'min' | 'max' | 'pattern';
  message: string;
  value?: number | string | RegExp;
}

interface FieldValidation {
  isValid: boolean;
  message: string;
}

const useFieldValidation = (
  value: string,
  rules: ValidationRule[]
): FieldValidation => {
  const validate = useCallback((): FieldValidation => {
    for (const rule of rules) {
      switch (rule.type) {
        case 'required':
          if (!value.trim()) {
            return { isValid: false, message: rule.message };
          }
          break;
        case 'email':
          if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
            return { isValid: false, message: rule.message };
          }
          break;
        case 'min':
          if (value.length < (rule.value as number)) {
            return { isValid: false, message: rule.message };
          }
          break;
        case 'max':
          if (value.length > (rule.value as number)) {
            return { isValid: false, message: rule.message };
          }
          break;
        case 'pattern':
          if (!(rule.value as RegExp).test(value)) {
            return { isValid: false, message: rule.message };
          }
          break;
      }
    }
    return { isValid: true, message: '' };
  }, [value, rules]);

  return validate();
};

const ValidatedInput: React.FC<{
  value: string;
  onChange: (value: string) => void;
  rules: ValidationRule[];
}> = ({ value, onChange, rules }) => {
  const validation = useFieldValidation(value, rules);

  return (
    <div>
      <input
        type="text"
        value={value}
        onChange={e => onChange(e.target.value)}
      />
      {!validation.isValid && (
        <span className="error">{validation.message}</span>
      )}
    </div>
  );
};
```

### 2. Form Submission with Validation

```typescript
interface FormField {
  name: string;
  value: string;
  rules: ValidationRule[];
}

interface FormState {
  fields: Record<string, FormField>;
  errors: Record<string, string>;
  isValid: boolean;
}

const useForm = (initialFields: Record<string, FormField>) => {
  const [state, setState] = useState<FormState>({
    fields: initialFields,
    errors: {},
    isValid: false,
  });

  const validateField = (name: string, value: string): string => {
    const field = state.fields[name];
    const validation = useFieldValidation(value, field.rules);
    return validation.message;
  };

  const handleChange = (name: string, value: string) => {
    const error = validateField(name, value);
    setState(prev => ({
      ...prev,
      fields: {
        ...prev.fields,
        [name]: { ...prev.fields[name], value },
      },
      errors: {
        ...prev.errors,
        [name]: error,
      },
      isValid: Object.values(prev.fields).every(
        field => !validateField(field.name, field.value)
      ),
    }));
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    if (!state.isValid) return;

    // Handle form submission
    const formData = Object.entries(state.fields).reduce(
      (acc, [name, field]) => ({
        ...acc,
        [name]: field.value,
      }),
      {}
    );
    console.log('Form submitted:', formData);
  };

  return {
    state,
    handleChange,
    handleSubmit,
  };
};
```

## Practice Exercises

1. Create a custom event system that:
   - Supports event bubbling
   - Handles event cancellation
   - Includes event data validation
   - Supports event listeners
   - Has proper TypeScript types

2. Implement a form validation system that:
   - Validates in real-time
   - Supports custom validation rules
   - Handles async validation
   - Shows validation errors
   - Has proper TypeScript types

3. Build an event delegation system that:
   - Handles dynamic content
   - Supports custom events
   - Includes event filtering
   - Manages event listeners
   - Has proper TypeScript types

## Key Takeaways

- TypeScript provides type safety for event handling
- Event delegation improves performance
- Form validation should be type-safe
- Custom events enhance component communication
- Proper event typing prevents runtime errors
- Event handling should be consistent
- Cleanup is important for event listeners

## Next Steps

In the next section, we'll explore conditional rendering strategies in React with TypeScript, including different patterns and best practices. 