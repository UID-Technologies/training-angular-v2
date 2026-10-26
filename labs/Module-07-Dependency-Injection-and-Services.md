# Lab 07 — Dependency Injection & Services

| | |
|---|---|
| **Module** | 7 — Dependency Injection & Services |
| **Duration** | 75 minutes |
| **Presentation** | `presentations/Module-07-Dependency-Injection-and-Services.pdf` |
| **Prerequisites** | Day 2 labs complete |

---

## Learning Objectives

By completing this lab, you will:

- Create injectable services with proper provider scope
- Use the `inject()` function for dependency injection
- Configure injection tokens for environment-specific values
- Apply service design patterns for the Employee Portal

---

## Exercise 1: Employee Data Service

### Step 1.1 — Generate service

```bash
ng generate service core/services/employee --standalone
```

### Step 1.2 — Implement with signals

```typescript
import { Injectable, signal, computed } from '@angular/core';
import { Employee } from '../../models/employee.model';

@Injectable({ providedIn: 'root' })
export class EmployeeService {
  private readonly _employees = signal<Employee[]>(this.getSeedData());
  readonly employees = this._employees.asReadonly();

  readonly count = computed(() => this._employees().length);

  getById(id: number): Employee | undefined {
    return this._employees().find(e => e.id === id);
  }

  add(employee: Employee): void {
    this._employees.update(list => [...list, employee]);
  }

  update(id: number, changes: Partial<Employee>): void {
    this._employees.update(list =>
      list.map(e => e.id === id ? { ...e, ...changes } : e)
    );
  }

  remove(id: number): void {
    this._employees.update(list => list.filter(e => e.id !== id));
  }

  private getSeedData(): Employee[] {
    return [ /* your 5+ sample employees */ ];
  }
}
```

### Step 1.3 — Inject into components

Refactor `employee-list` and `dashboard` to use `EmployeeService` instead of local arrays:

```typescript
private employeeService = inject(EmployeeService);
employees = this.employeeService.employees;
```

---

## Exercise 2: Department Service

### Step 2.1 — Create DepartmentService

```bash
ng generate service core/services/department --standalone
```

Manage department list, headcount per department, and `getByName(name: string)`.

### Step 2.2 — Cross-service dependency

`DepartmentService` injects `EmployeeService` to compute headcounts:

```typescript
private employeeService = inject(EmployeeService);

headcountByDepartment = computed(() => {
  const counts: Record<string, number> = {};
  for (const emp of this.employeeService.employees()) {
    counts[emp.department] = (counts[emp.department] ?? 0) + 1;
  }
  return counts;
});
```

---

## Exercise 3: inject() API

### Step 3.1 — Refactor constructor injection

Convert any constructor-based injection to `inject()`:

**Before:**

```typescript
constructor(private employeeService: EmployeeService) {}
```

**After:**

```typescript
private employeeService = inject(EmployeeService);
```

Apply across `employee-list`, `dashboard`, and `employee-detail`.

### Step 3.2 — Inject in functional context

Create a utility function (for learning):

```typescript
// core/utils/employee.utils.ts
import { inject } from '@angular/core';
import { EmployeeService } from '../services/employee.service';

export function getActiveEmployeeCount(): number {
  const service = inject(EmployeeService);
  return service.employees().filter(e => e.status === 'active').length;
}
```

> **Note:** `inject()` only works during injection context (constructor, field initializer, factory). Do not call from arbitrary functions.

---

## Exercise 4: Injection Tokens

### Step 4.1 — API configuration token

Create `core/tokens/api-config.token.ts`:

```typescript
import { InjectionToken } from '@angular/core';

export interface ApiConfig {
  baseUrl: string;
  timeout: number;
}

export const API_CONFIG = new InjectionToken<ApiConfig>('API_CONFIG');
```

### Step 4.2 — Provide in app.config.ts

```typescript
import { API_CONFIG } from './core/tokens/api-config.token';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    { provide: API_CONFIG, useValue: { baseUrl: 'http://localhost:3000/api', timeout: 5000 } },
  ],
};
```

### Step 4.3 — Consume in a service

```typescript
private config = inject(API_CONFIG);

get baseUrl(): string {
  return this.config.baseUrl;
}
```

Add a `console.log(this.baseUrl)` in `EmployeeService` constructor body — you will use this in Lab 12.

---

## Exercise 5: Provider Scope

### Step 5.1 — Component-level provider

Create `WizardStateService` (simple counter):

```typescript
@Injectable()
export class WizardStateService {
  step = signal(1);
  next(): void { this.step.update(s => s + 1); }
}
```

Provide at component level in a new `employee-wizard` component:

```typescript
@Component({
  providers: [WizardStateService],
  // ...
})
```

Verify two wizard instances maintain independent state.

### Step 5.2 — Document scope decision

In code comments, note when to use:

- `providedIn: 'root'` — shared singleton (EmployeeService)
- Component `providers` — isolated instance (WizardStateService)

---

## Exercise 6: Notification Service

```bash
ng generate service core/services/notification --standalone
```

```typescript
@Injectable({ providedIn: 'root' })
export class NotificationService {
  private readonly _messages = signal<string[]>([]);
  readonly messages = this._messages.asReadonly();

  success(message: string): void {
    this._messages.update(m => [...m, message]);
    setTimeout(() => this.dismiss(message), 3000);
  }

  dismiss(message: string): void {
    this._messages.update(m => m.filter(msg => msg !== message));
  }
}
```

Call `notificationService.success('Employee deleted')` from delete handler.

---

## Verification Checklist

- [ ] `EmployeeService` is single source of truth for employee data
- [ ] Components use `inject()` instead of constructor DI
- [ ] `API_CONFIG` token provided in `app.config.ts` and consumed
- [ ] `DepartmentService` depends on `EmployeeService` via DI
- [ ] Component-scoped provider demonstrated with wizard
- [ ] `NotificationService` shows feedback on CRUD actions

---

## Stretch Goals

1. Create `LoggerService` with `log`, `warn`, `error` and inject into all services.
2. Use `useFactory` provider for `API_CONFIG` reading from environment file.
3. Add `APP_INITIALIZER` that logs app version on startup.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `inject() must be called from injection context` | Move call to constructor or field initializer |
| Service returns stale data | Ensure components read signals with `()` |
| Token not found | Register provider in `app.config.ts` |

---

## Next Lab

**[Lab 08 — Forms in Angular](Module-08-Forms-in-Angular.md)**
