# Shared Repository DataGrid Component - Requirements Specification & Migration Plan

## Table of Contents

1.  [Current State Analysis](#1-current-state-analysis)
2.  [Requirements Specification](#2-requirements-specification)
3.  [Component Architecture](#3-component-architecture)
4.  [Props Interface & Type Definitions](#4-props-interface--type-definitions)
5.  [File Structure & Location](#5-file-structure--location)
6.  [Migration Plan](#6-migration-plan)
7.  [Risk Assessment & Mitigation](#7-risk-assessment--mitigation)
8.  [Testing Strategy](#8-testing-strategy)
9.  [Performance Considerations](#9-performance-considerations)
10. [Future Enhancements](#10-future-enhancements)

---

## 1. Current State Analysis

### 1.1 Existing Table Implementations

The Designer project currently has **4 distinct table implementations**:

### A. **RepositoryTable** (`apps/designer/src/containers/Organizer/RepositoryTable/`)
- **Purpose**: Main workspace/repository file browser
- **Features**:
  - Server-side sorting (title field)
  - Server-side pagination (MUI Pagination component)
  - Drag & drop for folders/items
  - Column resizing with persistent widths
  - Dynamic column visibility (via workspace store)
  - Row selection and highlighting
  - Inline editing (rename functionality)
  - Custom row actions (duplicate, delete, permissions)
  - Breadcrumb navigation (TablePaths)
  - Empty state handling
  - Responsive design with overflow detection
- **Technology**: Material-UI Table, custom Row components
- **State Management**: Redux + Zustand (useMainWorkspaceStore)
- **Styling**: tss-react/mui with theme integration

#### B. **EntityTable** (`libs/shared-ui/src/components/EntityTable/`)
- **Purpose**: Generic entity display table (used in AccountsTable)
- **Features**:
  - Generic type support (`<T>`)
  - Loading state with LinearProgress
  - Row click handlers
  - Row selection
  - Empty state with search message
  - Custom column rendering
  - Configurable cell alignment
- **Technology**: Material-UI Table
- **State Management**: Component-level state
- **Styling**: tss-react/mui

#### C. **BrowseTable** (`libs/shared-ui/src/components/BrowseWindow/`)
- **Purpose**: File/folder browser in modal dialogs
- **Features**:
  - Pagination
  - Row selection
  - Folder navigation
  - Disabled row states (draft items)
  - Tooltips for invalid items
  - Empty state with loading spinner
- **Technology**: Material-UI Table
- **State Management**: Props-based
- **Styling**: tss-react/mui

#### D. **OverviewTable** (`apps/designer/src/components/HeritageControl/`)
- **Purpose**: Heritage control view synchronization table
- **Features**:
  - Client-side sorting
  - Custom column configurations
  - Sortable columns
- **Technology**: Material-UI Table
- **State Management**: Component-level state
- **Styling**: tss-react/mui

### 1.2 Common Patterns

**Shared Functionality Across Tables**:
- ✅ Sorting (client-side & server-side)
- ✅ Pagination (client-side & server-side)
- ✅ Row selection
- ✅ Empty states
- ✅ Loading states
- ✅ Custom cell rendering
- ✅ Column configuration
- ✅ Search/filtering integration
- ✅ Theming consistency

**Unique Features**:
- Drag & drop (RepositoryTable)
- Column resizing (RepositoryTable)
- Inline editing (RepositoryTable)
- Breadcrumb navigation (RepositoryTable)
- Generic type support (EntityTable)

---

## 2. Requirements Specification

### 2.1 Functional Requirements

#### FR-1: Data Display
- **FR-1.1**: Support generic TypeScript types for row data
- **FR-1.2**: Render configurable columns with custom cell renderers
- **FR-1.3**: Support column alignment (left, center, right)
- **FR-1.4**: Support column width configuration (fixed, flex, percentage)
- **FR-1.5**: Display row data with proper type safety

#### FR-2: Sorting
- **FR-2.1**: Support client-side sorting for small datasets
- **FR-2.2**: Support server-side sorting for large datasets
- **FR-2.3**: Allow single-column sorting
- **FR-2.4**: Provide sort direction indicators (asc/desc)
- **FR-2.5**: Support sortable/non-sortable column configuration
- **FR-2.6**: Trigger callbacks on sort change

#### FR-3: Pagination
- **FR-3.1**: Support client-side pagination
- **FR-3.2**: Support server-side pagination
- **FR-3.3**: Configurable page size
- **FR-3.4**: Display total pages and current page
- **FR-3.5**: Trigger callbacks on page change
- **FR-3.6**: Hide pagination when total items < page size

#### FR-4: Search & Filtering
- **FR-4.1**: Accept external search query
- **FR-4.2**: Display search-specific empty states
- **FR-4.3**: Support filter integration via props
- **FR-4.4**: Trigger callbacks on filter change

#### FR-5: Row Interactions
- **FR-5.1**: Support row click handlers
- **FR-5.2**: Support row selection (single/multiple)
- **FR-5.3**: Highlight selected rows
- **FR-5.4**: Support conditional row clickability
- **FR-5.5**: Support row hover states
- **FR-5.6**: Support disabled row states

#### FR-6: Column Management
- **FR-6.1**: Support dynamic column visibility
- **FR-6.2**: Support column resizing (optional)
- **FR-6.3**: Persist column widths to storage
- **FR-6.4**: Support column reordering (future)
- **FR-6.5**: Support sticky columns (future)

#### FR-7: Advanced Features
- **FR-7.1**: Support drag & drop for rows (optional)
- **FR-7.2**: Support inline editing (optional)
- **FR-7.3**: Support row expansion (optional)
- **FR-7.4**: Support custom row actions
- **FR-7.5**: Support breadcrumb navigation integration
- **FR-7.6**: Support folder depth indentation

#### FR-8: Loading & Empty States
- **FR-8.1**: Display loading indicator
- **FR-8.2**: Display empty state with custom message
- **FR-8.3**: Display search-specific empty message
- **FR-8.4**: Support custom empty state icons
- **FR-8.5**: Support loading overlay vs skeleton

#### FR-9: Accessibility
- **FR-9.1**: Support ARIA labels
- **FR-9.2**: Keyboard navigation support
- **FR-9.3**: Screen reader compatibility
- **FR-9.4**: Focus management

### 2.2 Non-Functional Requirements

#### NFR-1: Performance
- **NFR-1.1**: Render 1000+ rows without performance degradation (virtualization)
- **NFR-1.2**: Minimize re-renders using React.memo and useMemo
- **NFR-1.3**: Support lazy loading for large datasets
- **NFR-1.4**: Optimize sort/filter operations

#### NFR-2: Maintainability
- **NFR-2.1**: Clear, documented prop interface
- **NFR-2.2**: Comprehensive TypeScript types
- **NFR-2.3**: Modular, composable architecture
- **NFR-2.4**: Consistent naming conventions

#### NFR-3: Compatibility
- **NFR-3.1**: Compatible with existing Redux store
- **NFR-3.2**: Compatible with Zustand stores
- **NFR-3.3**: Compatible with React Router
- **NFR-3.4**: Compatible with i18next translations
- **NFR-3.5**: Compatible with existing theme system

#### NFR-4: Styling
- **NFR-4.1**: Use tss-react/mui for styling
- **NFR-4.2**: Support theme customization
- **NFR-4.3**: Responsive design support
- **NFR-4.4**: Consistent with existing design system

---

## 3. Component Architecture

### 3.1 Component Hierarchy

```
SharedRepositoryDataGrid/
├── SharedRepositoryDataGrid.tsx          # Main component
├── index.ts                              # Exports
├── types.ts                              # TypeScript definitions
├── hooks/
│   ├── useDataGridState.ts              # State management hook
│   ├── useDataGridSort.ts               # Sorting logic
│   ├── useDataGridPagination.ts         # Pagination logic
│   ├── useDataGridSelection.ts          # Selection logic
│   └── useColumnManager.ts              # Column visibility/resize
├── components/
│   ├── DataGridHeader.tsx               # Table header
│   ├── DataGridBody.tsx                 # Table body
│   ├── DataGridRow.tsx                  # Individual row
│   ├── DataGridCell.tsx                 # Individual cell
│   ├── DataGridPagination.tsx           # Pagination controls
│   ├── DataGridEmptyState.tsx           # Empty state
│   ├── DataGridLoadingOverlay.tsx       # Loading state
│   └── DataGridToolbar.tsx              # Optional toolbar
├── cells/                                # Reusable cell renderers
│   ├── TextCell.tsx
│   ├── ActionCell.tsx
│   ├── StatusCell.tsx
│   ├── DateCell.tsx
│   └── CustomCell.tsx
└── utils/
    ├── sorting.ts                        # Sort utilities
    ├── filtering.ts                      # Filter utilities
    └── helpers.ts                        # General helpers
```

### 3.2 Data Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    Parent Component                          │
│  (e.g., RepositoryView, AccountsTable)                      │
└────────────────────┬────────────────────────────────────────┘
                     │
                     │ Props (data, columns, config)
                     ▼
┌─────────────────────────────────────────────────────────────┐
│           SharedRepositoryDataGrid                           │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  useDataGridState (manages internal state)           │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ DataGrid     │  │ DataGrid     │  │ DataGrid     │     │
│  │ Header       │  │ Body         │  │ Pagination   │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└────────────────────┬────────────────────────────────────────┘
                     │
                     │ Callbacks (onSort, onPageChange, etc.)
                     ▼
┌─────────────────────────────────────────────────────────────┐
│              Parent Component Handlers                       │
│  (Update Redux/Zustand, fetch new data, etc.)               │
└─────────────────────────────────────────────────────────────┘
```

### 3.3 State Management Strategy

**Two-Mode Operation**:

1. **Controlled Mode** (Server-side operations)
   - Parent manages all state
   - Component receives data via props
   - Callbacks notify parent of user actions
   - Parent fetches/updates data

2. **Uncontrolled Mode** (Client-side operations)
   - Component manages internal state
   - Receives full dataset via props
   - Handles sorting/pagination internally
   - No callbacks needed

---

## 4. Props Interface & Type Definitions

### 4.1 Core Types

```typescript
// types.ts

import { ReactNode } from 'react';
import { SxProps, Theme } from '@mui/material';

/**
 * Generic column definition for the data grid
 */
export interface DataGridColumn<T = any> {
  /** Unique identifier for the column */
  id: string;

  /** Column header label */
  header: ReactNode;

  /** Field name in the data object (for default rendering) */
  field?: keyof T;

  /** Custom cell renderer function */
  render?: (row: T, rowIndex: number) => ReactNode;

  /** Column width (number for px, string for %, 'auto', or flex) */
  width?: number | string;

  /** Minimum width in pixels */
  minWidth?: number;

  /** Maximum width in pixels */
  maxWidth?: number;

  /** Flex grow value (for flexible columns) */
  flex?: number;

  /** Cell alignment */
  align?: 'left' | 'center' | 'right';

  /** Header alignment */
  headerAlign?: 'left' | 'center' | 'right';

  /** Whether column is sortable */
  sortable?: boolean;

  /** Whether column is resizable */
  resizable?: boolean;

  /** Whether column is hidden */
  hidden?: boolean;

  /** Custom sort comparator function */
  sortComparator?: (a: T, b: T, direction: 'asc' | 'desc') => number;

  /** Additional cell props */
  cellProps?: {
    sx?: SxProps<Theme>;
    className?: string;
  };

  /** Additional header props */
  headerProps?: {
    sx?: SxProps<Theme>;
    className?: string;
  };
}

/**
 * Pagination configuration
 */
export interface PaginationConfig {
  /** Current page number (1-based) */
  page: number;

  /** Number of items per page */
  pageSize: number;

  /** Total number of items */
  totalItems: number;

  /** Total number of pages */
  totalPages: number;

  /** Whether this is the last page */
  isLastPage?: boolean;

  /** Page size options */
  pageSizeOptions?: number[];

  /** Show page size selector */
  showPageSizeSelector?: boolean;
}

/**
 * Sort configuration
 */
export interface SortConfig {
  /** Field to sort by */
  field: string;

  /** Sort direction */
  direction: 'asc' | 'desc';
}

/**
 * Empty state configuration
 */
export interface EmptyStateConfig {
  /** Icon class name (e.g., 'ri-search-line') */
  iconClassName?: string;

  /** Default empty message */
  defaultMessage: string;

  /** Message when search returns no results */
  searchMessage?: string;

  /** Custom empty state component */
  customComponent?: ReactNode;
}

/**
 * Loading state configuration
 */
export interface LoadingConfig {
  /** Loading state */
  loading: boolean;

  /** Loading type */
  type?: 'overlay' | 'skeleton' | 'spinner';

  /** Custom loading component */
  customComponent?: ReactNode;
}

/**
 * Row selection configuration
 */
export interface SelectionConfig<T = any> {
  /** Selection mode */
  mode?: 'single' | 'multiple' | 'none';

  /** Selected row keys */
  selectedKeys?: React.Key[];

  /** Function to determine if row is selectable */
  isRowSelectable?: (row: T) => boolean;

  /** Callback when selection changes */
  onSelectionChange?: (selectedKeys: React.Key[]) => void;
}

/**
 * Drag and drop configuration
 */
export interface DragDropConfig<T = any> {
  /** Enable drag and drop */
  enabled: boolean;

  /** DnD item type */
  itemType: string;

  /** Function to determine if row is draggable */
  isDraggable?: (row: T) => boolean;

  /** Function to determine if drop is allowed */
  canDrop?: (dragItem: T, dropTarget: T) => boolean;

  /** Callback when item is dropped */
  onDrop?: (dragItem: T, dropTarget: T) => void;

  /** Custom drag handle component */
  dragHandleComponent?: ReactNode;
}

/**
 * Column resize configuration
 */
export interface ColumnResizeConfig {
  /** Enable column resizing */
  enabled: boolean;

  /** Persist column widths to storage */
  persist?: boolean;

  /** Storage key for persisted widths */
  storageKey?: string;

  /** Callback when column is resized */
  onColumnResize?: (columnId: string, width: number) => void;
}

/**
 * Inline editing configuration
 */
export interface InlineEditConfig<T = any> {
  /** Enable inline editing */
  enabled: boolean;

  /** Fields that are editable */
  editableFields?: (keyof T)[];

  /** Function to determine if cell is editable */
  isCellEditable?: (row: T, field: keyof T) => boolean;

  /** Callback when cell value changes */
  onCellValueChange?: (row: T, field: keyof T, newValue: any) => void;

  /** Validation function */
  validateCell?: (row: T, field: keyof T, value: any) => boolean | string;
}
```

### 4.2 Main Component Props

```typescript
/**
 * Main SharedRepositoryDataGrid component props
 */
export interface SharedRepositoryDataGridProps<T = any> {
  // ============ REQUIRED PROPS ============

  /** Array of data rows */
  rows: T[];

  /** Column definitions */
  columns: DataGridColumn<T>[];

  /** Function to extract unique key from row */
  rowKey: (row: T) => React.Key;

  // ============ OPTIONAL PROPS ============

  /** ARIA label for accessibility */
  ariaLabel?: string;

  /** Custom class name */
  className?: string;

  /** Custom styles */
  sx?: SxProps<Theme>;

  // ============ SORTING ============

  /** Sorting mode */
  sortingMode?: 'client' | 'server' | 'none';

  /** Current sort configuration (controlled) */
  sortModel?: SortConfig;

  /** Default sort configuration (uncontrolled) */
  defaultSortModel?: SortConfig;

  /** Callback when sort changes */
  onSortChange?: (sortModel: SortConfig) => void;

  // ============ PAGINATION ============

  /** Pagination mode */
  paginationMode?: 'client' | 'server' | 'none';

  /** Pagination configuration (controlled) */
  paginationConfig?: PaginationConfig;

  /** Default pagination config (uncontrolled) */
  defaultPaginationConfig?: Partial<PaginationConfig>;

  /** Callback when page changes */
  onPageChange?: (page: number) => void;

  /** Callback when page size changes */
  onPageSizeChange?: (pageSize: number) => void;

  // ============ SEARCH & FILTERING ============

  /** Current search query */
  searchQuery?: string;

  /** Filter configuration */
  filters?: Record<string, any>;

  /** Callback when filters change */
  onFiltersChange?: (filters: Record<string, any>) => void;

  // ============ ROW INTERACTIONS ============

  /** Callback when row is clicked */
  onRowClick?: (row: T, event: React.MouseEvent) => void;

  /** Function to determine if row is clickable */
  isRowClickable?: (row: T) => boolean;

  /** Callback when row is double-clicked */
  onRowDoubleClick?: (row: T, event: React.MouseEvent) => void;

  /** Row selection configuration */
  selectionConfig?: SelectionConfig<T>;

  // ============ LOADING & EMPTY STATES ============

  /** Loading configuration */
  loadingConfig?: LoadingConfig;

  /** Empty state configuration */
  emptyStateConfig?: EmptyStateConfig;

  // ============ ADVANCED FEATURES ============

  /** Drag and drop configuration */
  dragDropConfig?: DragDropConfig<T>;

  /** Column resize configuration */
  columnResizeConfig?: ColumnResizeConfig;

  /** Inline editing configuration */
  inlineEditConfig?: InlineEditConfig<T>;

  /** Breadcrumb navigation component */
  breadcrumbComponent?: ReactNode;

  /** Toolbar component */
  toolbarComponent?: ReactNode;

  /** Footer component */
  footerComponent?: ReactNode;

  // ============ STYLING & THEMING ============

  /** Row height in pixels */
  rowHeight?: number;

  /** Header height in pixels */
  headerHeight?: number;

  /** Enable row hover effect */
  enableRowHover?: boolean;

  /** Enable row striping */
  enableRowStriping?: boolean;

  /** Custom row class name function */
  getRowClassName?: (row: T, index: number) => string;

  /** Custom row style function */
  getRowStyle?: (row: T, index: number) => React.CSSProperties;

  // ============ PERFORMANCE ============

  /** Enable virtualization for large datasets */
  enableVirtualization?: boolean;

  /** Virtualization threshold (number of rows) */
  virtualizationThreshold?: number;

  // ============ MISCELLANEOUS ============

  /** Unique identifier for the grid (for storage keys) */
  gridId?: string;

  /** Enable debug mode */
  debug?: boolean;

  /** Custom translations */
  translations?: {
    noData?: string;
    noResults?: string;
    loading?: string;
    page?: string;
    of?: string;
    rowsPerPage?: string;
  };
}
```

### 4.3 Usage Examples

#### Example 1: Basic Usage (Client-side)

```typescript
import { SharedRepositoryDataGrid, DataGridColumn } from '@cg-frontend/shared-ui';

interface User {
  id: string;
  name: string;
  email: string;
  role: string;
}

const MyComponent = () => {
  const users: User[] = [
    { id: '1', name: 'John Doe', email: 'john@example.com', role: 'Admin' },
    { id: '2', name: 'Jane Smith', email: 'jane@example.com', role: 'User' },
  ];

  const columns: DataGridColumn<User>[] = [
    {
      id: 'name',
      header: 'Name',
      field: 'name',
      sortable: true,
      width: 200,
    },
    {
      id: 'email',
      header: 'Email',
      field: 'email',
      sortable: true,
      flex: 1,
    },
    {
      id: 'role',
      header: 'Role',
      render: (user) => <Chip label={user.role} />,
      width: 120,
    },
  ];

  return (
    <SharedRepositoryDataGrid
      rows={users}
      columns={columns}
      rowKey={(user) => user.id}
      sortingMode="client"
      paginationMode="client"
      defaultPaginationConfig={{ pageSize: 10 }}
      emptyStateConfig={{
        defaultMessage: 'No users found',
        iconClassName: 'ri-user-line',
      }}
    />
  );
};
```

#### Example 2: Server-side Operations (Repository Table)

```typescript
import { SharedRepositoryDataGrid } from '@cg-frontend/shared-ui';
import { useMainWorkspaceStore } from '@designer/store/workspaceStore';
import { useDispatch, useSelector } from 'react-redux';

const RepositoryView = () => {
  const dispatch = useDispatch();
  const nodes = useSelector((state) => state.organizer.selectedContent);
  const paginationInfo = useSelector((state) => state.organizer.paginationInfo);
  const workspaceColumns = useMainWorkspaceStore((state) => state.workspaceColumns);
  const searchText = useMainWorkspaceStore((state) => state.workspaceSearchText);
  const filters = useMainWorkspaceStore((state) => state.workspaceFilters);

  const handleSortChange = (sortModel) => {
    dispatch(fetchRepository(repositoryId, page, null, sortModel.direction, searchText, filters));
  };

  const handlePageChange = (page) => {
    dispatch(fetchRepository(repositoryId, page, null, sortDirection, searchText, filters));
  };

  const columns = useRepositoryColumns(workspaceColumns); // Custom hook

  return (
    <SharedRepositoryDataGrid
      rows={nodes}
      columns={columns}
      rowKey={(node) => node.id}
      sortingMode="server"
      sortModel={{ field: 'title', direction: 'asc' }}
      onSortChange={handleSortChange}
      paginationMode="server"
      paginationConfig={{
        page: paginationInfo.pageNumber,
        pageSize: paginationInfo.pageSize,
        totalItems: paginationInfo.totalElements,
        totalPages: paginationInfo.totalPages,
      }}
      onPageChange={handlePageChange}
      searchQuery={searchText}
      filters={filters}
      dragDropConfig={{
        enabled: true,
        itemType: 'WORKSPACE_NODE',
        onDrop: handleDrop,
      }}
      columnResizeConfig={{
        enabled: true,
        persist: true,
        storageKey: 'repository-columns',
      }}
      emptyStateConfig={{
        defaultMessage: t('noData'),
        searchMessage: t('noResults'),
        iconClassName: 'ri-search-line',
      }}
      loadingConfig={{
        loading: isLoading,
        type: 'overlay',
      }}
    />
  );
};
```

#### Example 3: With Inline Editing

```typescript
const EditableTable = () => {
  const [data, setData] = useState(initialData);

  const handleCellValueChange = (row, field, newValue) => {
    setData((prev) =>
      prev.map((item) => (item.id === row.id ? { ...item, [field]: newValue } : item))
    );
  };

  return (
    <SharedRepositoryDataGrid
      rows={data}
      columns={columns}
      rowKey={(row) => row.id}
      inlineEditConfig={{
        enabled: true,
        editableFields: ['name', 'email'],
        onCellValueChange: handleCellValueChange,
        validateCell: (row, field, value) => {
          if (field === 'email') {
            return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) || 'Invalid email';
          }
          return true;
        },
      }}
    />
  );
};
```

---

## 5. File Structure & Location

### 5.1 Recommended Location

**Primary Location**: `libs/shared-ui/src/components/SharedRepositoryDataGrid/`


### 5.2 Complete File Structure

```
libs/shared-ui/src/components/SharedRepositoryDataGrid/
│
├── index.ts                                    # Main exports
├── SharedRepositoryDataGrid.tsx                # Main component (200-300 lines)
├── types.ts                                    # All TypeScript types (150-200 lines)
├── constants.ts                                # Constants and defaults
├── README.md                                   # Component documentation
│
├── hooks/
│   ├── index.ts
│   ├── useDataGridState.ts                    # Central state management
│   ├── useDataGridSort.ts                     # Sorting logic
│   ├── useDataGridPagination.ts               # Pagination logic
│   ├── useDataGridSelection.ts                # Row selection logic
│   ├── useColumnManager.ts                    # Column visibility/resize
│   ├── useDataGridKeyboard.ts                 # Keyboard navigation
│   └── useDataGridVirtualization.ts           # Virtualization (optional)
│
├── components/
│   ├── index.ts
│   ├── DataGridHeader/
│   │   ├── DataGridHeader.tsx                 # Table header
│   │   ├── DataGridHeaderCell.tsx             # Header cell with sort
│   │   ├── ResizeHandle.tsx                   # Column resize handle
│   │   └── index.ts
│   │
│   ├── DataGridBody/
│   │   ├── DataGridBody.tsx                   # Table body
│   │   ├── DataGridRow.tsx                    # Individual row
│   │   ├── DataGridCell.tsx                   # Individual cell
│   │   ├── DraggableRow.tsx                   # Row with DnD
│   │   └── index.ts
│   │
│   ├── DataGridPagination/
│   │   ├── DataGridPagination.tsx             # Pagination controls
│   │   └── index.ts
│   │
│   ├── DataGridEmptyState/
│   │   ├── DataGridEmptyState.tsx             # Empty state
│   │   └── index.ts
│   │
│   ├── DataGridLoadingOverlay/
│   │   ├── DataGridLoadingOverlay.tsx         # Loading overlay
│   │   ├── DataGridSkeleton.tsx               # Skeleton loader
│   │   └── index.ts
│   │
│   └── DataGridToolbar/
│       ├── DataGridToolbar.tsx                # Optional toolbar
│       └── index.ts
│
├── cells/                                      # Reusable cell renderers
│   ├── index.ts
│   ├── TextCell.tsx                           # Basic text cell
│   ├── ActionCell.tsx                         # Action buttons cell
│   ├── StatusCell.tsx                         # Status chip cell
│   ├── DateCell.tsx                           # Formatted date cell
│   ├── UserCell.tsx                           # User avatar + name
│   ├── EditableCell.tsx                       # Inline editable cell
│   └── CustomCell.tsx                         # Generic custom cell
│
├── utils/
│   ├── index.ts
│   ├── sorting.ts                             # Sort utilities
│   ├── filtering.ts                           # Filter utilities
│   ├── pagination.ts                          # Pagination helpers
│   ├── columnHelpers.ts                       # Column utilities
│   └── storage.ts                             # LocalStorage helpers
│
└── __tests__/
    ├── SharedRepositoryDataGrid.test.tsx
    ├── hooks/
    │   ├── useDataGridSort.test.ts
    │   └── useDataGridPagination.test.ts
    └── utils/
        ├── sorting.test.ts
        └── filtering.test.ts
```

### 5.3 Export Configuration

**Update `libs/shared-ui/src/components/index.ts`**:

```typescript
// Add to existing exports
export { default as SharedRepositoryDataGrid } from './SharedRepositoryDataGrid';
export type {
  SharedRepositoryDataGridProps,
  DataGridColumn,
  PaginationConfig,
  SortConfig,
  EmptyStateConfig,
  LoadingConfig,
  SelectionConfig,
  DragDropConfig,
  ColumnResizeConfig,
  InlineEditConfig,
} from './SharedRepositoryDataGrid/types';

// Export cell renderers for custom use
export * from './SharedRepositoryDataGrid/cells';
```

---

## 6. Migration Plan

### 6.1 Migration Phases

#### **Phase 1: Foundation (Days 2)**

**Objectives**:
- Create component structure
- Implement core functionality
- Set up testing infrastructure

**Tasks**:
1. ✅ Create directory structure in `libs/shared-ui/src/components/SharedRepositoryDataGrid/`
2. ✅ Define TypeScript types and interfaces
3. ✅ Implement main component shell
4. ✅ Implement basic hooks (state, sort, pagination)
5. ✅ Create header, body, and cell components
6. ✅ Implement client-side sorting and pagination
7. ✅ Add empty state and loading state


**Deliverables**:
- Working component with basic features

#### **Phase 2: Advanced Features (days 4)**

**Objectives**:
- Add server-side operations support
- Implement row selection
- Add drag & drop support

**Tasks**:
1. ✅ Implement server-side sorting/pagination
2. ✅ Add row selection (single/multiple)
3. ✅ Integrate react-dnd for drag & drop
4. ✅ Implement column resizing
5. ✅ Add column visibility management
6. ✅ Implement storage persistence
7. ✅ Add keyboard navigation
8. ✅ Write integration tests

**Deliverables**:
- Feature-complete component
- Integration tests
- Updated documentation

#### **Phase 3: Migration  (Days 4)**

**Objectives**:
- Replace RepositoryDataGrid with SharedRepositoryDataGrid
- Ensure feature parity

**Tasks**:
1. ✅ Create migration wrapper component (if needed)
2. ✅ Migrate column definitions to new format
3. ✅ Update state management integration
4. ✅ Migrate custom cell renderers
5. ✅ Test all features (sorting, pagination, DnD, etc.)
7. ✅ Remove old RepositoryDataGrid code

8. ✅ Analyze RepositoryTable Row component
9. ✅ Extract reusable cell components
10. ✅ Migrate inline editing functionality
11. ✅ Migrate row actions (duplicate, delete, permissions)
12. ✅ Migrate breadcrumb integration
13. ✅ Update Redux/Zustand integration
14. ✅ Test all features thoroughly
15. ✅ Performance testing with large datasets
16. ✅ Remove old RepositoryTable code
17. ✅ Migrate EntityTable usage in AccountsTable
18. ✅ Migrate BrowseTable in BrowseWindow
19. ✅ Migrate OverviewTable in HeritageControl
20. ✅ Update all imports
21. ✅ Remove old table components
22. ✅ Update documentation

**Validation**:
- All tables migrated successfully
- No old table code remains
- All tests passing

**Deliverables**:
- Optimized component



## 7. Risk Assessment & Mitigation

### 7.1 Identified Risks

#### **RISK-1: Breaking Existing Functionality** 🔴 HIGH

**Description**: Migration may break existing features in RepositoryTable

**Impact**:
- Users unable to browse repositories
- Data loss if inline editing breaks
- Workflow disruption

**Probability**: Medium

**Mitigation**:
1. ✅ Comprehensive testing before migration
2. ✅ Migrate one table at a time

#### **RISK-2: Performance Degradation** 🟡 MEDIUM

**Description**: New component may be slower than existing implementations

**Impact**:
- Slow rendering with large datasets
- Poor user experience
- Increased server load

**Probability**: Low-Medium

**Mitigation**:
1. ✅ Use React.memo and useMemo extensively
2. ✅ Performance benchmarking before migration
3. ✅ Lazy loading for heavy components
4. ✅ Optimize re-renders with proper dependency arrays
5. ✅ Profile with React DevTools

**Benchmarks**:
- Initial render: <500ms for 100 rows
- Scroll performance: 60fps with virtualization
- Sort/filter: <200ms response time

#### **RISK-3: Complex State Management Integration** 🟡 MEDIUM

**Description**: Integration with Redux and Zustand may be complex

**Impact**:
- State synchronization issues
- Bugs in filtering/sorting
- Difficult debugging

**Probability**: Medium

**Mitigation**:
1. ✅ Clear separation of concerns (controlled vs uncontrolled)
2. ✅ Comprehensive prop documentation
3. ✅ Example implementations for each store type

---

#### **RISK-4: Drag & Drop Compatibility** 🟡 MEDIUM

**Description**: react-dnd integration may conflict with existing DnD implementations

**Impact**:
- Broken drag & drop functionality
- Folder organization issues
- User frustration

**Probability**: Low-Medium

**Mitigation**:
1. ✅ Use same react-dnd version as existing code
2. ✅ Test DnD thoroughly with different scenarios
3. ✅ Maintain same DnD item types
4. ✅ Backward compatibility with existing drop handlers
5. ✅ Comprehensive DnD testing

**Contingency**:
- Make DnD optional via config

---

#### **RISK-5: Column Resizing State Persistence** 🟢 LOW

**Description**: Column widths may not persist correctly across sessions

**Impact**:
- User preferences lost
- Inconsistent UI
- Minor annoyance

**Probability**: Low

**Mitigation**:
1. ✅ Use localStorage for persistence
2. ✅ Fallback to defaults if storage fails
3. ✅ Version storage schema
4. ✅ Clear migration path for old storage keys
5. ✅ Test across browsers

**Contingency**:
- Provide reset button
- Document storage structure

---

#### **RISK-6: Accessibility Regression** 🟡 MEDIUM

**Description**: New component may have accessibility issues

**Impact**:
- Non-compliant with WCAG standards
- Poor screen reader experience
- Legal/compliance issues

**Probability**: Low

**Mitigation**:
1. ✅ ARIA labels on all interactive elements
2. ✅ Keyboard navigation support
3. ✅ Screen reader testing
4. ✅ Focus management
5. ✅ Accessibility audit before release
6. ✅ Follow MUI accessibility guidelines

**Contingency**:
- Accessibility expert review
- Fix issues before full rollout

---

#### **RISK-8: Bundle Size Increase** 🟢 LOW

**Description**: New component may increase bundle size

**Impact**:
- Slower page loads
- Increased bandwidth usage
- Poor mobile experience

**Probability**: Low

**Mitigation**:
1. ✅ Tree-shaking support
2. ✅ Code splitting for optional features
3. ✅ Lazy loading for heavy dependencies
4. ✅ Bundle size monitoring
5. ✅ Remove old components after migration


### 7.2 Risk Matrix

| Risk | Probability | Impact | Severity | Mitigation Priority |
|------|-------------|--------|----------|---------------------|
| RISK-1: Breaking Functionality | Medium | High | 🔴 HIGH | P0 - Critical |
| RISK-2: Performance | Low-Medium | Medium | 🟡 MEDIUM | P1 - High |
| RISK-3: State Management | Medium | Medium | 🟡 MEDIUM | P1 - High |
| RISK-4: Drag & Drop | Low-Medium | Medium | 🟡 MEDIUM | P2 - Medium |
| RISK-5: Column Persistence | Low | Low | 🟢 LOW | P3 - Low |
| RISK-6: Accessibility | Low | Medium | 🟡 MEDIUM | P1 - High |
| RISK-7: Learning Curve | Medium | Low | 🟢 LOW | P2 - Medium |
| RISK-8: Bundle Size | Low | Low | 🟢 LOW | P3 - Low |

---

## 8. Testing Strategy

### 8.1 Unit Testing

**Test Categories**:

1. **Component Rendering**
   - Renders with minimal props
   - Renders with all props
   - Renders empty state
   - Renders loading state
   - Renders with different row counts

2. **Sorting**
   - Client-side sorting (asc/desc)
   - Server-side sorting callbacks
   - Custom sort comparators
   - Multi-column sorting (future)

3. **Pagination**
   - Client-side pagination
   - Server-side pagination callbacks
   - Page size changes
   - Edge cases (last page, single page)

4. **Selection**
   - Single row selection
   - Multiple row selection
   - Select all functionality
   - Disabled row selection

5. **Interactions**
   - Row click handlers
   - Cell click handlers
   - Keyboard navigation
   - Drag & drop

6. **Utilities**
   - Sort functions
   - Filter functions
   - Pagination helpers
   - Storage helpers

### 8.4 Accessibility Testing

**Tools**:
- axe-core
- NVDA/JAWS screen readers
- Keyboard-only navigation

### 8.5 Performance Testing

**Metrics**:
- Initial render time
- Re-render time
- Memory usage
- Bundle size


## 9. Performance Considerations

### 9.1 Optimization Techniques

#### **1. Memoization**

**Problem**: Unnecessary re-renders on parent state changes

**Solution**: Use React.memo and useMemo

```typescript
export const DataGridRow = React.memo<DataGridRowProps>(
  ({ row, columns, onClick }) => {
    const cells = useMemo(
      () => columns.map((col) => renderCell(row, col)),
      [row, columns]
    );

    return <TableRow onClick={onClick}>{cells}</TableRow>;
  },
  (prevProps, nextProps) => {
    // Custom comparison
    return (
      prevProps.row === nextProps.row &&
      prevProps.columns === nextProps.columns
    );
  }
);
```

---

#### **2. Debouncing**

**Problem**: Excessive API calls on search/filter

**Solution**: Debounce user input

```typescript
import { useDebouncedCallback } from 'use-debounce';

const handleSearchChange = useDebouncedCallback((value) => {
  onSearchChange(value);
}, 300);
```

---

#### **3. Code Splitting**

**Problem**: Large bundle size

**Solution**: Lazy load optional features

```typescript
const DraggableRow = lazy(() => import('./DraggableRow'));
const EditableCell = lazy(() => import('./EditableCell'));

// Use only when needed
{dragDropConfig?.enabled && (
  <Suspense fallback={<DataGridRow />}>
    <DraggableRow />
  </Suspense>
)}
```

## 10. Future Enhancements (As per the future requirements)

1. **Column Reordering**
   - Drag & drop columns
   - Persist column order
   - Reset to default

2. **Column Pinning**
   - Pin columns to left/right
   - Sticky columns on scroll

3. **Advanced Filtering**
   - Filter builder UI
   - Multiple filter conditions
   - Custom filter operators

4. **Export Functionality**
   - Export to CSV
   - Export to Excel
   - Export to PDF

5. **Row Grouping**
   - Group by column
   - Expandable groups
   - Group aggregations

7. **Context Menu**
   - Right-click actions
   - Custom menu items
   - Keyboard shortcuts


## 11. Conclusion

### 11.1 Summary

This specification outlines a comprehensive plan to create a unified `SharedRepositoryDataGrid` component that will:

✅ **Consolidate** 5 different table implementations into one reusable component
✅ **Improve** maintainability and reduce code duplication
✅ **Enhance** developer experience with clear prop-based API
✅ **Maintain** feature parity with existing implementations
✅ **Optimize** performance with virtualization and memoization
✅ **Ensure** accessibility and WCAG compliance
✅ **Provide** comprehensive testing and documentation

### 11.2 Success Criteria

The migration will be considered successful when:

1. ✅ All 5 table implementations are replaced
2. ✅ No regressions in functionality
3. ✅ Performance benchmarks met or exceeded
4. ✅ Test coverage >85%
5. ✅ Zero critical bugs in production
6. ✅ Positive developer feedback
7. ✅ Documentation complete
8. ✅ Old code removed from codebase

### 11.3 Timeline

**Total Duration**: 12 Days

---

## 12. Appendix

### 12.1 References
- [MUI X DataGrid Documentation](https://mui.com/x/react-data-grid/)
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

### 12.2 Glossary

- **DnD**: Drag and Drop
- **MUI**: Material-UI
- **WCAG**: Web Content Accessibility Guidelines
- **ARIA**: Accessible Rich Internet Applications
---

### Recommendations
If we move forward with this plan, I would suggest reviewing the current table UI and finalizing a design that accommodates all scenarios.
