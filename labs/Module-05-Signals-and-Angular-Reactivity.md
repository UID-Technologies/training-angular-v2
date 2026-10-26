# Lab 05 — Signals & Angular Reactivity

| | |
|---|---|
| **Module** | 5 — Signals & Angular Reactivity |
| **Duration** | 90 minutes |
| **Presentation** | `presentations/Module-05-Signals-and-Angular-Reactivity.pdf` |
| **Prerequisites** | Labs 01–04 complete |

---

## Learning Objectives

By completing this lab, you will:

- Replace imperative state with writable and computed signals
- Use `effect()` for side effects
- Implement `input()` and `output()` signal APIs
- Manage dependent state with `linkedSignal()`

---

## Exercise 1: Writable Signals — Dashboard Stats

### Step 1.1 — Generate dashboard component

```bash
ng generate component dashboard/dashboard --standalone
```

### Step 1.2 — Define signal state

```typescript
import { Component, signal, computed } from '@angular/core';

export class DashboardComponent {
  totalEmployees = signal(128);
  activeEmployees = signal(115);
  onLeave = signal(8);
  openPositions = signal(5);

  readonly attendanceRate = computed(() => {
    const total = this.totalEmployees();
    return total > 0 ? (this.activeEmployees() / total) * 100 : 0;
  });
}
```

### Step 1.3 — Template bindings

```html
<div class="stat-card">
  <h3>Total Employees</h3>
  <p class="stat-value">{{ totalEmployees() }}</p>
</div>
<div class="stat-card">
  <h3>Attendance Rate</h3>
  <p class="stat-value">{{ attendanceRate() | number:'1.1-1' }}%</p>
</div>
```

### Step 1.4 — Update signals

Add buttons:

```html
<button (click)="totalEmployees.update(v => v + 1)">Hire +1</button>
<button (click)="activeEmployees.set(activeEmployees() - 1)">Mark Leave</button>
```

---

## Exercise 2: Computed Signals — Filtered Metrics

### Step 2.1 — Department filter signal

```typescript
selectedDepartment = signal<string | null>(null);

departments = signal(['Engineering', 'HR', 'Sales', 'Finance']);

departmentHeadcount = computed(() => {
  const dept = this.selectedDepartment();
  if (!dept) return this.totalEmployees();
  // Simulated — replace with real data in Lab 12
  const map: Record<string, number> = { Engineering: 45, HR: 12, Sales: 38, Finance: 33 };
  return map[dept] ?? 0;
});
```

### Step 2.2 — Template

```html
<select (change)="selectedDepartment.set($any($event.target).value || null)">
  <option value="">All Departments</option>
  @for (dept of departments(); track dept) {
    <option [value]="dept">{{ dept }}</option>
  }
</select>
<p>Headcount: {{ departmentHeadcount() }}</p>
```

---

## Exercise 3: Effects — Logging & Persistence

### Step 3.1 — Add an effect

```typescript
import { Component, signal, computed, effect, inject } from '@angular/core';

export class DashboardComponent {
  constructor() {
    effect(() => {
      console.log('[Dashboard] Stats updated:', {
        total: this.totalEmployees(),
        active: this.activeEmployees(),
        rate: this.attendanceRate(),
      });
    });
  }
}
```

### Step 3.2 — Persist filter preference

```typescript
effect(() => {
  const dept = this.selectedDepartment();
  if (dept) {
    localStorage.setItem('dashboard-dept', dept);
  }
});
```

Load saved value in a constructor effect or `ngOnInit` with `selectedDepartment.set(saved)`.

> **Best practice:** Effects should not modify signals that they read (avoid circular updates).

---

## Exercise 4: Signal Inputs & Outputs

### Step 4.1 — Refactor employee-card

```typescript
import { Component, input, output } from '@angular/core';
import { Employee } from '../../models/employee.model';

@Component({ /* ... */ })
export class EmployeeCardComponent {
  employee = input.required<Employee>();
  selected = output<number>();

  onSelect(): void {
    this.selected.emit(this.employee().id);
  }
}
```

Template: `{{ employee().name }}` — note signal invocation `()`.

Parent:

```html
@for (emp of employees(); track emp.id) {
  <app-employee-card
    [employee]="emp"
    (selected)="onEmployeeSelected($event)"
  />
}
```

Convert parent `employees` array to `employees = signal<Employee[]>([...])`.

---

## Exercise 5: linkedSignal — Dependent Selection

### Step 5.1 — Employee detail panel

```bash
ng generate component employees/employee-detail --standalone
```

### Step 5.2 — Implement linkedSignal

```typescript
import { linkedSignal, signal } from '@angular/core';

export class EmployeeDetailComponent {
  employees = input.required<Employee[]>();
  selectedId = signal<number | null>(null);

  selectedEmployee = linkedSignal(() => {
    const id = this.selectedId();
    const list = this.employees();
    return id ? list.find(e => e.id === id) ?? null : null;
  });
}
```

When `selectedId` or `employees` changes, `selectedEmployee` recomputes automatically.

### Step 5.3 — Wire parent-child

In `employee-list`, on card select: `selectedId.set(id)`.

Show detail panel:

```html
@if (selectedEmployee()) {
  <app-employee-detail [employee]="selectedEmployee()!" />
}
```

---

## Exercise 6: Signal-Based Search

Replace `searchQuery` string with:

```typescript
searchQuery = signal('');

filteredEmployees = computed(() => {
  const query = this.searchQuery().toLowerCase().trim();
  const list = this.employees();
  if (!query) return list;
  return list.filter(e =>
    e.name.toLowerCase().includes(query) ||
    e.department.toLowerCase().includes(query)
  );
});
```

Template:

```html
<input
  [value]="searchQuery()"
  (input)="searchQuery.set($any($event.target).value)"
/>
```

---

## Verification Checklist

- [ ] Dashboard uses `signal()` and `computed()` for all stats
- [ ] `effect()` logs state changes to console
- [ ] `employee-card` uses `input()` and `output()` signal APIs
- [ ] `linkedSignal()` drives employee detail selection
- [ ] Search filtering uses `computed()` — no manual filter method
- [ ] All template signal reads use `()` syntax

---

## Stretch Goals

1. Convert `DashboardComponent` to use `resource()` or `httpResource()` preview (Angular 19+).
2. Add `equal: customEqualityFn` to a computed signal.
3. Use `untracked()` inside an effect to read without subscribing.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Template shows signal object not value | Add `()` when reading: `mySignal()` |
| Effect runs too often | Check for signal writes inside effects |
| `input.required` error | Ensure parent passes the binding |

---

## Next Lab

**[Lab 06 — Component Communication & Content Projection](Module-06-Component-Communication-and-Content-Projection.md)**
