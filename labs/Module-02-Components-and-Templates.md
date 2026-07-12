# Lab 02 — Components & Templates

| | |
|---|---|
| **Module** | 2 — Components & Templates |
| **Duration** | 90 minutes |
| **Presentation** | `presentations/Module-2-Components-and-Templates.pdf` |
| **Prerequisites** | Lab 01 complete |

---

## Learning Objectives

By completing this lab, you will:

- Create standalone components using Angular CLI
- Apply interpolation, property binding, event binding, and two-way binding
- Compose a multi-component layout for the Employee Portal
- Use lifecycle hooks and template reference variables

---

## Prerequisites

- Lab 01 project (`employee-portal`) running locally
- Familiarity with TypeScript classes and decorators

---

## Exercise 1: Scaffold Portal Layout Components

### Step 1.1 — Generate components

```bash
ng generate component layout/header --standalone
ng generate component layout/sidebar --standalone
ng generate component layout/footer --standalone
ng generate component employees/employee-card --standalone
ng generate component employees/employee-list --standalone
```

### Step 1.2 — Wire layout into the app

Update `app.component.ts` to import `HeaderComponent`, `SidebarComponent`, and `FooterComponent`.

Update `app.component.html`:

```html
<app-header />
<div class="app-body">
  <app-sidebar />
  <main class="content">
    <router-outlet />
  </main>
</div>
<app-footer />
```

Add flex layout styles in `app.component.scss` so sidebar and content sit side by side.

---

## Exercise 2: Build the Header with Data Binding

### Step 2.1 — Interpolation and property binding

In `header.component.ts`, add:

```typescript
appTitle = 'Employee Management Portal';
userName = 'Alex Trainer';
isLoggedIn = true;
logoUrl = 'assets/logo.svg'; // use any placeholder image path
```

In `header.component.html`:

```html
<img [src]="logoUrl" alt="Company logo" height="32" />
<h1>{{ appTitle }}</h1>
@if (isLoggedIn) {
  <span>Welcome, {{ userName }}</span>
}
```

### Step 2.2 — Event binding

Add a `logout()` method that sets `isLoggedIn = false` and logs to console.

Add a button: `<button (click)="logout()">Logout</button>`.

Verify the welcome message disappears after clicking Logout.

---

## Exercise 3: Employee Card Component

### Step 3.1 — Define the Employee model

Create `src/app/models/employee.model.ts`:

```typescript
export interface Employee {
  id: number;
  name: string;
  role: string;
  department: string;
  email: string;
  photoUrl?: string;
}
```

### Step 3.2 — Implement employee-card

In `employee-card.component.ts`, add a sample employee object (hardcoded for now).

Template requirements:

- Display name, role, department via **interpolation**
- Bind `[src]` for photo with a fallback placeholder
- Bind `[class.inactive]` when role is `'Contractor'`
- Add `(click)` on the card to log the employee id

---

## Exercise 4: Employee List with Hardcoded Data

### Step 4.1 — Populate sample data

In `employee-list.component.ts`, create an array of at least 5 `Employee` objects.

### Step 4.2 — Render cards

Use `@for` temporarily (full control flow lab is next — this is a preview):

```html
@for (employee of employees; track employee.id) {
  <app-employee-card [employee]="employee" />
}
```

> **Note:** You will refactor `employee-card` to accept `@Input()` in Lab 06. For now, duplicate data or use a shared constant.

### Step 4.3 — Two-way binding for search

Install forms module for `ngModel`:

```bash
ng add @angular/forms
```

In `employee-list.component.ts`, add `searchQuery = ''`.

Add to template:

```html
<input [(ngModel)]="searchQuery" placeholder="Search employees..." />
<p>Searching: {{ searchQuery }}</p>
```

Import `FormsModule` in the component's `imports` array.

---

## Exercise 5: Lifecycle Hooks & Template Utilities

### Step 5.1 — Implement ngOnInit

In `employee-list.component.ts`:

```typescript
import { Component, OnInit } from '@angular/core';

export class EmployeeListComponent implements OnInit {
  ngOnInit(): void {
    console.log('EmployeeList initialized with', this.employees.length, 'records');
  }
}
```

### Step 5.2 — Template reference variable

In `employee-list.component.html`:

```html
<input #searchInput type="text" placeholder="Quick search" />
<button (click)="focusSearch(searchInput)">Focus Search</button>
```

Implement `focusSearch(el: HTMLInputElement)` to call `el.focus()`.

### Step 5.3 — ViewChild (optional stretch preview)

Use `@ViewChild('searchInput')` and access it in `ngAfterViewInit()` to log the element.

---

## Exercise 6: Sidebar Navigation Links

Hardcode navigation items in `sidebar.component.ts`:

```typescript
navItems = [
  { label: 'Dashboard', icon: 'dashboard' },
  { label: 'Employees', icon: 'people' },
  { label: 'Departments', icon: 'business' },
  { label: 'Reports', icon: 'assessment' },
];
```

Render with `@for` and use **event binding** `(click)` to log the selected item.

---

## Verification Checklist

- [ ] Five standalone components created and composed in app shell
- [ ] Header uses interpolation, property binding, and event binding
- [ ] Employee list displays 5+ employee cards
- [ ] Two-way binding works on search input
- [ ] `ngOnInit` logs on component load
- [ ] Template reference variable focuses the search input
- [ ] No NgModule files introduced

---

## Stretch Goals

1. Set `changeDetection: ChangeDetectionStrategy.OnPush` on `employee-card` and observe behavior.
2. Extract inline template to external HTML for one component (if not already).
3. Add `ngOnDestroy` with a `console.log` cleanup message.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `ngModel` not working | Import `FormsModule` in component `imports` |
| Component not found in template | Add component to parent `imports` array |
| Images not loading | Place files in `src/assets/` and reference as `assets/filename` |

---

## Next Lab

Continue to **[Lab 03 — Modern Angular Control Flow](Module-03-Modern-Angular-Control-Flow.md)**.
