# Lab 11 — Route Guards & Data Resolution

| | |
|---|---|
| **Module** | 11 — Route Guards & Data Resolution |
| **Duration** | 75 minutes |
| **Presentation** | `presentations/Module-11-Route-Guards-and-Data-Resolution.pdf` |
| **Prerequisites** | Lab 10 complete |

---

## Learning Objectives

By completing this lab, you will:

- Implement functional `CanActivate` and `CanDeactivate` guards
- Protect routes with authentication and role-based authorization
- Prefetch route data with resolvers
- Apply enterprise auth patterns to the Employee Portal

---

## Exercise 1: Auth Guard — CanActivate

### Step 1.1 — Enhance AuthService

```typescript
@Injectable({ providedIn: 'root' })
export class AuthService {
  private currentUser = signal<{ name: string; role: 'admin' | 'manager' | 'viewer' } | null>(null);

  readonly user = this.currentUser.asReadonly();
  readonly isAuthenticated = computed(() => this.currentUser() !== null);
  readonly isAdmin = computed(() => this.currentUser()?.role === 'admin');

  login(name: string, role: 'admin' | 'manager' | 'viewer'): void {
    this.currentUser.set({ name, role });
  }

  logout(): void {
    this.currentUser.set(null);
  }
}
```

### Step 1.2 — Create authGuard

```typescript
// core/guards/auth.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const authGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  const router = inject(Router);

  if (auth.isAuthenticated()) {
    return true;
  }

  return router.createUrlTree(['/login']);
};
```

### Step 1.3 — Apply to protected routes

```typescript
{
  path: 'employees',
  canActivate: [authGuard],
  loadChildren: () => import('./employees/employees.routes').then(m => m.EMPLOYEE_ROUTES),
},
{
  path: 'settings',
  canActivate: [authGuard],
  loadComponent: () => import('./settings/settings.component').then(m => m.SettingsComponent),
},
```

---

## Exercise 2: Role Guard — Authorization

### Step 2.1 — Create roleGuard factory

```typescript
export function roleGuard(allowedRoles: string[]): CanActivateFn {
  return () => {
    const auth = inject(AuthService);
    const router = inject(Router);
    const user = auth.user();

    if (user && allowedRoles.includes(user.role)) {
      return true;
    }

    return router.createUrlTree(['/dashboard']);
  };
}
```

### Step 2.2 — Protect admin-only routes

```typescript
{
  path: 'employees/new',
  canActivate: [authGuard, roleGuard(['admin', 'manager'])],
  loadComponent: () => import('./employee-form.component').then(m => m.EmployeeFormComponent),
},
{
  path: 'settings',
  canActivate: [authGuard, roleGuard(['admin'])],
  loadComponent: () => import('./settings/settings.component').then(m => m.SettingsComponent),
},
```

---

## Exercise 3: Login Page

### Step 3.1 — Generate login component

```bash
ng generate component auth/login --standalone
```

Simple form: name + role dropdown. On submit:

```typescript
onLogin(): void {
  this.authService.login(this.name, this.role);
  this.router.navigate(['/dashboard']);
}
```

Route:

```typescript
{ path: 'login', loadComponent: () => import('./auth/login.component').then(m => m.LoginComponent) },
```

### Step 3.2 — Redirect authenticated users away from login

```typescript
export const guestGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  const router = inject(Router);
  return auth.isAuthenticated() ? router.createUrlTree(['/dashboard']) : true;
};

{ path: 'login', canActivate: [guestGuard], loadComponent: ... }
```

---

## Exercise 4: CanDeactivate — Unsaved Changes

### Step 4.1 — Create deactivate guard

```typescript
export interface CanComponentDeactivate {
  canDeactivate: () => boolean | Observable<boolean>;
}

export const unsavedChangesGuard: CanDeactivateFn<CanComponentDeactivate> = (component) => {
  return component.canDeactivate ? component.canDeactivate() : true;
};
```

### Step 4.2 — Implement in employee form

```typescript
export class EmployeeFormComponent implements CanComponentDeactivate {
  private formDirty = toSignal(/* ... */);

  canDeactivate(): boolean {
    if (!this.formDirty()) return true;
    return confirm('You have unsaved changes. Leave anyway?');
  }
}
```

Apply:

```typescript
{ path: 'new', canDeactivate: [unsavedChangesGuard], /* ... */ }
```

---

## Exercise 5: Route Resolver — Prefetch Employee

### Step 5.1 — Create resolver

```typescript
export const employeeResolver: ResolveFn<Employee | null> = (route) => {
  const service = inject(EmployeeService);
  const id = Number(route.paramMap.get('id'));
  return of(service.getById(id) ?? null);
};
```

### Step 5.2 — Apply to detail route

```typescript
{
  path: ':id',
  resolve: { employee: employeeResolver },
  loadComponent: () => import('./employee-detail.component').then(m => m.EmployeeDetailComponent),
}
```

### Step 5.3 — Consume resolved data

With `withComponentInputBinding()` in `app.config.ts`:

```typescript
provideRouter(routes, withComponentInputBinding())
```

In `employee-detail`:

```typescript
employee = input<Employee | null>(); // bound from resolved data
```

Or read from `ActivatedRoute.snapshot.data['employee']`.

---

## Exercise 6: Route Data & Breadcrumbs

### Step 6.1 — Static route data

```typescript
{
  path: 'dashboard',
  data: { title: 'Dashboard', breadcrumb: 'Home' },
  loadComponent: () => import('./dashboard/dashboard.component').then(m => m.DashboardComponent),
},
```

### Step 6.2 — Breadcrumb component

```bash
ng generate component shared/breadcrumb --standalone
```

Walk `ActivatedRoute` tree and collect `data['breadcrumb']` values.

---

## Exercise 7: Auth Integration in Header

Update header to use `AuthService`:

```html
@if (authService.isAuthenticated()) {
  <span>{{ authService.user()?.name }} ({{ authService.user()?.role }})</span>
  <button (click)="logout()">Logout</button>
} @else {
  <a routerLink="/login">Login</a>
}
```

---

## Verification Checklist

- [ ] Unauthenticated users redirected to `/login`
- [ ] `roleGuard` blocks unauthorized access to settings and add-employee
- [ ] Login page works and redirects to dashboard
- [ ] `guestGuard` prevents logged-in users from seeing login page
- [ ] `unsavedChangesGuard` prompts before leaving dirty form
- [ ] `employeeResolver` prefetches employee before detail renders
- [ ] Header reflects auth state from `AuthService`

---

## Stretch Goals

1. Store auth token in `sessionStorage` and restore on refresh.
2. Implement `canMatch` guard for lazy route protection.
3. Async resolver that simulates API delay with loading indicator.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Guard not firing | Ensure `canActivate` is on correct route level |
| Infinite redirect loop | Check login route is NOT protected by `authGuard` |
| Resolver data undefined | Verify key matches: `resolve: { employee: resolver }` |

---

## Day 4 Lab Summary

Your portal is now a multi-page SPA with authentication, authorization, and data prefetching.

**Next:** Day 5 — **[Lab 12 — HTTP Communication & API Integration](Module-12-HTTP-Communication-and-API-Integration.md)**
