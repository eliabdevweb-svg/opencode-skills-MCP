---
name: state-management
description: "Gestion d'etat: patterns Redux, Zustand, Context, signals, architecture de store. Use when choosing or implementing state management in an application."
---

# State Management Skill

## Overview
Expert in managing application state using React patterns, context API, Redux, Zustand, and server state management with TanStack Query.

## State Categories

### 1. UI State (Local Component State)
```typescript
// useState for simple local state
const [isOpen, setIsOpen] = useState(false);
const [count, setCount] = useState(0);
const [searchQuery, setSearchQuery] = useState('');

// useReducer for complex local state
type FormState = {
  values: Record<string, string>;
  errors: Record<string, string>;
  isSubmitting: boolean;
};

type FormAction =
  | { type: 'SET_FIELD'; field: string; value: string }
  | { type: 'SET_ERROR'; field: string; error: string }
  | { type: 'SUBMIT_START' }
  | { type: 'SUBMIT_SUCCESS' }
  | { type: 'SUBMIT_FAILURE'; errors: Record<string, string> };

function formReducer(state: FormState, action: FormAction): FormState {
  switch (action.type) {
    case 'SET_FIELD':
      return { ...state, values: { ...state.values, [action.field]: action.value } };
    case 'SET_ERROR':
      return { ...state, errors: { ...state.errors, [action.field]: action.error } };
    case 'SUBMIT_START':
      return { ...state, isSubmitting: true, errors: {} };
    case 'SUBMIT_SUCCESS':
      return { ...state, isSubmitting: false };
    case 'SUBMIT_FAILURE':
      return { ...state, isSubmitting: false, errors: action.errors };
  }
}
```

### 2. Server State (Remote Data)
```typescript
// TanStack Query (React Query) for server state
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Fetching data
function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(res => res.json()),
    staleTime: 5 * 60 * 1000, // 5 minutes
    gcTime: 10 * 60 * 1000, // 10 minutes (formerly cacheTime)
  });
}

// Mutations with cache invalidation
function useCreateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (newUser: CreateUserInput) =>
      fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(newUser),
      }).then(res => res.json()),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
}

// Optimistic updates
function useToggleTodo() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (todo: Todo) =>
      fetch(`/api/todos/${todo.id}`, {
        method: 'PATCH',
        body: JSON.stringify({ completed: !todo.completed }),
      }),
    onMutate: async (todo) => {
      await queryClient.cancelQueries({ queryKey: ['todos'] });

      const previousTodos = queryClient.getQueryData(['todos']);

      queryClient.setQueryData(['todos'], (old: Todo[]) =>
        old.map(t => t.id === todo.id ? { ...t, completed: !t.completed } : t)
      );

      return { previousTodos };
    },
    onError: (err, todo, context) => {
      queryClient.setQueryData(['todos'], context?.previousTodos);
    },
    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
  });
}
```

### 3. Global UI State (Zustand)
```typescript
import { create } from 'zustand';
import { devtools, persist } from 'zustand/middleware';

interface AppStore {
  // Auth state
  user: User | null;
  isAuthenticated: boolean;
  login: (credentials: LoginCredentials) => Promise<void>;
  logout: () => void;

  // UI state
  sidebarOpen: boolean;
  toggleSidebar: () => void;

  // Theme
  theme: 'light' | 'dark';
  setTheme: (theme: 'light' | 'dark') => void;
}

export const useAppStore = create<AppStore>()(
  devtools(
    persist(
      (set, get) => ({
        // Auth
        user: null,
        isAuthenticated: false,
        login: async (credentials) => {
          const user = await authService.login(credentials);
          set({ user, isAuthenticated: true }, false, 'login');
        },
        logout: () => {
          set({ user: null, isAuthenticated: false }, false, 'logout');
        },

        // UI
        sidebarOpen: true,
        toggleSidebar: () => set(
          (state) => ({ sidebarOpen: !state.sidebarOpen }),
          false,
          'toggleSidebar'
        ),

        // Theme
        theme: 'light',
        setTheme: (theme) => set({ theme }, false, 'setTheme'),
      }),
      { name: 'app-store' }
    )
  )
);
```

### 4. Form State (React Hook Form)
```typescript
import { useForm, FormProvider } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  name: z.string().min(1, 'Name is required'),
  email: z.string().email('Invalid email'),
  age: z.number().min(18, 'Must be at least 18'),
});

type FormData = z.infer<typeof schema>;

function MyForm() {
  const methods = useForm<FormData>({
    resolver: zodResolver(schema),
    mode: 'onBlur',
  });

  const { register, handleSubmit, formState: { errors, isSubmitting } } = methods;

  const onSubmit = async (data: FormData) => {
    await createUser(data);
  };

  return (
    <FormProvider {...methods}>
      <form onSubmit={handleSubmit(onSubmit)}>
        <input {...register('name')} />
        {errors.name && <span>{errors.name.message}</span>}

        <input {...register('email')} />
        {errors.email && <span>{errors.email.message}</span>}

        <input type="number" {...register('age', { valueAsNumber: true })} />
        {errors.age && <span>{errors.age.message}</span>}

        <button type="submit" disabled={isSubmitting}>
          Submit
        </button>
      </form>
    </FormProvider>
  );
}
```

### 5. URL State
```typescript
import { useSearchParams } from 'react-router-dom';

function ProductFilters() {
  const [searchParams, setSearchParams] = useSearchParams();

  const category = searchParams.get('category') || 'all';
  const page = parseInt(searchParams.get('page') || '1');
  const sort = searchParams.get('sort') || 'newest';

  const updateFilter = (key: string, value: string) => {
    setSearchParams(prev => {
      prev.set(key, value);
      if (key !== 'page') prev.set('page', '1');
      return prev;
    });
  };

  return (
    <div>
      <select value={category} onChange={e => updateFilter('category', e.target.value)}>
        <option value="all">All Categories</option>
        <option value="electronics">Electronics</option>
      </select>
      {/* ... */}
    </div>
  );
}
```

## State Management Decision Tree

```
Is the state...
â”œâ”€â”€ Used by one component?
â”‚   â””â”€â”€ useState / useReducer
â”œâ”€â”€ Used by sibling components?
â”‚   â””â”€â”€ Lift state up to common parent
â”œâ”€â”€ Used by distant components?
â”‚   â”œâ”€â”€ Server data? â†’ TanStack Query
â”‚   â”œâ”€â”€ UI/theme state? â†’ Zustand
â”‚   â””â”€â”€ Complex shared state? â†’ Context + useReducer
â””â”€â”€ Form state?
    â””â”€â”€ React Hook Form + Zod
```

## Performance Patterns

### Memoization
```typescript
// useMemo for expensive computations
const filteredUsers = useMemo(() => {
  return users.filter(user => user.name.includes(searchQuery));
}, [users, searchQuery]);

// useCallback for stable function references
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);

// React.memo for component memoization
const UserCard = React.memo(({ user }: { user: User }) => {
  return <div>{user.name}</div>;
});
```

### Selector Pattern (Zustand)
```typescript
// Bad: Re-renders on any store change
const { user, theme } = useAppStore();

// Good: Only re-renders when user changes
const user = useAppStore(state => state.user);
const theme = useAppStore(state => state.theme);
```

## Tools
- **State**: Zustand, Jotai, Valtio, Redux Toolkit
- **Server State**: TanStack Query, SWR
- **Forms**: React Hook Form, Formik
- **URL**: nuqs, React Router useSearchParams
- **Validation**: Zod, Yup, Valibot
