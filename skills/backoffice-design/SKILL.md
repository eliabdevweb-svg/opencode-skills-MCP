---
name: backoffice-design
description: "Design d'interfaces back-office, admin panels et dashboards internes. Use when building admin panels, internal tools, back-office UI, or management dashboards."
---

# Back-Office & Admin Dashboard Design Skill

## Overview
Expert in designing operational admin panels, back-office interfaces, and business dashboards for SaaS applications. Focus on action-oriented UI, RBAC, data density, and workflow efficiency.

## Key Principles

### 1. Admin Panel vs Dashboard
| | Admin Panel | Dashboard |
|---|---|---|
| **Purpose** | Manage and control state | Visualize and communicate data |
| **User** | Internal operators, support, finance | Stakeholders, managers |
| **Interaction** | Deep, multi-step (CRUD, configure) | Shallow (filter, drill-down) |
| **Data** | Editable tables, forms, actions | Charts, graphs, KPI cards |
| **Consequence** | Every action affects real data | Read-only |

### 2. Layout Patterns

**Fixed Sidebar (Default)**
```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ Logo    Search              User  Bell  â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚ Dashboardâ”‚                              â”‚
â”‚ Users    â”‚    Main Content Area         â”‚
â”‚ Orders   â”‚    (Cards, Tables, Forms)    â”‚
â”‚ Products â”‚                              â”‚
â”‚ Settings â”‚                              â”‚
â”‚          â”‚                              â”‚
â”‚          â”‚                              â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```
Best for: Internal tools, CRUD-heavy admins, deep navigation

**Top Navigation (Linear-style)**
```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ Logo   Users   Orders   Settings  User  â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚                                         â”‚
â”‚         Focused Work Surface            â”‚
â”‚         (Minimal chrome, dense)         â”‚
â”‚                                         â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```
Best for: Product tools with few sections, keyboard-centric users

**Table-First (Stripe-style)**
```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ Orders              Filters    Export   â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚ [Chart: Summary above table]            â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚ ID â”‚ Customer â”‚ Amount â”‚ Status â”‚ Date  â”‚
â”‚ 1  â”‚ John     â”‚ $120   â”‚ Paid   â”‚ Sep 8 â”‚
â”‚ 2  â”‚ Jane     â”‚ $85    â”‚ Pendingâ”‚ Sep 7 â”‚
â”‚ ...â”‚ ...      â”‚ ...    â”‚ ...    â”‚ ...   â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```
Best for: Transactions, orders, records â€” scan rows and drill in

**Panel Grid (Grafana-style)**
```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ [Panel 1]â”‚ [Panel 2]â”‚ [Panel 3]â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚ [Panel 4]â”‚ [Panel 5]â”‚ [Panel 6]â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```
Best for: Ops, monitoring, TV dashboards â€” glanceable metrics

### 3. Information Hierarchy

**3-Level Disclosure:**
- **Level 1 (Primary)**: Key metrics, critical alerts, most-used actions â€” visible immediately
- **Level 2 (Secondary)**: Filters, configuration, detailed tables â€” 1 interaction away
- **Level 3 (Tertiary)**: Audit logs, historical data, advanced settings â€” on demand

**The 5-Second Test**: User should see the one status they opened the dashboard for within 5 seconds. If not, hierarchy is the problem.

### 4. Navigation Design

```typescript
// Navigation structure example
const navigation = [
  {
    label: 'Overview',
    items: [
      { label: 'Dashboard', icon: 'LayoutDashboard', href: '/dashboard' },
      { label: 'Analytics', icon: 'BarChart3', href: '/analytics' },
    ]
  },
  {
    label: 'Management',
    items: [
      { label: 'Users', icon: 'Users', href: '/users', badge: 'new' },
      { label: 'Orders', icon: 'ShoppingCart', href: '/orders' },
      { label: 'Products', icon: 'Package', href: '/products' },
    ]
  },
  {
    label: 'System',
    items: [
      { label: 'Settings', icon: 'Settings', href: '/settings' },
      { label: 'Audit Log', icon: 'FileText', href: '/audit' },
    ]
  }
];
```

**Navigation Rules:**
- Group by user goal, not data type
- Use nouns, not verbs ("Users" not "Manage Users")
- Highlight active section persistently
- Keep critical actions in consistent position
- 3-5 top-level groups max, collapse rest

### 5. Data Tables (Critical Component)

```typescript
// Table with all states
interface DataTableProps {
  data: T[];
  columns: ColumnDef<T>[];
  loading?: boolean;
  error?: string;
  emptyTitle?: string;
  emptyDescription?: string;
  filters?: FilterConfig[];
  bulkActions?: BulkAction[];
  pagination?: PaginationState;
}

// Essential features
const tableFeatures = {
  sorting: true,
  filtering: true,
  pagination: true, // Use pagination, not infinite scroll
  columnResize: true,
  inlineEdit: true,
  bulkSelect: true,
  rowExpand: true, // For details
  export: true,
};
```

**Table Design Rules:**
- Tabular numerals (right-aligned numbers)
- Muted gridlines, no heavy borders
- Status as colored chips/badges
- 44x44px minimum hit area for actions
- Design with overflow: long strings, empty cells, 200+ rows
- Always include: loading, error, zero-results states

### 6. Forms & Actions

```typescript
// Form best practices
const formPatterns = {
  // Inline validation
  validation: 'onBlur', // or 'onChange' for immediate feedback
  
  // Smart defaults
  defaults: {
    status: 'active',
    role: 'viewer',
  },
  
  // Confirmation for destructive actions
  destructive: {
    delete: 'This will permanently delete 3 workspaces and 214 users.',
    requireReason: true,
  },
  
  // Success feedback
  feedback: {
    success: 'toast', // or 'inline'
    error: 'inline',
  }
};
```

### 7. RBAC-Aware UI

```typescript
// Permission-based component rendering
function PermissionGate({ 
  permission, 
  children, 
  fallback 
}: PermissionGateProps) {
  const { can } = usePermissions();
  
  if (!can(permission)) {
    return fallback ?? null; // Hide or disable
  }
  return children;
}

// Usage
<PermissionGate permission="edit_users">
  <Button>Edit User</Button>
</PermissionGate>

// Permission flag, not role name
// Bad:  if (role === 'admin')
// Good: if (can('edit_users'))
```

**Hide vs Disable Decision:**
- **Hide**: Feature user should never know exists
- **Disable**: Feature user should know about but can't access yet (e.g., upgrade prompt)

### 8. Empty & Loading States

```typescript
// Empty state component
function EmptyState({ 
  icon, 
  title, 
  description, 
  action 
}: EmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-12">
      <Icon name={icon} className="h-12 w-12 text-muted-foreground" />
      <h3 className="mt-4 text-lg font-semibold">{title}</h3>
      <p className="mt-2 text-sm text-muted-foreground">{description}</p>
      {action && <Button className="mt-4">{action.label}</Button>}
    </div>
  );
}

// Skeleton loader
function TableSkeleton({ rows = 5, columns = 4 }) {
  return (
    <div className="space-y-3">
      {Array.from({ length: rows }).map((_, i) => (
        <div key={i} className="flex space-x-4">
          {Array.from({ length: columns }).map((_, j) => (
            <Skeleton key={j} className="h-4 flex-1" />
          ))}
        </div>
      ))}
    </div>
  );
}
```

### 9. Color & Theming

**Light Mode Rules:**
- Off-white (#fafafa) beats pure white for large surfaces
- Pure white for content cards only
- Shadows, not borders, for elevation
- Desaturate status colors slightly

**Dark Mode Rules:**
- Lighter = closer (inverse elevation)
- High-contrast numerals
- Reserve red/green for loss/gain only
- Test contrast at same ratios as light mode

**Color as Signal:**
- Brand color â†’ chrome only, never in data
- Status colors â†’ success/warning/danger/info
- Neutral â†’ text, borders, backgrounds

### 10. Responsive Behavior

```typescript
// Breakpoint strategy for admin
const responsiveStrategy = {
  desktop: 'Full sidebar + table layout',
  tablet: 'Collapsed sidebar (icons only) + adapted tables',
  mobile: 'Hamburger menu + card-based layout (not compressed tables)',
};

// Sidebar collapse
@media (max-width: 768px) {
  .sidebar { 
    transform: translateX(-100%);
    position: fixed;
    z-index: 50;
  }
  .sidebar.open { 
    transform: translateX(0); 
  }
}
```

### 11. Keyboard & Focus

```typescript
// Keyboard navigation requirements
const keyboardRequirements = {
  tabOrder: 'Logical, follows visual flow',
  focusVisible: 'Always show focus ring',
  shortcuts: {
    'Ctrl+K': 'Command palette',
    'Ctrl+S': 'Save current form',
    'Escape': 'Close modal/drawer',
    'Delete': 'Delete selected (with confirm)',
  },
  tableNavigation: 'Arrow keys for rows, Tab for actions',
};
```

## Implementation Checklist
- [ ] Navigation grouped by user goal
- [ ] RBAC-aware components (permission flags)
- [ ] Data table with all states (loading, error, empty, overflow)
- [ ] Empty states designed for every blank view
- [ ] Keyboard navigation tested
- [ ] Focus indicators visible
- [ ] Form validation inline
- [ ] Destructive actions confirmed
- [ ] Audit trail for critical actions
- [ ] Responsive at 768px and 1024px breakpoints
