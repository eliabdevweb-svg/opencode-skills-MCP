---
name: saas-design-system
description: "Design System spécifique SaaS. Tokens, composants, patterns pour produits multi-tenant. Design tokens 3 couches, composants SaaS (data tables, dashboards, forms, navigation), intégration Tailwind/shadcn, dark mode. Use when building design systems for SaaS, creating SaaS-specific components, or establishing design tokens for multi-tenant applications."
version: 1.0.0
tags: [saas, design-system, tokens, components, tailwind, shadcn, dark-mode]
---

# SaaS Design System

Design System spécialement conçu pour les produits SaaS multi-tenant. Tokens, composants, patterns.

## When to Use

| Scenario | Trigger Examples |
|----------|-----------------|
| **Nouveau Design System** | "Create SaaS design system", "Build token architecture" |
| **Composants SaaS** | "Data table component", "Dashboard layout" |
| **Tokens** | "Design tokens for SaaS", "CSS variables system" |
| **Multi-tenant theming** | "Tenant customization", "White-label SaaS" |
| **Intégration** | "Tailwind config", "shadcn components" |
| **Dark Mode** | "Add dark mode", "Theme switching" |

---

## Token Architecture (3 Couches)

### Structure

```
┌─────────────────────────────────────────────────┐
│            Primitive Tokens                      │
│    (Raw values, platform-agnostic)              │
│    --color-blue-500: #3B82F6                     │
│    --space-4: 16px                              │
│    --font-size-base: 14px                       │
├─────────────────────────────────────────────────┤
│            Semantic Tokens                       │
│    (Purpose aliases)                            │
│    --color-primary: var(--color-blue-500)       │
│    --space-component-gap: var(--space-4)        │
│    --text-body: var(--font-size-base)           │
├─────────────────────────────────────────────────┤
│            Component Tokens                      │
│    (Component-specific)                         │
│    --button-bg: var(--color-primary)            │
│    --card-padding: var(--space-component-gap)   │
│    --input-font: var(--text-body)               │
└─────────────────────────────────────────────────┘
```

### Primitive Tokens

```css
:root {
  /* Colors - Brand */
  --color-primary-50: #EFF6FF;
  --color-primary-100: #DBEAFE;
  --color-primary-200: #BFDBFE;
  --color-primary-300: #93C5FD;
  --color-primary-400: #60A5FA;
  --color-primary-500: #3B82F6;
  --color-primary-600: #2563EB;
  --color-primary-700: #1D4ED8;
  --color-primary-800: #1E40AF;
  --color-primary-900: #1E3A8A;

  /* Colors - Neutral */
  --color-gray-50: #F9FAFB;
  --color-gray-100: #F3F4F6;
  --color-gray-200: #E5E7EB;
  --color-gray-300: #D1D5DB;
  --color-gray-400: #9CA3AF;
  --color-gray-500: #6B7280;
  --color-gray-600: #4B5563;
  --color-gray-700: #374151;
  --color-gray-800: #1F2937;
  --color-gray-900: #111827;

  /* Colors - Semantic */
  --color-success-500: #22C55E;
  --color-warning-500: #F59E0B;
  --color-error-500: #EF4444;
  --color-info-500: #3B82F6;

  /* Spacing */
  --space-0: 0px;
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 20px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;

  /* Typography */
  --font-size-xs: 12px;
  --font-size-sm: 14px;
  --font-size-base: 16px;
  --font-size-lg: 18px;
  --font-size-xl: 20px;
  --font-size-2xl: 24px;
  --font-size-3xl: 30px;
  --font-size-4xl: 36px;

  /* Font Weights */
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;

  /* Border Radius */
  --radius-sm: 4px;
  --radius-md: 6px;
  --radius-lg: 8px;
  --radius-xl: 12px;
  --radius-2xl: 16px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1);
  --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1);
}
```

### Semantic Tokens

```css
:root {
  /* Colors - Semantic */
  --color-background: var(--color-gray-50);
  --color-surface: #FFFFFF;
  --color-surface-hover: var(--color-gray-50);
  --color-border: var(--color-gray-200);
  --color-border-strong: var(--color-gray-300);

  /* Text */
  --color-text-primary: var(--color-gray-900);
  --color-text-secondary: var(--color-gray-600);
  --color-text-muted: var(--color-gray-400);
  --color-text-inverse: #FFFFFF;

  /* Interactive */
  --color-primary: var(--color-primary-600);
  --color-primary-hover: var(--color-primary-700);
  --color-primary-active: var(--color-primary-800);
  --color-primary-muted: var(--color-primary-50);

  /* Status */
  --color-success: var(--color-success-500);
  --color-warning: var(--color-warning-500);
  --color-error: var(--color-error-500);
  --color-info: var(--color-info-500);

  /* Spacing - Component */
  --space-component-xs: var(--space-1);
  --space-component-sm: var(--space-2);
  --space-component-md: var(--space-4);
  --space-component-lg: var(--space-6);
  --space-component-xl: var(--space-8);

  /* Typography - Semantic */
  --text-body: var(--font-size-sm);
  --text-body-lg: var(--font-size-base);
  --text-caption: var(--font-size-xs);
  --text-heading: var(--font-size-xl);
  --text-subheading: var(--font-size-lg);

  /* Border Radius - Component */
  --radius-button: var(--radius-md);
  --radius-card: var(--radius-lg);
  --radius-input: var(--radius-md);
  --radius-badge: var(--radius-full);
}
```

### Dark Mode Tokens

```css
[data-theme="dark"] {
  /* Backgrounds */
  --color-background: var(--color-gray-900);
  --color-surface: var(--color-gray-800);
  --color-surface-hover: var(--color-gray-700);
  --color-border: var(--color-gray-700);
  --color-border-strong: var(--color-gray-600);

  /* Text */
  --color-text-primary: var(--color-gray-50);
  --color-text-secondary: var(--color-gray-300);
  --color-text-muted: var(--color-gray-500);
  --color-text-inverse: var(--color-gray-900);

  /* Interactive */
  --color-primary: var(--color-primary-500);
  --color-primary-hover: var(--color-primary-400);
  --color-primary-active: var(--color-primary-300);
  --color-primary-muted: var(--color-primary-900);
}
```

---

## Composants SaaS

### Button

```tsx
// Button component pattern
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'ghost' | 'danger';
  size: 'sm' | 'md' | 'lg';
  loading?: boolean;
  disabled?: boolean;
  icon?: React.ReactNode;
  children: React.ReactNode;
}

// Token mapping
const buttonTokens = {
  primary: {
    bg: 'var(--color-primary)',
    text: 'var(--color-text-inverse)',
    hover: 'var(--color-primary-hover)',
    active: 'var(--color-primary-active)',
  },
  secondary: {
    bg: 'transparent',
    text: 'var(--color-primary)',
    border: 'var(--color-primary)',
    hover: 'var(--color-primary-muted)',
  },
  ghost: {
    bg: 'transparent',
    text: 'var(--color-text-secondary)',
    hover: 'var(--color-surface-hover)',
  },
  danger: {
    bg: 'var(--color-error)',
    text: 'var(--color-text-inverse)',
    hover: '#DC2626',
  },
};
```

### Card

```css
.card {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-card);
  padding: var(--space-component-lg);
  transition: box-shadow 0.2s, border-color 0.2s;
}

.card:hover {
  border-color: var(--color-border-strong);
  box-shadow: var(--shadow-md);
}

.card-header {
  margin-bottom: var(--space-component-md);
}

.card-title {
  font-size: var(--text-heading);
  font-weight: var(--font-weight-semibold);
  color: var(--color-text-primary);
}

.card-description {
  font-size: var(--text-body);
  color: var(--color-text-secondary);
  margin-top: var(--space-component-xs);
}
```

### Data Table

```tsx
// SaaS Data Table Pattern
interface Column<T> {
  key: keyof T;
  label: string;
  sortable?: boolean;
  width?: string;
  render?: (value: any, row: T) => React.ReactNode;
}

interface DataTableProps<T> {
  data: T[];
  columns: Column<T>[];
  selectable?: boolean;
  pagination?: {
    page: number;
    pageSize: number;
    total: number;
  };
  onSort?: (key: string, direction: 'asc' | 'desc') => void;
  onSelect?: (rows: T[]) => void;
  onRowClick?: (row: T) => void;
  actions?: DataTableAction[];
}
```

**Table Tokens :**
```css
.data-table {
  --table-header-bg: var(--color-surface);
  --table-row-hover: var(--color-surface-hover);
  --table-border: var(--color-border);
  --table-selected-bg: var(--color-primary-muted);
}

.data-table th {
  background: var(--table-header-bg);
  font-weight: var(--font-weight-medium);
  font-size: var(--text-caption);
  color: var(--color-text-secondary);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.data-table tr:hover {
  background: var(--table-row-hover);
}

.data-table tr.selected {
  background: var(--table-selected-bg);
}
```

### Input / Form

```css
.input {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-input);
  padding: var(--space-component-sm) var(--space-component-md);
  font-size: var(--text-body);
  color: var(--color-text-primary);
  transition: border-color 0.2s, box-shadow 0.2s;
}

.input:focus {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px var(--color-primary-muted);
}

.input:disabled {
  background: var(--color-surface-hover);
  color: var(--color-text-muted);
  cursor: not-allowed;
}

.input-error {
  border-color: var(--color-error);
}

.input-error:focus {
  box-shadow: 0 0 0 3px rgba(239, 68, 68, 0.1);
}

.input-label {
  display: block;
  font-size: var(--text-body);
  font-weight: var(--font-weight-medium);
  color: var(--color-text-primary);
  margin-bottom: var(--space-component-xs);
}

.input-helper {
  font-size: var(--text-caption);
  color: var(--color-text-secondary);
  margin-top: var(--space-component-xs);
}

.input-error-message {
  font-size: var(--text-caption);
  color: var(--color-error);
  margin-top: var(--space-component-xs);
}
```

### Navigation (Sidebar)

```css
.sidebar {
  width: var(--sidebar-width, 240px);
  background: var(--color-surface);
  border-right: 1px solid var(--color-border);
  display: flex;
  flex-direction: column;
  transition: width 0.2s;
}

.sidebar.collapsed {
  width: var(--sidebar-width-collapsed, 64px);
}

.sidebar-item {
  display: flex;
  align-items: center;
  gap: var(--space-component-sm);
  padding: var(--space-component-sm) var(--space-component-md);
  border-radius: var(--radius-button);
  color: var(--color-text-secondary);
  text-decoration: none;
  transition: background 0.2s, color 0.2s;
}

.sidebar-item:hover {
  background: var(--color-surface-hover);
  color: var(--color-text-primary);
}

.sidebar-item.active {
  background: var(--color-primary-muted);
  color: var(--color-primary);
  font-weight: var(--font-weight-medium);
}
```

### Badge / Status

```css
.badge {
  display: inline-flex;
  align-items: center;
  padding: var(--space-component-xs) var(--space-component-sm);
  border-radius: var(--radius-badge);
  font-size: var(--text-caption);
  font-weight: var(--font-weight-medium);
}

.badge-success {
  background: rgba(34, 197, 94, 0.1);
  color: var(--color-success);
}

.badge-warning {
  background: rgba(245, 158, 11, 0.1);
  color: var(--color-warning);
}

.badge-error {
  background: rgba(239, 68, 68, 0.1);
  color: var(--color-error);
}

.badge-info {
  background: rgba(59, 130, 246, 0.1);
  color: var(--color-info);
}

.badge-neutral {
  background: var(--color-gray-100);
  color: var(--color-text-secondary);
}
```

### Command Palette

```css
.command-palette {
  position: fixed;
  inset: 0;
  z-index: 50;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding-top: 20vh;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
}

.command-palette-content {
  width: 100%;
  max-width: 560px;
  background: var(--color-surface);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-xl);
  overflow: hidden;
}

.command-palette-input {
  width: 100%;
  padding: var(--space-component-md) var(--space-component-lg);
  border: none;
  border-bottom: 1px solid var(--color-border);
  font-size: var(--text-body-lg);
  background: transparent;
  color: var(--color-text-primary);
}

.command-palette-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: var(--space-component-sm) var(--space-component-lg);
  cursor: pointer;
}

.command-palette-item:hover,
.command-palette-item.active {
  background: var(--color-surface-hover);
}
```

---

## Intégration Tailwind

### tailwind.config.js

```javascript
module.exports = {
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        primary: {
          50: 'var(--color-primary-50)',
          100: 'var(--color-primary-100)',
          200: 'var(--color-primary-200)',
          300: 'var(--color-primary-300)',
          400: 'var(--color-primary-400)',
          500: 'var(--color-primary-500)',
          600: 'var(--color-primary-600)',
          700: 'var(--color-primary-700)',
          800: 'var(--color-primary-800)',
          900: 'var(--color-primary-900)',
        },
        surface: {
          DEFAULT: 'var(--color-surface)',
          hover: 'var(--color-surface-hover)',
        },
      },
      spacing: {
        'component-xs': 'var(--space-component-xs)',
        'component-sm': 'var(--space-component-sm)',
        'component-md': 'var(--space-component-md)',
        'component-lg': 'var(--space-component-lg)',
        'component-xl': 'var(--space-component-xl)',
      },
      borderRadius: {
        button: 'var(--radius-button)',
        card: 'var(--radius-card)',
        input: 'var(--radius-input)',
        badge: 'var(--radius-badge)',
      },
    },
  },
};
```

---

## Multi-Tenant Theming

### Tenant Theme Override

```css
/* Tenant-specific overrides */
[data-tenant="acme"] {
  --color-primary: #FF5722;
  --color-primary-hover: #E64A19;
  --color-primary-active: #D84315;
  --radius-card: 4px; /* Acme prefers sharp corners */
}

[data-tenant="globex"] {
  --color-primary: #4CAF50;
  --color-primary-hover: #43A047;
  --color-primary-active: #388E3C;
  --font-family-base: 'Inter', sans-serif;
}
```

### White-Label Pattern

```typescript
// Tenant theme loader
async function loadTenantTheme(tenantId: string) {
  const theme = await fetch(`/api/tenants/${tenantId}/theme`);
  const css = await theme.text();
  
  const style = document.createElement('style');
  style.textContent = css;
  document.head.appendChild(style);
}
```

---

## Dark Mode Implementation

### Toggle Component

```tsx
function ThemeToggle() {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  useEffect(() => {
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      {theme === 'light' ? '🌙' : '☀️'}
    </button>
  );
}
```

### System Preference Detection

```css
@media (prefers-color-scheme: dark) {
  :root:not([data-theme]) {
    /* Auto dark mode based on system preference */
    --color-background: var(--color-gray-900);
    --color-surface: var(--color-gray-800);
    /* ... */
  }
}
```

---

## Spacing Scale

| Token | Value | Usage |
|-------|-------|-------|
| `--space-0` | 0px | Reset |
| `--space-1` | 4px | Tight spacing (icons, badges) |
| `--space-2` | 8px | Compact spacing (inline elements) |
| `--space-3` | 12px | Small spacing (form elements) |
| `--space-4` | 16px | Base spacing (component padding) |
| `--space-5` | 20px | Medium spacing |
| `--space-6` | 24px | Default spacing (card padding) |
| `--space-8` | 32px | Large spacing (section gaps) |
| `--space-10` | 40px | XL spacing |
| `--space-12` | 48px | 2XL spacing |
| `--space-16` | 64px | Page margins |

---

## Typography Scale

| Token | Size | Usage |
|-------|------|-------|
| `--text-caption` | 12px | Captions, helper text |
| `--text-body` | 14px | Body text, form inputs |
| `--text-body-lg` | 16px | Default body, search input |
| `--text-subheading` | 18px | Section subheadings |
| `--text-heading` | 20px | Card titles |
| `--text-2xl` | 24px | Page titles |
| `--text-3xl` | 30px | Hero headings |
| `--text-4xl` | 36px | Marketing headlines |

---

## Scripts de Validation

### Token Validator

```bash
# Check for hardcoded values
grep -rn "#[0-9A-Fa-f]\{6\}" src/
grep -rn "rgb\|rgba" src/

# Should return 0 matches (all values should use tokens)
```

### Component Audit

```bash
# Find components not using tokens
grep -rn "style={" src/components/ | grep -v "var(--"

# Should return 0 matches
```

---

## References

| Resource | URL |
|----------|-----|
| Tailwind CSS | https://tailwindcss.com |
| shadcn/ui | https://ui.shadcn.com |
| Radix UI | https://www.radix-ui.com |
| CSS Variables | https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties |

---

## Quick Reference: Design System Checklist

### Tokens
- [ ] Primitive tokens defined (colors, spacing, typography)
- [ ] Semantic tokens created
- [ ] Component tokens mapped
- [ ] Dark mode tokens included
- [ ] Tenant override system ready

### Components
- [ ] Button (primary, secondary, ghost, danger)
- [ ] Card (with header, body, footer)
- [ ] Data Table (sortable, selectable, paginated)
- [ ] Input / Form (with validation)
- [ ] Navigation (sidebar, breadcrumbs)
- [ ] Badge / Status indicators
- [ ] Command Palette

### Integration
- [ ] Tailwind config with CSS variables
- [ ] Dark mode support (class + system preference)
- [ ] Tenant theming capability
- [ ] Export tokens for design tools (Figma)

### Documentation
- [ ] Token usage guidelines
- [ ] Component API documentation
- [ ] Examples for each variant
- [ ] Accessibility guidelines
