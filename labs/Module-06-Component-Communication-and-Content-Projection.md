# Lab 06 — Component Communication & Content Projection

| | |
|---|---|
| **Module** | 6 — Component Communication & Content Projection |
| **Duration** | 75 minutes |
| **Presentation** | `presentations/Module-06-Component-Communication-and-Content-Projection.pdf` |
| **Prerequisites** | Labs 01–05 complete |

---

## Learning Objectives

By completing this lab, you will:

- Implement parent-to-child and child-to-parent communication patterns
- Share state across unrelated components via a signal-based service
- Build reusable card and panel components with content projection
- Apply multi-slot projection for flexible layouts

---

## Exercise 1: Parent-to-Child — Employee Detail

### Step 1.1 — Pass data via input

Ensure `employee-detail` receives employee via `input.required<Employee>()`.

Display: name, email, role, department, status badge.

### Step 1.2 — Pass configuration

Add optional `showActions = input(true)` to control action button visibility.

Parent passes `[showActions]="currentUserRole === 'admin'"`.

---

## Exercise 2: Child-to-Parent — Event Communication

### Step 2.1 — Employee detail actions

In `employee-detail`, add outputs:

```typescript
edit = output<Employee>();
delete = output<number>();
```

Template buttons:

```html
@if (showActions()) {
  <button (click)="edit.emit(employee())">Edit</button>
  <button (click)="delete.emit(employee().id)">Delete</button>
}
```

### Step 2.2 — Parent handles events

```typescript
onEdit(employee: Employee): void {
  console.log('Edit requested:', employee.id);
}

onDelete(id: number): void {
  this.employees.update(list => list.filter(e => e.id !== id));
}
```

---

## Exercise 3: Shared State Service

### Step 3.1 — Create EmployeeSelectionService

```bash
ng generate service core/services/employee-selection --standalone
```

```typescript
import { Injectable, signal, computed } from '@angular/core';
import { Employee } from '../../models/employee.model';

@Injectable({ providedIn: 'root' })
export class EmployeeSelectionService {
  private readonly _selectedId = signal<number | null>(null);
  private readonly _employees = signal<Employee[]>([]);

  readonly selectedId = this._selectedId.asReadonly();
  readonly selectedEmployee = computed(() => {
    const id = this._selectedId();
    return this._employees().find(e => e.id === id) ?? null;
  });

  setEmployees(employees: Employee[]): void {
    this._employees.set(employees);
  }

  select(id: number): void {
    this._selectedId.set(id);
  }

  clear(): void {
    this._selectedId.set(null);
  }
}
```

### Step 3.2 — Use in list and detail (sibling communication)

- `employee-list` calls `selectionService.select(id)` on card click
- `employee-detail` reads `selectionService.selectedEmployee()` in template
- Sidebar shows "Selected: {{ name }}" when an employee is selected

Inject with `inject(EmployeeSelectionService)`.

---

## Exercise 4: Single-Slot Content Projection

### Step 4.1 — Create CardComponent

```bash
ng generate component shared/ui/card --standalone
```

```html
<!-- card.component.html -->
<article class="card">
  <ng-content />
</article>
```

```scss
.card {
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 1rem;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
}
```

### Step 4.2 — Wrap dashboard stats

```html
<app-card>
  <h3>Total Employees</h3>
  <p class="stat-value">{{ totalEmployees() }}</p>
</app-card>
```

Refactor all dashboard stat blocks to use `<app-card>`.

---

## Exercise 5: Multi-Slot Content Projection

### Step 5.1 — Create PanelComponent

```bash
ng generate component shared/ui/panel --standalone
```

```html
<section class="panel">
  <header class="panel-header">
    <ng-content select="[panel-header]" />
  </header>
  <div class="panel-body">
    <ng-content />
  </div>
  <footer class="panel-footer">
    <ng-content select="[panel-footer]" />
  </footer>
</section>
```

### Step 5.2 — Employee detail with slots

```html
<app-panel>
  <div panel-header>
    <h2>{{ employee().name }}</h2>
    <span class="badge">{{ employee().role }}</span>
  </div>

  <p>Department: {{ employee().department }}</p>
  <p>Email: {{ employee().email }}</p>

  <div panel-footer>
    <button (click)="edit.emit(employee())">Edit</button>
    <button (click)="delete.emit(employee().id)">Delete</button>
  </div>
</app-panel>
```

---

## Exercise 6: Anti-Pattern Refactoring

### Step 6.1 — Identify and fix

Review your code for these anti-patterns:

| Anti-Pattern | Fix |
|--------------|-----|
| Passing data through 3+ component layers | Introduce shared service |
| Child component calling HttpClient | Move API calls to service (Lab 07) |
| Duplicate selected employee state | Single source in `EmployeeSelectionService` |

Document one refactor you made in a `REFACTORING.md` file (2–3 sentences).

---

## Verification Checklist

- [ ] Parent passes data via `input()`; child emits via `output()`
- [ ] `EmployeeSelectionService` shares selection across list, detail, sidebar
- [ ] `CardComponent` wraps dashboard stats via single-slot projection
- [ ] `PanelComponent` uses named slots for header and footer
- [ ] No prop-drilling beyond 2 levels
- [ ] `REFACTORING.md` documents one improvement

---

## Stretch Goals

1. Add `ng-content select="[panel-actions]"` for a third slot.
2. Create `NotificationService` with signal-based toast messages.
3. Build a presentational `StatCardComponent` with `title` input and projected content.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Projected content not appearing | Check `select` attribute matches exactly |
| Service state not shared | Ensure `providedIn: 'root'` |
| Events not reaching parent | Verify `(outputName)="handler($event)"` binding |

---

## Day 2 Lab Summary

Your portal now has signal-based dashboard, custom directives/pipes, reusable projected components, and shared selection state.

**Next:** Day 3 — **[Lab 07 — Dependency Injection & Services](Module-07-Dependency-Injection-and-Services.md)**
