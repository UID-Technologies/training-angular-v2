# Lab 15 — Accessibility in Angular

| | |
|---|---|
| **Module** | 15 — Accessibility in Angular |
| **Duration** | 45 minutes |
| **Presentation** | `presentations/Module-15-Accessibility-in-Angular.pdf` |
| **Prerequisites** | Lab 14 complete |

---

## Learning Objectives

By completing this lab, you will:

- Apply semantic HTML and ARIA attributes in Angular templates
- Ensure keyboard navigation across the Employee Portal
- Augment Material components for screen reader compatibility
- Audit and fix accessibility issues

---

## Exercise 1: Semantic HTML Audit

### Step 1.1 — Review app shell

Ensure proper landmarks:

```html
<header>
  <mat-toolbar>...</mat-toolbar>
</header>

<mat-sidenav-container>
  <mat-sidenav role="navigation" aria-label="Main navigation">
    ...
  </mat-sidenav>
  <mat-sidenav-content>
    <main id="main-content" tabindex="-1">
      <router-outlet />
    </main>
  </mat-sidenav-content>
</mat-sidenav-container>

<footer>
  <p>&copy; 2026 Employee Management Portal</p>
</footer>
```

### Step 1.2 — Fix common issues

Replace generic `<div>` click handlers with `<button>` elements.
Ensure every `<input>` has an associated `<label>` or `aria-label`.

---

## Exercise 2: ARIA Attributes

### Step 2.1 — Employee table

```html
<table mat-table aria-label="Employee directory" role="table">
  <th mat-header-cell scope="col">Name</th>
  <!-- scope="col" on all headers -->
</table>
```

### Step 2.2 — Status badges

```html
<span
  class="badge"
  role="status"
  [attr.aria-label]="'Employee status: ' + (employee.status | employeeStatusLabel)"
>
  {{ employee.status | employeeStatusLabel }}
</span>
```

### Step 2.3 — Loading states

```html
@if (employeeStore.loading()) {
  <div role="status" aria-live="polite" aria-busy="true">
    <mat-spinner aria-label="Loading employees"></mat-spinner>
    <span class="sr-only">Loading employees, please wait</span>
  </div>
}
```

Add `.sr-only` utility class:

```scss
.sr-only {
  position: absolute;
  width: 1px; height: 1px;
  padding: 0; margin: -1px;
  overflow: hidden;
  clip: rect(0,0,0,0);
  border: 0;
}
```

### Step 2.4 — Error alerts

```html
@if (employeeStore.error(); as error) {
  <div role="alert" aria-live="assertive" class="alert-error">
    {{ error }}
  </div>
}
```

---

## Exercise 3: Keyboard Navigation

### Step 3.1 — Skip link

Add as first element in `app.component.html`:

```html
<a class="skip-link" href="#main-content">Skip to main content</a>
```

```scss
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  z-index: 1000;
  padding: 0.5rem 1rem;
  background: #fff;
  &:focus { top: 0; }
}
```

### Step 3.2 — Focus management on route change

```typescript
// app.component.ts
private router = inject(Router);
private document = inject(DOCUMENT);

constructor() {
  this.router.events.pipe(
    filter(e => e instanceof NavigationEnd),
    takeUntilDestroyed(),
  ).subscribe(() => {
    const main = this.document.getElementById('main-content');
    main?.focus();
  });
}
```

### Step 3.3 — Keyboard-operable employee list

Ensure table rows are focusable and activatable:

```html
<tr
  mat-row
  tabindex="0"
  [attr.aria-selected]="selectedId() === row.id"
  (click)="select(row.id)"
  (keydown.enter)="select(row.id)"
  (keydown.space)="select(row.id); $event.preventDefault()"
>
```

### Step 3.4 — Trap focus in dialog

Material Dialog handles focus trap by default. Verify:

1. Open delete dialog
2. Press Tab — focus stays within dialog
3. Press Escape — dialog closes

---

## Exercise 4: Form Accessibility

### Step 4.1 — Labels and descriptions

```html
<mat-form-field>
  <mat-label>Email address</mat-label>
  <input
    matInput
    formControlName="email"
    type="email"
    autocomplete="email"
    aria-describedby="email-hint"
  />
  <mat-hint id="email-hint">Used for login and notifications</mat-hint>
  <mat-error>Please enter a valid email address</mat-error>
</mat-form-field>
```

### Step 4.2 — Error summary

At top of form:

```html
@if (form.invalid && form.touched) {
  <div role="alert" aria-labelledby="form-errors-heading">
    <h3 id="form-errors-heading">Please fix the following errors:</h3>
    <ul>
      @if (form.get('name')?.invalid) { <li>Name is required</li> }
      @if (form.get('email')?.invalid) { <li>Valid email is required</li> }
    </ul>
  </div>
}
```

---

## Exercise 5: Color Contrast & Visual Accessibility

### Step 5.1 — Verify contrast ratios

Use Chrome DevTools → CSS Overview or axe DevTools extension.

Fix any text below WCAG AA (4.5:1 for normal text).

### Step 5.2 — Don't rely on color alone

Status indicators should include text or icons, not just color:

```html
<mat-chip>
  <mat-icon>{{ statusIcon(emp.status) }}</mat-icon>
  {{ emp.status | employeeStatusLabel }}
</mat-chip>
```

---

## Exercise 6: Accessibility Audit

### Step 6.1 — Run automated audit

```bash
npm install -D @axe-core/cli
npx axe http://localhost:4200/employees --exit
```

Or use Lighthouse Accessibility score in Chrome DevTools.

### Step 6.2 — Document findings

Create `docs/a11y-audit.md`:

| Page | Issue | Severity | Fix Applied |
|------|-------|----------|-------------|
| /employees | Missing table captions | Medium | Added aria-label |
| /login | Low contrast hint text | High | Updated color token |

Fix at least 3 issues found by the audit.

---

## Verification Checklist

- [ ] Landmarks: header, nav, main, footer present
- [ ] Skip-to-content link works on keyboard focus
- [ ] Focus moves to main content on route change
- [ ] Loading/error states use `aria-live` regions
- [ ] Form fields have labels, hints, and error messages
- [ ] Table rows keyboard-operable with Enter/Space
- [ ] Status not conveyed by color alone
- [ ] `a11y-audit.md` documents fixes

---

## Stretch Goals

1. Add `prefers-reduced-motion` media query to disable animations.
2. Implement `aria-expanded` on collapsible sidebar.
3. Test with NVDA or VoiceOver screen reader for 10 minutes.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Focus lost after navigation | Implement focus management on `NavigationEnd` |
| Screen reader skips content | Verify semantic elements and landmark roles |
| Material component aria conflicts | Follow Material a11y docs; don't duplicate roles |

---

## Next Lab

**[Lab 16 — AI-Powered Angular Development & MCP Integration](Module-16-AI-Powered-Angular-Development-and-MCP-Integration.md)**
