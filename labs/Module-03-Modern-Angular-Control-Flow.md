# Lab 03 — Modern Angular Control Flow

| | |
|---|---|
| **Module** | 3 — Modern Angular Control Flow |
| **Duration** | 60 minutes |
| **Presentation** | `presentations/Module-3-Modern-Angular-Control-Flow.pdf` |
| **Prerequisites** | Labs 01–02 complete |

---

## Learning Objectives

By completing this lab, you will:

- Replace legacy structural directives with `@if`, `@else`, `@for`, and `@switch`
- Apply correct `track` expressions for list rendering
- Build conditional UI for the Employee Portal using modern syntax
- Run the Angular control-flow migration schematic

---

## Prerequisites

- Employee Portal with employee list and layout components
- No dependency on `*ngIf`, `*ngFor`, or `*ngSwitch` in new code

---

## Exercise 1: Conditional Rendering with @if / @else

### Step 1.1 — Login state in header

Refactor `header.component.html` to use explicit blocks:

```html
@if (isLoggedIn) {
  <div class="user-menu">
    <span>Welcome, {{ userName }}</span>
    <button (click)="logout()">Logout</button>
  </div>
} @else {
  <div class="guest-menu">
    <span>Please sign in</span>
    <button (click)="login()">Login</button>
  </div>
}
```

Add `login()` method that sets `isLoggedIn = true`.

### Step 1.2 — Loading state in employee list

Add `isLoading = true` to `employee-list.component.ts`.

Simulate loading in `ngOnInit`:

```typescript
ngOnInit(): void {
  setTimeout(() => {
    this.isLoading = false;
  }, 1500);
}
```

Template:

```html
@if (isLoading) {
  <p class="loading">Loading employees...</p>
} @else {
  <!-- employee list content -->
}
```

---

## Exercise 2: Iteration with @for

### Step 2.1 — Employee list

Refactor the employee list to use modern syntax:

```html
@for (employee of employees; track employee.id) {
  <app-employee-card [employee]="employee" />
} @empty {
  <p class="empty-state">No employees found.</p>
}
```

### Step 2.2 — Sidebar navigation

```html
@for (item of navItems; track item.label) {
  <a class="nav-item" (click)="selectNav(item)">
    {{ item.label }}
  </a>
}
```

### Step 2.3 — Filtered list with track

Add a `filteredEmployees` getter that filters by `searchQuery`.

```html
@for (employee of filteredEmployees; track employee.id) {
  <app-employee-card [employee]="employee" />
} @empty {
  <p>No employees match "{{ searchQuery }}".</p>
}
```

**Rule:** Always use `track employee.id` — never omit the track expression.

---

## Exercise 3: Conditional Branching with @switch

### Step 3.1 — Employee status badge

Add `status: 'active' | 'on-leave' | 'terminated'` to the `Employee` interface.

Update sample data with mixed statuses.

In `employee-card.component.html`:

```html
@switch (employee.status) {
  @case ('active') {
    <span class="badge badge-active">Active</span>
  }
  @case ('on-leave') {
    <span class="badge badge-leave">On Leave</span>
  }
  @case ('terminated') {
    <span class="badge badge-terminated">Terminated</span>
  }
  @default {
    <span class="badge">Unknown</span>
  }
}
```

### Step 3.2 — Role-based view placeholder

In `employee-list.component.html`, add a role switch for the current user:

```html
@switch (currentUserRole) {
  @case ('admin') {
    <button class="btn-primary">Add Employee</button>
  }
  @case ('manager') {
    <button class="btn-secondary">Request Hire</button>
  }
  @default {
    <p class="read-only-notice">Read-only access</p>
  }
}
```

Set `currentUserRole = 'admin'` in the component class.

---

## Exercise 4: Refactor Legacy Directives (If Present)

### Step 4.1 — Search for legacy syntax

```bash
# In project root
rg "ngIf|ngFor|ngSwitch" src/
```

If any matches exist, refactor them to modern control flow.

### Step 4.2 — Run migration schematic

```bash
ng generate @angular/core:control-flow
```

Review the diff. Accept automated changes where correct; revert any incorrect migrations manually.

---

## Exercise 5: Control Flow Best Practices

### Step 5.1 — Extract complex conditions

**Before (avoid):**

```html
@if (employees.length > 0 && !isLoading && currentUserRole === 'admin') { ... }
```

**After (preferred):**

```typescript
get canManageEmployees(): boolean {
  return !this.isLoading && this.employees.length > 0 && this.currentUserRole === 'admin';
}
```

```html
@if (canManageEmployees) {
  <button>Add Employee</button>
}
```

Apply this pattern in at least one component.

### Step 5.2 — Avoid deep nesting

If you have `@if` inside `@for` inside `@if` more than 2 levels deep, extract a child component.

---

## Verification Checklist

- [ ] All templates use `@if` / `@else` — no `*ngIf`
- [ ] All lists use `@for` with explicit `track` — no `*ngFor`
- [ ] Status badges use `@switch` / `@case` / `@default`
- [ ] `@empty` block shown when filtered list has no results
- [ ] Complex conditions moved to component getters
- [ ] App runs without `CommonModule` imported for control flow

---

## Stretch Goals

1. Add `@defer` preview on a heavy section (full implementation in Lab 14).
2. Create an `EmployeeStatusPipe` mapping — deferred to Lab 04; use `@switch` for now.
3. Write a unit test verifying `@empty` renders when `employees = []`.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `track` expression required error | Add `track item.id` or `track $index` as last resort |
| `@empty` not showing | Ensure the `@for` iterable is empty, not undefined |
| Migration breaks templates | Revert file and migrate manually |

---

## Day 1 Lab Summary

You have completed Day 1 labs. Your portal should have:

- Angular 21 standalone app shell
- Multi-component layout
- Data binding throughout
- Modern control flow for all dynamic UI

**Next:** Day 2 begins with **[Lab 04 — Directives & Pipes](Module-04-Directives-and-Pipes.md)**.
