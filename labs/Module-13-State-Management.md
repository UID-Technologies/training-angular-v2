# Lab 13 — State Management

| | |
|---|---|
| **Module** | 13 — State Management |
| **Duration** | 75 minutes |
| **Presentation** | `presentations/Module-13-State-Management.pdf` |
| **Prerequisites** | Lab 12 complete |

---

## Learning Objectives

By completing this lab, you will:

- Design component, service, and signal-based application state
- Implement a lightweight Employee Store with clear actions
- Compare Signals vs Services vs NgRx for enterprise scenarios
- Apply the right state strategy per use case

---

## Exercise 1: State Audit

### Step 1.1 — Inventory current state

Create `docs/state-audit.md` listing all state in your app:

| State | Location | Scope | Type |
|-------|----------|-------|------|
| Employee list | EmployeeService | Global | Signal |
| Selected employee | EmployeeSelectionService | Global | Signal |
| Auth user | AuthService | Global | Signal |
| Form dirty | EmployeeFormComponent | Local | Signal |
| Search query | EmployeeListComponent | Local | Signal |

### Step 1.2 — Classify state

Label each as:

- **Local UI state** — stays in component
- **Shared feature state** — service with signals
- **Global app state** — root-level store

---

## Exercise 2: Employee Store (Signal-Based)

### Step 2.1 — Create store

```bash
ng generate service core/store/employee-store --standalone
```

```typescript
interface EmployeeState {
  employees: Employee[];
  loading: boolean;
  error: string | null;
  selectedId: number | null;
}

const initialState: EmployeeState = {
  employees: [],
  loading: false,
  error: null,
  selectedId: null,
};

@Injectable({ providedIn: 'root' })
export class EmployeeStore {
  private readonly state = signal<EmployeeState>(initialState);
  private api = inject(EmployeeApiService);

  // Selectors
  readonly employees = computed(() => this.state().employees);
  readonly loading = computed(() => this.state().loading);
  readonly error = computed(() => this.state().error);
  readonly selectedEmployee = computed(() => {
    const { employees, selectedId } = this.state();
    return selectedId ? employees.find(e => e.id === selectedId) ?? null : null;
  });
  readonly activeCount = computed(() =>
    this.employees().filter(e => e.status === 'active').length
  );

  // Actions
  loadEmployees(): void {
    this.patch({ loading: true, error: null });
    this.api.getAll().pipe(
      tap(employees => this.patch({ employees, loading: false })),
      catchError(() => {
        this.patch({ loading: false, error: 'Failed to load' });
        return EMPTY;
      }),
    ).subscribe();
  }

  selectEmployee(id: number | null): void {
    this.patch({ selectedId: id });
  }

  addEmployee(employee: Omit<Employee, 'id'>): void {
    this.api.create(employee).pipe(
      tap(created => this.patch({
        employees: [...this.state().employees, created],
      })),
    ).subscribe();
  }

  removeEmployee(id: number): void {
    this.api.delete(id).pipe(
      tap(() => this.patch({
        employees: this.state().employees.filter(e => e.id !== id),
        selectedId: this.state().selectedId === id ? null : this.state().selectedId,
      })),
    ).subscribe();
  }

  private patch(partial: Partial<EmployeeState>): void {
    this.state.update(s => ({ ...s, ...partial }));
  }
}
```

### Step 2.2 — Migrate components

Replace direct `EmployeeService` usage with `EmployeeStore` in list, detail, and dashboard.

---

## Exercise 3: UI State vs Domain State

### Step 3.1 — Extract UiStore

```typescript
@Injectable({ providedIn: 'root' })
export class UiStore {
  sidebarCollapsed = signal(false);
  theme = signal<'light' | 'dark'>('light');

  toggleSidebar(): void {
    this.sidebarCollapsed.update(v => !v);
  }

  setTheme(theme: 'light' | 'dark'): void {
    this.theme.set(theme);
    document.body.dataset['theme'] = theme;
  }
}
```

### Step 3.2 — Rule

- **Domain state** (employees, departments) → `EmployeeStore`
- **UI state** (sidebar, theme, modals) → `UiStore`
- Never mix them in one store

---

## Exercise 4: Service-Based vs Signal Store

### Step 4.1 — Compare approaches

Document in `docs/state-audit.md`:

| Approach | When to Use | Example in Portal |
|----------|-------------|-------------------|
| Component signals | Local, ephemeral UI | Form dirty, tab selection |
| Service + signals | Shared feature state | EmployeeStore, AuthService |
| BehaviorSubject + Observable | Legacy interop, event streams | Auth guard async checks |
| NgRx Store | Large teams, complex undo/time-travel | Not needed for this app |

### Step 4.2 — NgRx awareness (discussion)

When would you add NgRx?

- 10+ developers on same feature
- Complex state transitions with audit requirements
- Need Redux DevTools time-travel debugging

For this training app, **signal-based store is sufficient**.

---

## Exercise 5: Store Design Patterns

### Step 5.1 — Facade pattern (optional)

```typescript
@Injectable({ providedIn: 'root' })
export class EmployeeFacade {
  private store = inject(EmployeeStore);

  readonly employees = this.store.employees;
  readonly loading = this.store.loading;
  readonly selected = this.store.selectedEmployee;

  load(): void { this.store.loadEmployees(); }
  select(id: number): void { this.store.selectEmployee(id); }
  create(emp: Omit<Employee, 'id'>): void { this.store.addEmployee(emp); }
}
```

Components inject only `EmployeeFacade` — store internals hidden.

### Step 5.2 — Immutable updates

Verify all state updates use spread operator or `patch` — never mutate arrays in place:

```typescript
// Wrong
this.state().employees.push(newEmployee);

// Correct
this.patch({ employees: [...this.state().employees, newEmployee] });
```

---

## Exercise 6: Derived Dashboard State

Connect dashboard to store selectors:

```html
<div class="stat-card">
  <h3>Active Employees</h3>
  <p>{{ employeeStore.activeCount() }}</p>
</div>
<div class="stat-card">
  <h3>Total</h3>
  <p>{{ employeeStore.employees().length }}</p>
</div>
```

Remove duplicate hardcoded stats from Day 2.

---

## Verification Checklist

- [ ] `state-audit.md` documents all application state
- [ ] `EmployeeStore` manages employees, loading, error, selection
- [ ] `UiStore` manages sidebar and theme separately
- [ ] Components use store selectors — no duplicate employee arrays
- [ ] All state updates are immutable
- [ ] Dashboard reads live data from store
- [ ] State strategy comparison documented

---

## Stretch Goals

1. Persist `UiStore.theme` to `localStorage` with an `effect`.
2. Add undo for delete: keep `lastDeleted` signal for 5 seconds.
3. Sketch NgRx equivalent (actions, reducer, effects) in comments.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Stale UI after update | Ensure `patch` creates new array references |
| Duplicate state in services | Consolidate into single store; deprecate old service |
| Circular DI | Store should not inject components |

---

## Next Lab

**[Lab 14 — Angular Material & Performance Optimization](Module-14-Angular-Material-and-Performance-Optimization.md)**
