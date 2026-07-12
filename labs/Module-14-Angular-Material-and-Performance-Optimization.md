# Lab 14 — Angular Material & Performance Optimization

| | |
|---|---|
| **Module** | 14 — Angular Material & Performance Optimization |
| **Duration** | 90 minutes |
| **Presentation** | `presentations/Module-14-Angular-Material-and-Performance-Optimization.pdf` |
| **Prerequisites** | Lab 13 complete |

---

## Learning Objectives

By completing this lab, you will:

- Integrate Angular Material into the Employee Portal
- Build data tables, dialogs, and navigation with Material components
- Apply `OnPush` change detection for performance
- Use `@defer` for deferred loading of heavy UI blocks

---

## Exercise 1: Install Angular Material

```bash
ng add @angular/material
```

Choose a theme (e.g., Indigo/Pink or custom). Enable typography and animations.

Verify `provideAnimationsAsync()` is in `app.config.ts`.

---

## Exercise 2: Material Navigation & Layout

### Step 2.1 — Toolbar header

Replace custom header with Material toolbar:

```typescript
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatIconModule } from '@angular/material/icon';
```

```html
<mat-toolbar color="primary">
  <button mat-icon-button (click)="uiStore.toggleSidebar()">
    <mat-icon>menu</mat-icon>
  </button>
  <span>Employee Management Portal</span>
  <span class="spacer"></span>
  @if (authService.isAuthenticated()) {
    <span>{{ authService.user()?.name }}</span>
    <button mat-button (click)="logout()">Logout</button>
  }
</mat-toolbar>
```

### Step 2.2 — Sidenav layout

```html
<mat-sidenav-container>
  <mat-sidenav [opened]="!uiStore.sidebarCollapsed()" mode="side">
    <mat-nav-list>
      @for (item of navItems; track item.path) {
        <a mat-list-item [routerLink]="item.path" routerLinkActive="active">
          <mat-icon matListItemIcon>{{ item.icon }}</mat-icon>
          <span matListItemTitle>{{ item.label }}</span>
        </a>
      }
    </mat-nav-list>
  </mat-sidenav>
  <mat-sidenav-content>
    <router-outlet />
  </mat-sidenav-content>
</mat-sidenav-container>
```

---

## Exercise 3: Material Data Table

### Step 3.1 — Employee table

```typescript
import { MatTableModule } from '@angular/material/table';
import { MatSortModule, Sort } from '@angular/material/sort';
import { MatPaginatorModule } from '@angular/material/paginator';

displayedColumns = ['id', 'name', 'department', 'role', 'status', 'actions'];
```

```html
<table mat-table [dataSource]="employeeStore.employees()" matSort>
  <ng-container matColumnDef="name">
    <th mat-header-cell *matHeaderCellDef mat-sort-header>Name</th>
    <td mat-cell *matCellDef="let emp">{{ emp.name }}</td>
  </ng-container>
  <!-- Additional columns -->
  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns" (click)="select(row.id)"></tr>
</table>
```

> Use `@for` compatible approach or `MatTableDataSource` with signal updates.

### Step 3.2 — Status chips

```html
<mat-chip [class]="'status-' + emp.status">{{ emp.status | employeeStatusLabel }}</mat-chip>
```

Import `MatChipsModule`.

---

## Exercise 4: Dialogs

### Step 4.1 — Delete confirmation dialog

```bash
ng generate component shared/dialogs/confirm-dialog --standalone
```

```typescript
export class ConfirmDialogComponent {
  data = inject<{ title: string; message: string }>(MAT_DIALOG_DATA);
  confirmed = output<boolean>();
}
```

```typescript
deleteEmployee(id: number): void {
  const dialogRef = this.dialog.open(ConfirmDialogComponent, {
    data: { title: 'Delete Employee', message: 'This action cannot be undone.' },
  });

  dialogRef.afterClosed().subscribe(confirmed => {
    if (confirmed) this.employeeStore.removeEmployee(id);
  });
}
```

Import `MatDialogModule` and provide `MatDialog` via `inject(MatDialog)`.

---

## Exercise 5: Material Form Controls

Refactor `employee-form` to use:

- `mat-form-field` + `matInput` for text fields
- `mat-select` for department and status
- `mat-error` for validation messages
- `mat-datepicker` for hire date (optional)

```html
<mat-form-field appearance="outline">
  <mat-label>Full Name</mat-label>
  <input matInput formControlName="name" />
  @if (form.get('name')?.hasError('required')) {
    <mat-error>Name is required</mat-error>
  }
</mat-form-field>
```

---

## Exercise 6: OnPush Change Detection

### Step 6.1 — Apply OnPush

Add to presentational components:

```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})
export class EmployeeCardComponent { }
```

Apply to: `employee-card`, `card`, `panel`, `stat-card`.

### Step 6.2 — Verify signal compatibility

OnPush works seamlessly with signal inputs — no `ChangeDetectorRef.markForCheck()` needed when using signals.

### Step 6.3 — Measure impact

1. Open Angular DevTools → Profiler
2. Record interaction (search, select employee)
3. Compare component check count before/after OnPush

---

## Exercise 7: Deferred Loading with @defer

### Step 7.1 — Defer reports chart

In dashboard, wrap a heavy section:

```html
@defer (on viewport) {
  <app-employee-chart />
} @placeholder {
  <div class="chart-skeleton">Loading chart...</div>
} @loading (minimum 500ms) {
  <mat-spinner diameter="40" />
} @error {
  <p>Failed to load chart</p>
}
```

### Step 7.2 — Defer admin panel

```html
@defer (when isAdmin()) {
  <app-admin-panel />
} @placeholder {
  <p>Admin tools load when authorized</p>
}
```

Generate stub `employee-chart` component with simulated heavy init (2s delay).

---

## Exercise 8: Theming

### Step 8.1 — Custom theme tokens

In `styles.scss`:

```scss
@use '@angular/material' as mat;

$employee-portal-primary: mat.m2-define-palette(mat.$m2-indigo-palette, 700);
$employee-portal-accent: mat.m2-define-palette(mat.$m2-teal-palette, A200);

$employee-portal-theme: mat.m2-define-light-theme((
  color: (primary: $employee-portal-primary, accent: $employee-portal-accent),
));

@include mat.all-component-themes($employee-portal-theme);
```

### Step 8.2 — Dark mode toggle

Wire `UiStore.setTheme()` to add/remove `.dark-theme` class on `<body>`.

---

## Verification Checklist

- [ ] Angular Material installed with theme
- [ ] Toolbar, sidenav, and nav list implemented
- [ ] Employee data displayed in Material table
- [ ] Delete confirmation uses Material dialog
- [ ] Employee form uses Material form fields
- [ ] OnPush applied to presentational components
- [ ] `@defer` loads chart on viewport entry
- [ ] Theme toggle switches light/dark mode

---

## Stretch Goals

1. Add `MatSnackBar` for notifications instead of custom toast.
2. Implement drag-and-drop reorder with `cdkDrag` on table rows.
3. Virtual scroll with `cdk-virtual-scroll-viewport` for 1000+ employees.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Material styles missing | Verify theme import in `styles.scss` |
| Table not rendering | Check `MatTableModule` imported; data source has items |
| OnPush component not updating | Ensure inputs are signals or use immutable data |

---

## Next Lab

**[Lab 15 — Accessibility in Angular](Module-15-Accessibility-in-Angular.md)**
