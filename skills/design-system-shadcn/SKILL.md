---
name: design-system-shadcn
description: "Design system base sur shadcn/ui et Radix UI. Use when building or extending a shadcn/ui-based design system, component library, or theme configuration."
---

# Design System with shadcn/ui Skill

## Overview
Expert in building scalable design systems using shadcn/ui, Radix UI, and Tailwind CSS. Covers architecture, composition patterns, tokens, theming, and multi-brand support.

## Architecture

### 1. Component Layers
```
components/
â”œâ”€â”€ ui/                    # Layer 1: shadcn primitives (DON'T EDIT)
â”‚   â”œâ”€â”€ button.tsx
â”‚   â”œâ”€â”€ input.tsx
â”‚   â”œâ”€â”€ dialog.tsx
â”‚   â””â”€â”€ ...
â”œâ”€â”€ primitives/            # Layer 2: Your wrappers (shared)
â”‚   â”œâ”€â”€ AppButton.tsx
â”‚   â”œâ”€â”€ AppInput.tsx
â”‚   â””â”€â”€ AppDialog.tsx
â””â”€â”€ blocks/                # Layer 3: Feature compositions
    â”œâ”€â”€ auth-form.tsx
    â”œâ”€â”€ pricing-card.tsx
    â””â”€â”€ dashboard-header.tsx
```

**Import Rules:**
- App code â†’ Layer 2/3 only
- Never import from `components/ui/` directly
- Enforce with ESLint: `no-restricted-imports`

### 2. shadcn/ui Setup
```bash
# Install (CLI only, no runtime dependency)
npx shadcn@latest init

# Add components one at a time
npx shadcn@latest add button
npx shadcn@latest add input
npx shadcn@latest add dialog

# Check for upstream changes
npx shadcn@latest diff
```

### 3. Design Tokens

```css
/* globals.css - CSS variables */
@layer base {
  :root {
    /* Colors */
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;
    
    /* Spacing */
    --radius: 0.5rem;
  }
  
  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    /* ... dark mode tokens */
  }
}
```

```typescript
// tailwind.config.js - Map tokens to utilities
module.exports = {
  theme: {
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
    },
  },
}
```

### 4. cn() Utility

```typescript
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Usage
<button className={cn(
  "base-classes",
  isActive && "active-classes",
  className  // Allow override
)}>
```

### 5. Component Composition

```typescript
// Layer 2: Your wrapper component
import { Button } from "@/components/ui/button";
import { Loader2 } from "lucide-react";
import { cn } from "@/lib/utils";

interface AppButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'default' | 'destructive' | 'outline' | 'secondary' | 'ghost' | 'link';
  size?: 'default' | 'sm' | 'lg' | 'icon';
  loading?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
}

export const AppButton = React.forwardRef<HTMLButtonElement, AppButtonProps>(
  ({ className, variant, size, loading, leftIcon, rightIcon, children, disabled, ...props }, ref) => {
    return (
      <Button
        ref={ref}
        variant={variant}
        size={size}
        className={cn("font-medium", className)}
        disabled={disabled || loading}
        {...props}
      >
        {loading ? (
          <Loader2 className="h-4 w-4 animate-spin mr-2" />
        ) : leftIcon ? (
          <span className="mr-2">{leftIcon}</span>
        ) : null}
        {children}
        {rightIcon && <span className="ml-2">{rightIcon}</span>}
      </Button>
    );
  }
);
```

### 6. asChild Pattern

```typescript
// Use asChild to render as child element
import { Link } from "react-router-dom";

// Bad: Nested buttons
<Button>
  <Link to="/dashboard">Go to Dashboard</Link>
</Button>

// Good: Button renders as Link
<Button asChild>
  <Link to="/dashboard">Go to Dashboard</Link>
</Button>

// Dialog trigger
<Dialog>
  <DialogTrigger asChild>
    <Button>Open Dialog</Button>
  </DialogTrigger>
  <DialogContent>
    {/* Dialog content */}
  </DialogContent>
</Dialog>
```

### 7. Multi-Brand Theming

```typescript
// Theme provider
function ThemeProvider({ children, brand = 'default' }) {
  useEffect(() => {
    document.documentElement.setAttribute('data-brand', brand);
  }, [brand]);
  
  return children;
}

// CSS for different brands
[data-brand="enterprise"] {
  --primary: 210 100% 50%;  /* Blue */
}

[data-brand="healthcare"] {
  --primary: 160 84% 40%;  /* Teal */
}
```

### 8. Storybook Documentation

```typescript
// Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './button';

const meta: Meta<typeof Button> = {
  title: 'Primitives/Button',
  component: Button,
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['default', 'destructive', 'outline', 'secondary', 'ghost', 'link'],
    },
    size: {
      control: 'select',
      options: ['default', 'sm', 'lg', 'icon'],
    },
  },
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Default: Story = {
  args: {
    children: 'Button',
  },
};

export const Loading: Story = {
  args: {
    children: 'Loading...',
    loading: true,
  },
};
```

### 9. Testing

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './button';

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  it('calls onClick when clicked', async () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await userEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when loading', () => {
    render(<Button loading>Submit</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });

  it('has correct focus styles', async () => {
    render(<Button>Click me</Button>);
    const button = screen.getByRole('button');
    
    await userEvent.tab();
    expect(button).toHaveFocus();
    expect(button).toHaveClass('focus-visible:ring-2');
  });
});
```

### 10. Checklist

- [ ] shadcn/ui initialized with CSS variable theme
- [ ] cn() utility configured
- [ ] Components organized: ui/ â†’ primitives/ â†’ blocks/
- [ ] ESLint rule blocks direct ui/ imports
- [ ] Design tokens in globals.css
- [ ] All components wrapped in Layer 2
- [ ] Storybook for component documentation
- [ ] Unit tests for critical components
- [ ] Accessibility tests (axe-core, keyboard)
- [ ] Visual regression tests (optional)

## Common Mistakes
- âŒ Editing `components/ui/*` directly
- âŒ Using className without cn()
- âŒ Overriding Radix accessibility attributes
- âŒ Importing from ui/ in app code
- âŒ Not forwarding refs
- âŒ Adding business logic to primitives
