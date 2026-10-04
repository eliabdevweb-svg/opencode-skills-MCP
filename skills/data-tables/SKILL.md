---
name: data-tables
description: "Conception de tableaux de donnees: tri, filtrage, pagination, selection, colonnes, etats vides et performance. Use when building or improving data tables, grids, sorting, filtering, or table UX."
---

# Data Tables Skill

## Overview
Expert in designing and building complex data tables for admin panels, back-office interfaces, and data-heavy applications. Covers sorting, filtering, pagination, bulk actions, inline editing, and all table states.

## Table Architecture

### 1. Core Structure
```typescript
interface DataTableConfig<T> {
  // Data
  data: T[];
  columns: ColumnDef<T>[];
  
  // Features
  sorting?: SortingState;
  filtering?: FilterState;
  pagination?: PaginationState;
  selection?: SelectionState;
  
  // States
  loading?: boolean;
  error?: string | null;
  empty?: { title: string; description: string; action?: Action };
  
  // Actions
  bulkActions?: BulkAction<T>[];
  rowActions?: RowAction<T>[];
  export?: ExportConfig;
}
```

### 2. Column Definitions
```typescript
import { createColumnHelper } from '@tanstack/react-table';

const columnHelper = createColumnHelper<Order>();

const columns = [
  columnHelper.accessor('id', {
    header: 'Order ID',
    cell: (info) => `#${info.getValue()}`,
    size: 100,
    enableSorting: true,
  }),
  columnHelper.accessor('customer.name', {
    header: 'Customer',
    cell: (info) => (
      <div className="flex items-center gap-2">
        <Avatar src={info.row.original.customer.avatar} />
        <span>{info.getValue()}</span>
      </div>
    ),
  }),
  columnHelper.accessor('status', {
    header: 'Status',
    cell: (info) => (
      <Badge variant={statusVariant[info.getValue()]}>
        {info.getValue()}
      </Badge>
    ),
    filterFn: 'equals',
  }),
  columnHelper.accessor('amount', {
    header: 'Amount',
    cell: (info) => formatCurrency(info.getValue()),
    // Tabular numerals, right-aligned
    meta: { align: 'right', tabular: true },
  }),
  columnHelper.accessor('createdAt', {
    header: 'Date',
    cell: (info) => formatDate(info.getValue()),
    filterFn: 'dateRange',
  }),
  // Actions column
  columnHelper.display({
    id: 'actions',
    size: 80,
    cell: (info) => (
      <DropdownMenu>
        <DropdownMenuTrigger asChild>
          <Button variant="ghost" size="icon">
            <MoreHorizontal className="h-4 w-4" />
          </Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent>
          <DropdownMenuItem>View</DropdownMenuItem>
          <DropdownMenuItem>Edit</DropdownMenuItem>
          <DropdownMenuItem className="text-destructive">
            Delete
          </DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>
    ),
  }),
];
```

### 3. Sorting
```typescript
// Multi-column sorting
const [sorting, setSorting] = useState<SortingState>([
  { id: 'createdAt', desc: true }
]);

// Sort indicators
const sortIcons = {
  asc: <ArrowUp className="h-4 w-4" />,
  desc: <ArrowDown className="h-4 w-4" />,
  none: <ArrowUpDown className="h-4 w-4 opacity-50" />,
};

// Numeric sorting (tabular figures)
{
  accessorFn: (row) => row.amount,
  sortingFn: 'basic', // or custom numeric sort
}
```

### 4. Filtering
```typescript
// Filter types
const filterTypes = {
  text: TextInput,
  select: SelectFilter,
  multiSelect: MultiSelectFilter,
  dateRange: DateRangePicker,
  numberRange: NumberRangeInput,
  boolean: ToggleFilter,
};

// Filter UI pattern (above table)
function TableFilters({ filters, onChange }) {
  return (
    <div className="flex items-center gap-2 mb-4">
      {filters.map(filter => (
        <FilterChip
          key={filter.id}
          filter={filter}
          onRemove={() => onChange(filter.id, null)}
        />
      ))}
      <Popover>
        <PopoverTrigger asChild>
          <Button variant="outline" size="sm">
            <Filter className="h-4 w-4 mr-2" />
            Add Filter
          </Button>
        </PopoverTrigger>
        <FilterPanel filters={availableFilters} onSelect={onChange} />
      </Popover>
    </div>
  );
}
```

### 5. Pagination
```typescript
// Server-side pagination
interface PaginationState {
  pageIndex: number;  // 0-based
  pageSize: number;
}

// Pagination UI
function TablePagination({ 
  total, 
  pageIndex, 
  pageSize, 
  onPageChange 
}: PaginationProps) {
  const totalPages = Math.ceil(total / pageSize);
  
  return (
    <div className="flex items-center justify-between px-2 py-4">
      <span className="text-sm text-muted-foreground">
        {total} total results
      </span>
      <div className="flex items-center gap-2">
        <Button
          variant="outline"
          size="sm"
          onClick={() => onPageChange(pageIndex - 1)}
          disabled={pageIndex === 0}
        >
          Previous
        </Button>
        <span className="text-sm">
          Page {pageIndex + 1} of {totalPages}
        </span>
        <Button
          variant="outline"
          size="sm"
          onClick={() => onPageChange(pageIndex + 1)}
          disabled={pageIndex >= totalPages - 1}
        >
          Next
        </Button>
      </div>
    </div>
  );
}
```

### 6. Bulk Actions
```typescript
// Selection state
const [rowSelection, setRowSelection] = useState({});

// Bulk action bar
function BulkActionBar({ selectedCount, actions, onClear }) {
  if (selectedCount === 0) return null;
  
  return (
    <div className="fixed bottom-4 left-1/2 -translate-x-1/2 
                    bg-primary text-primary-foreground 
                    rounded-lg shadow-lg px-4 py-3 
                    flex items-center gap-4 z-50">
      <span className="font-medium">{selectedCount} selected</span>
      <Separator orientation="vertical" className="h-6" />
      {actions.map(action => (
        <Button
          key={action.id}
          variant={action.variant}
          size="sm"
          onClick={action.onClick}
        >
          <action.icon className="h-4 w-4 mr-2" />
          {action.label}
        </Button>
      ))}
      <Button variant="ghost" size="sm" onClick={onClear}>
        <X className="h-4 w-4" />
      </Button>
    </div>
  );
}
```

### 7. Inline Editing
```typescript
// Editable cell component
function EditableCell({ getValue, row, column, table }) {
  const initialValue = getValue();
  const [value, setValue] = useState(initialValue);
  const [isEditing, setIsEditing] = useState(false);

  const onBlur = () => {
    table.options.meta?.updateData(row.index, column.id, value);
    setIsEditing(false);
  };

  if (isEditing) {
    return (
      <Input
        value={value}
        onChange={(e) => setValue(e.target.value)}
        onBlur={onBlur}
        autoFocus
        className="h-8"
      />
    );
  }

  return (
    <div
      onClick={() => setIsEditing(true)}
      className="cursor-pointer hover:bg-muted rounded px-2 py-1"
    >
      {value}
    </div>
  );
}
```

### 8. Table States

```typescript
// Loading state
function TableLoading({ rows = 5, columns = 4 }) {
  return (
    <Table>
      <TableHeader>
        <TableRow>
          {Array.from({ length: columns }).map((_, i) => (
            <TableHead key={i}>
              <Skeleton className="h-4 w-20" />
            </TableHead>
          ))}
        </TableRow>
      </TableHeader>
      <TableBody>
        {Array.from({ length: rows }).map((_, i) => (
          <TableRow key={i}>
            {Array.from({ length: columns }).map((_, j) => (
              <TableCell key={j}>
                <Skeleton className="h-4 w-full" />
              </TableCell>
            ))}
          </TableRow>
        ))}
      </TableBody>
    </Table>
  );
}

// Error state
function TableError({ message, onRetry }) {
  return (
    <div className="flex flex-col items-center justify-center py-12">
      <AlertCircle className="h-12 w-12 text-destructive" />
      <h3 className="mt-4 text-lg font-semibold">Something went wrong</h3>
      <p className="mt-2 text-sm text-muted-foreground">{message}</p>
      <Button onClick={onRetry} className="mt-4">
        Try Again
      </Button>
    </div>
  );
}

// Empty state
function TableEmpty({ title, description, action }) {
  return (
    <div className="flex flex-col items-center justify-center py-12">
      <Inbox className="h-12 w-12 text-muted-foreground" />
      <h3 className="mt-4 text-lg font-semibold">{title}</h3>
      <p className="mt-2 text-sm text-muted-foreground">{description}</p>
      {action && <Button className="mt-4">{action.label}</Button>}
    </div>
  );
}
```

### 9. Responsive Tables
```typescript
// Desktop: Full table
// Tablet: Condensed table
// Mobile: Card-based layout

function ResponsiveTable({ data, columns }) {
  const isMobile = useMediaQuery('(max-width: 768px)');
  
  if (isMobile) {
    return (
      <div className="space-y-4">
        {data.map((row, i) => (
          <Card key={i}>
            <CardHeader>
              <div className="flex justify-between items-center">
                <span className="font-medium">{row.name}</span>
                <Badge>{row.status}</Badge>
              </div>
            </CardHeader>
            <CardContent>
              <dl className="grid grid-cols-2 gap-2 text-sm">
                {columns.slice(0, 4).map(col => (
                  <div key={col.id}>
                    <dt className="text-muted-foreground">{col.header}</dt>
                    <dd>{col.accessor(row)}</dd>
                  </div>
                ))}
              </dl>
            </CardContent>
          </Card>
        ))}
      </div>
    );
  }
  
  return <DataTable data={data} columns={columns} />;
}
```

### 10. Export Functionality
```typescript
// Export options
const exportOptions = [
  { label: 'Export as CSV', icon: FileDown, format: 'csv' },
  { label: 'Export as Excel', icon: FileSpreadsheet, format: 'xlsx' },
  { label: 'Export as PDF', icon: FileText, format: 'pdf' },
];

// Export function
async function exportData(data, columns, format) {
  const headers = columns.map(c => c.header);
  const rows = data.map(row => 
    columns.map(c => c.accessor(row))
  );
  
  switch (format) {
    case 'csv':
      return downloadCSV(headers, rows);
    case 'xlsx':
      return downloadExcel(headers, rows);
    case 'pdf':
      return downloadPDF(headers, rows);
  }
}
```

## Performance Optimization
- **Virtual scrolling** for 1000+ rows
- **Server-side** sorting/filtering/pagination for large datasets
- **Debounced** search input
- **Memoized** column definitions
- **Lazy loaded** row details

## Accessibility
- Keyboard navigation (arrow keys for rows, tab for actions)
- ARIA labels for sort buttons
- Screen reader announcements for selection count
- Focus management for bulk action bar
