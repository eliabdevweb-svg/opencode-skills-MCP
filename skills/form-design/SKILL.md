---
name: form-design
description: "Conception de formulaires accessibles: validation, erreurs, progressive disclosure, wizards etapes. Use when building forms, validation flows, or improving form UX."
---

# Form Design Skill

## Overview
Expert in designing user-friendly forms with proper validation, error handling, accessibility, and progressive disclosure. Covers simple forms to complex multi-step wizards.

## Form Patterns

### 1. Form Layout
```typescript
// Single column (preferred for most forms)
function FormLayout({ children }) {
  return (
    <div className="space-y-6 max-w-lg">
      {children}
    </div>
  );
}

// Two columns (for dense forms)
function FormGrid({ children }) {
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
      {children}
    </div>
  );
}
```

### 2. Field Components
```typescript
// Form field wrapper
interface FieldProps {
  label: string;
  description?: string;
  error?: string;
  required?: boolean;
  children: React.ReactNode;
}

function Field({ label, description, error, required, children }: FieldProps) {
  const id = useId();
  
  return (
    <div className="space-y-2">
      <Label htmlFor={id}>
        {label}
        {required && <span className="text-destructive ml-1">*</span>}
      </Label>
      {description && (
        <p className="text-sm text-muted-foreground">{description}</p>
      )}
      <div id={id}>{children}</div>
      {error && (
        <p className="text-sm text-destructive" role="alert">{error}</p>
      )}
    </div>
  );
}

// Usage
<Field label="Email" error={errors.email?.message} required>
  <Input {...register('email')} type="email" />
</Field>
```

### 3. Validation Patterns

```typescript
// React Hook Form + Zod
const schema = z.object({
  name: z.string().min(1, 'Name is required'),
  email: z.string().email('Invalid email address'),
  password: z.string()
    .min(8, 'Password must be at least 8 characters')
    .regex(/[A-Z]/, 'Must contain at least one uppercase letter')
    .regex(/[0-9]/, 'Must contain at least one number'),
  confirmPassword: z.string(),
  age: z.number().min(18, 'Must be at least 18'),
  website: z.string().url().optional().or(z.literal('')),
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ['confirmPassword'],
});

// Validation timing
const methods = useForm({
  resolver: zodResolver(schema),
  mode: 'onBlur',        // Validate on blur (recommended)
  // mode: 'onChange',    // Validate on change (aggressive)
  // mode: 'onTouched',   // First blur, then onChange
  // mode: 'onSubmit',    // Only on submit (permissive)
});
```

### 4. Error Display

```typescript
// Inline validation (best UX)
function InputField({ register, error }) {
  return (
    <div className="space-y-1">
      <Input
        {...register}
        className={error ? 'border-destructive' : ''}
        aria-invalid={!!error}
        aria-describedby={error ? `${register.name}-error` : undefined}
      />
      {error && (
        <p id={`${register.name}-error`} className="text-sm text-destructive">
          {error.message}
        </p>
      )}
    </div>
  );
}

// Field-level vs Form-level errors
function ErrorSummary({ errors }) {
  const errorList = Object.entries(errors);
  
  if (errorList.length === 0) return null;
  
  return (
    <Alert variant="destructive">
      <AlertCircle className="h-4 w-4" />
      <AlertTitle>Please fix the following errors:</AlertTitle>
      <AlertDescription>
        <ul className="list-disc list-inside">
          {errorList.map(([field, error]) => (
            <li key={field}>{error.message}</li>
          ))}
        </ul>
      </AlertDescription>
    </Alert>
  );
}
```

### 5. Multi-Step Forms

```typescript
// Wizard pattern
interface Step {
  id: string;
  title: string;
  description?: string;
  fields: string[]; // Field names for this step
}

function FormWizard({ steps, currentStep, onSubmit }) {
  return (
    <div className="space-y-8">
      {/* Step indicator */}
      <nav aria-label="Progress">
        <ol className="flex items-center">
          {steps.map((step, index) => (
            <li key={step.id} className="flex items-center">
              <span className={cn(
                "flex h-8 w-8 items-center justify-center rounded-full",
                index < currentStep && "bg-primary text-primary-foreground",
                index === currentStep && "bg-primary text-primary-foreground ring-2 ring-primary ring-offset-2",
                index > currentStep && "bg-muted text-muted-foreground"
              )}>
                {index < currentStep ? <Check className="h-4 w-4" /> : index + 1}
              </span>
              <span className="ml-2 text-sm font-medium">{step.title}</span>
              {index < steps.length - 1 && (
                <div className="ml-2 h-0.5 w-12 bg-muted" />
              )}
            </li>
          ))}
        </ol>
      </nav>

      {/* Step content */}
      <div>
        <h2 className="text-lg font-semibold">{steps[currentStep].title}</h2>
        {steps[currentStep].description && (
          <p className="text-muted-foreground">{steps[currentStep].description}</p>
        )}
      </div>

      {/* Navigation */}
      <div className="flex justify-between">
        <Button variant="outline" onClick={prevStep} disabled={currentStep === 0}>
          Previous
        </Button>
        <Button onClick={currentStep === steps.length - 1 ? onSubmit : nextStep}>
          {currentStep === steps.length - 1 ? 'Submit' : 'Next'}
        </Button>
      </div>
    </div>
  );
}
```

### 6. Smart Defaults

```typescript
// Context-aware defaults
const smartDefaults = {
  // Based on user role
  role: user.role === 'admin' ? 'editor' : 'viewer',
  
  // Based on previous input
  shippingAddress: sameAsBilling ? billingAddress : null,
  
  // Based on context
  currency: user.region === 'EU' ? 'EUR' : 'USD',
  
  // Based on date
  dueDate: addDays(new Date(), 30),
};
```

### 7. Auto-Save

```typescript
// Debounced auto-save
function AutoSaveForm({ form, onSave }) {
  const saveRef = useRef<DebouncedFn>();
  
  useEffect(() => {
    saveRef.current = debounce(async (data) => {
      await onSave(data);
      toast.success('Draft saved');
    }, 2000);
    
    return () => saveRef.current?.cancel();
  }, [onSave]);

  useEffect(() => {
    const subscription = form.watch((data) => {
      saveRef.current?.(data);
    });
    return () => subscription.unsubscribe();
  }, [form.watch]);

  return null;
}

// Visual indicator
<div className="flex items-center gap-2 text-sm text-muted-foreground">
  {isSaving ? (
    <>
      <Loader2 className="h-4 w-4 animate-spin" />
      Saving...
    </>
  ) : (
    <>
      <Check className="h-4 w-4" />
      All changes saved
    </>
  )}
</div>
```

### 8. Keyboard Shortcuts

```typescript
// Form keyboard shortcuts
function useFormKeyboard({ onSubmit, onReset }) {
  useEffect(() => {
    const handler = (e: KeyboardEvent) => {
      // Ctrl/Cmd + S to save
      if ((e.ctrlKey || e.metaKey) && e.key === 's') {
        e.preventDefault();
        onSubmit();
      }
      // Ctrl/Cmd + Enter to submit
      if ((e.ctrlKey || e.metaKey) && e.key === 'Enter') {
        e.preventDefault();
        onSubmit();
      }
      // Escape to cancel/reset
      if (e.key === 'Escape') {
        onReset();
      }
    };
    
    window.addEventListener('keydown', handler);
    return () => window.removeEventListener('keydown', handler);
  }, [onSubmit, onReset]);
}
```

### 9. Accessible Forms

```typescript
// Accessibility requirements
const a11yRequirements = {
  // Labels
  labels: 'Every input must have visible label',
  
  // Required fields
  required: 'Use aria-required and visual indicator',
  
  // Error announcements
  errors: 'Use aria-live="polite" for error messages',
  
  // Field descriptions
  descriptions: 'Use aria-describedby for help text',
  
  // Keyboard
  keyboard: 'Tab through fields, Enter to submit',
  
  // Focus management
  focus: 'Focus first error field on validation failure',
};
```

### 10. Form Patterns by Use Case

```typescript
// Login form
function LoginForm() {
  return (
    <Form onSubmit={onSubmit}>
      <Field label="Email" required>
        <Input type="email" {...register('email')} />
      </Field>
      <Field label="Password" required>
        <Input type="password" {...register('password')} />
      </Field>
      <div className="flex items-center justify-between">
        <label className="flex items-center gap-2">
          <Checkbox {...register('remember')} />
          <span className="text-sm">Remember me</span>
        </label>
        <a href="/forgot-password" className="text-sm text-primary">
          Forgot password?
        </a>
      </div>
      <Button type="submit" className="w-full">Sign In</Button>
    </Form>
  );
}

// Settings form
function SettingsForm() {
  return (
    <Form onSubmit={onSubmit}>
      <Section title="Profile">
        <Field label="Display name">
          <Input {...register('name')} />
        </Field>
        <Field label="Email">
          <Input {...register('email')} type="email" />
        </Field>
      </Section>
      <Section title="Notifications">
        <label className="flex items-center gap-2">
          <Switch {...register('emailNotifications')} />
          <span>Email notifications</span>
        </label>
      </Section>
      <div className="flex justify-end gap-2">
        <Button type="button" variant="outline">Cancel</Button>
        <Button type="submit">Save Changes</Button>
      </div>
    </Form>
  );
}
```

## Validation Libraries
- **Zod**: Type-safe, excellent TypeScript inference
- **Yup**: Popular, simple API
- **Valibot**: Smaller bundle, similar to Zod
- **React Hook Form**: Performance-focused form library
- **Formik**: Mature, full-featured (but heavier)
