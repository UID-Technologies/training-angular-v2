# Lab 04 — Directives & Pipes

| | |
|---|---|
| **Module** | 4 — Directives & Pipes |
| **Duration** | 75 minutes |
| **Presentation** | `presentations/Module-4-Directives-and-Pipes.pdf` |
| **Prerequisites** | Day 1 labs complete |

---

## Learning Objectives

By completing this lab, you will:

- Use built-in attribute directives (`ngClass`, `ngStyle`)
- Create a custom attribute directive for reusable UI behavior
- Apply built-in pipes for formatting
- Build custom pure pipes for domain-specific display logic

---

## Exercise 1: Built-in Attribute Directives

### Step 1.1 — ngClass on employee cards

In `employee-card.component.html`, highlight active employees:

```html
<div
  class="employee-card"
  [ngClass]="{
    'card-active': employee.status === 'active',
    'card-inactive': employee.status === 'terminated'
  }"
>
```

Import `NgClass` from `@angular/common` in the component `imports`.

### Step 1.2 — ngStyle for department color coding

Add a `departmentColor` map in the component:

```typescript
departmentColors: Record<string, string> = {
  Engineering: '#e3f2fd',
  HR: '#fce4ec',
  Sales: '#e8f5e9',
};
```

Apply:

```html
<div [ngStyle]="{ 'border-left': '4px solid ' + (departmentColors[employee.department] ?? '#ccc') }">
```

Import `NgStyle` in component `imports`.

---

## Exercise 2: Custom Attribute Directive — HighlightInvalid

### Step 2.1 — Generate the directive

```bash
ng generate directive shared/directives/highlight-invalid --standalone
```

### Step 2.2 — Implement

```typescript
import { Directive, ElementRef, inject, input, OnChanges } from '@angular/core';

@Directive({
  selector: '[appHighlightInvalid]',
  standalone: true,
})
export class HighlightInvalidDirective implements OnChanges {
  private el = inject(ElementRef<HTMLElement>);
  appHighlightInvalid = input(false);

  ngOnChanges(): void {
    this.el.nativeElement.style.outline = this.appHighlightInvalid()
      ? '2px solid #e53935'
      : '';
  }
}
```

### Step 2.3 — Apply to a form input (preview)

On any input in the employee list search:

```html
<input
  [appHighlightInvalid]="searchQuery.length > 0 && searchQuery.length < 3"
  [(ngModel)]="searchQuery"
/>
```

Add helper text: "Enter at least 3 characters."

---

## Exercise 3: Custom Attribute Directive — AutoFocus

### Step 3.1 — Create `auto-focus.directive.ts`

Selector: `[appAutoFocus]`. On `ngOnInit`, call `this.el.nativeElement.focus()`.

### Step 3.2 — Apply

```html
<input appAutoFocus [(ngModel)]="searchQuery" placeholder="Search employees..." />
```

---

## Exercise 4: Built-in Pipes

### Step 4.1 — Format employee data in template

In `employee-card.component.html`:

```html
<p>Hired: {{ employee.hireDate | date:'mediumDate' }}</p>
<p>Salary: {{ employee.salary | currency:'USD':'symbol':'1.0-0' }}</p>
<p>{{ employee.name | titlecase }}</p>
```

Add `hireDate` and `salary` to the `Employee` interface and sample data.

Import `DatePipe`, `CurrencyPipe`, `TitleCasePipe` from `@angular/common`.

---

## Exercise 5: Custom Pipes

### Step 5.1 — EmployeeIdPipe

```bash
ng generate pipe shared/pipes/employee-id --standalone
```

Transform `42` → `EMP-00042`:

```typescript
transform(id: number): string {
  return `EMP-${String(id).padStart(5, '0')}`;
}
```

Usage: `{{ employee.id | employeeId }}`

### Step 5.2 — EmployeeStatusLabelPipe

Map enum values to readable labels:

- `active` → "Active Employee"
- `on-leave` → "Currently on Leave"
- `terminated` → "No Longer Employed"

Mark the pipe as `pure: true` (default).

### Step 5.3 — PhoneMaskPipe

Transform `9876543210` → `(987) 654-3210`. Handle invalid input gracefully.

---

## Exercise 6: Pipe Performance Awareness

### Step 6.1 — Pure vs impure discussion

Create a **deliberately impure** pipe `TimestampPipe` that returns `new Date().toLocaleTimeString()`.

Add it to the header: `{{ '' | timestamp }}`.

Observe: it updates on every change detection cycle.

### Step 6.2 — Refactor to pure or remove

Remove the impure pipe from production templates. Document in a code comment why impure pipes should be avoided for expensive logic.

---

## Verification Checklist

- [ ] `ngClass` and `ngStyle` applied to employee cards
- [ ] `HighlightInvalidDirective` highlights invalid search input
- [ ] `AutoFocusDirective` focuses search on load
- [ ] Built-in pipes format dates, currency, and title case
- [ ] `EmployeeIdPipe` and `EmployeeStatusLabelPipe` work correctly
- [ ] All custom pipes are standalone and imported where used
- [ ] No impure pipes in production UI

---

## Stretch Goals

1. Create `PermissionDirective` that hides elements when `currentUserRole` lacks access.
2. Create `TruncatePipe` for long department names.
3. Unit test `EmployeeIdPipe` with edge cases (0, negative, large numbers).

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Pipe not found in template | Add pipe to component `imports` array |
| ngClass not applying | Import `NgClass`; check expression syntax |
| Directive not firing | Confirm selector matches attribute exactly |

---

## Next Lab

**[Lab 05 — Signals & Angular Reactivity](Module-05-Signals-and-Angular-Reactivity.md)**
