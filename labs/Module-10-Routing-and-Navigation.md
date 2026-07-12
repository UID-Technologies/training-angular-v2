# Lab 10 — Routing & Navigation

| | |
|---|---|
| **Module** | 10 — Routing & Navigation |
| **Duration** | 90 minutes |
| **Presentation** | `presentations/Module-10-Routing-and-Navigation.pdf` |
| **Prerequisites** | Lab 09 complete |

---

## Learning Objectives

By completing this lab, you will:

- Configure feature routes for the Employee Portal
- Use route parameters and query parameters
- Implement nested child routes with shared layouts
- Lazy load feature areas and configure route-level providers

---

## Exercise 1: Feature Route Configuration

### Step 1.1 — Define routes

Update `app.routes.ts`:

```typescript
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', redirectTo: 'dashboard', pathMatch: 'full' },
  {
    path: 'dashboard',
    loadComponent: () => import('./dashboard/dashboard.component').then(m => m.DashboardComponent),
  },
  {
    path: 'employees',
    loadChildren: () => import('./employees/employees.routes').then(m => m.EMPLOYEE_ROUTES),
  },
  {
    path: 'departments',
    loadComponent: () => import('./departments/department-list.component').then(m => m.DepartmentListComponent),
  },
  {
    path: 'settings',
    loadComponent: () => import('./settings/settings.component').then(m => m.SettingsComponent),
  },
  { path: '**', loadComponent: () => import('./shared/not-found.component').then(m => m.NotFoundComponent) },
];
```

### Step 1.2 — Employee child routes

Create `employees/employees.routes.ts`:

```typescript
export const EMPLOYEE_ROUTES: Routes = [
  { path: '', loadComponent: () => import('./employee-list.component').then(m => m.EmployeeListComponent) },
  { path: 'new', loadComponent: () => import('./employee-form.component').then(m => m.EmployeeFormComponent) },
  { path: ':id', loadComponent: () => import('./employee-detail.component').then(m => m.EmployeeDetailComponent) },
];
```

---

## Exercise 2: RouterLink Navigation

### Step 2.1 — Update sidebar

```html
@for (item of navItems; track item.path) {
  <a [routerLink]="item.path" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: item.exact }">
    {{ item.label }}
  </a>
}
```

```typescript
navItems = [
  { label: 'Dashboard', path: '/dashboard', exact: true },
  { label: 'Employees', path: '/employees', exact: false },
  { label: 'Departments', path: '/departments', exact: true },
  { label: 'Settings', path: '/settings', exact: true },
];
```

Import `RouterLink` and `RouterLinkActive`.

---

## Exercise 3: Route Parameters

### Step 3.1 — Read :id in employee detail

```typescript
import { ActivatedRoute } from '@angular/router';
import { toSignal } from '@angular/core/rxjs-interop';
import { map } from 'rxjs';

private route = inject(ActivatedRoute);

employeeId = toSignal(
  this.route.paramMap.pipe(map(params => Number(params.get('id')))),
  { initialValue: 0 }
);

employee = computed(() => this.employeeService.getById(this.employeeId()));
```

### Step 3.2 — Navigate to detail

In employee list:

```typescript
private router = inject(Router);

viewEmployee(id: number): void {
  this.router.navigate(['/employees', id]);
}
```

---

## Exercise 4: Query Parameters

### Step 4.1 — Filter via URL

```typescript
department = toSignal(
  this.route.queryParamMap.pipe(map(p => p.get('department'))),
  { initialValue: null }
);

status = toSignal(
  this.route.queryParamMap.pipe(map(p => p.get('status'))),
  { initialValue: null }
);
```

### Step 4.2 — Update query params on filter change

```typescript
filterByDepartment(dept: string | null): void {
  this.router.navigate([], {
    relativeTo: this.route,
    queryParams: { department: dept },
    queryParamsHandling: 'merge',
  });
}
```

### Step 4.3 — Apply filters

```typescript
filteredEmployees = computed(() => {
  let list = this.employeeService.employees();
  const dept = this.department();
  const status = this.status();
  if (dept) list = list.filter(e => e.department === dept);
  if (status) list = list.filter(e => e.status === status);
  return list;
});
```

Verify URL updates: `/employees?department=Engineering&status=active`

---

## Exercise 5: Nested Routes with Shared Layout

### Step 5.1 — Employee shell layout

```bash
ng generate component employees/employee-shell --standalone
```

```html
<!-- employee-shell.component.html -->
<nav class="employee-tabs">
  <a routerLink="/employees" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: true }">List</a>
  <a routerLink="/employees/new" routerLinkActive="active">Add New</a>
</nav>
<router-outlet />
```

### Step 5.2 — Nest routes

```typescript
export const EMPLOYEE_ROUTES: Routes = [
  {
    path: '',
    loadComponent: () => import('./employee-shell.component').then(m => m.EmployeeShellComponent),
    children: [
      { path: '', loadComponent: () => import('./employee-list.component').then(m => m.EmployeeListComponent) },
      { path: 'new', loadComponent: () => import('./employee-form.component').then(m => m.EmployeeFormComponent) },
      { path: ':id', loadComponent: () => import('./employee-detail.component').then(m => m.EmployeeDetailComponent) },
    ],
  },
];
```

---

## Exercise 6: Programmatic Navigation

### Step 6.1 — After form save

```typescript
onSubmit(): void {
  // ... save logic
  this.router.navigate(['/employees']);
}
```

### Step 6.2 — Relative navigation

```typescript
this.router.navigate(['../'], { relativeTo: this.route }); // back to list from detail
```

### Step 6.3 — Navigation with state

```typescript
this.router.navigate(['/employees', id], { state: { fromList: true } });
```

Read in detail: `const state = this.router.getCurrentNavigation()?.extras.state`.

---

## Exercise 7: Lazy Loading Verification

### Step 7.1 — Confirm lazy chunks

1. Run `ng build`
2. Inspect `dist/` — verify separate chunks for `employees`, `dashboard`, etc.
3. Open DevTools Network tab — navigate between routes and confirm chunks load on demand

### Step 7.2 — Route-level providers (preview)

```typescript
{
  path: 'reports',
  loadComponent: () => import('./reports/reports.component').then(m => m.ReportsComponent),
  providers: [ReportConfigService],
}
```

`ReportConfigService` is only available within the reports route subtree.

---

## Verification Checklist

- [ ] Routes configured for dashboard, employees, departments, settings
- [ ] Sidebar uses `routerLink` and `routerLinkActive`
- [ ] Employee detail reads `:id` route parameter
- [ ] List filters sync with query parameters in URL
- [ ] Employee feature uses nested child routes with shell layout
- [ ] Form save navigates programmatically
- [ ] 404 page shown for unknown routes
- [ ] Lazy loading confirmed in build output

---

## Stretch Goals

1. Add pagination via query param `?page=2`.
2. Implement `withComponentInputBinding()` for route params as inputs.
3. Add breadcrumb component reading route data.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Blank page after navigation | Check `router-outlet` exists in parent |
| Lazy load fails | Verify `loadComponent` import path and export name |
| Query params not updating | Use `queryParamsHandling: 'merge'` |

---

## Next Lab

**[Lab 11 — Route Guards & Data Resolution](Module-11-Route-Guards-and-Data-Resolution.md)**
