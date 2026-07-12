# Lab 08 — Forms in Angular

| | |
|---|---|
| **Module** | 8 — Forms in Angular |
| **Duration** | 120 minutes |
| **Presentation** | `presentations/Module-8-Forms-in-Angular.pdf` |
| **Prerequisites** | Lab 07 complete |

---

## Learning Objectives

By completing this lab, you will:

- Build template-driven and reactive forms
- Implement validation with built-in and custom validators
- Create dynamic forms with `FormArray`
- Add async email validation
- Manage form state with signals

---

## Exercise 1: Template-Driven Registration Form

### Step 1.1 — Generate component

```bash
ng generate component auth/register --standalone
```

### Step 1.2 — Simple registration

```html
<form #registerForm="ngForm" (ngSubmit)="onSubmit(registerForm)">
  <input name="fullName" ngModel required minlength="2" #fullName="ngModel" />
  @if (fullName.invalid && fullName.touched) {
    <span class="error">Name is required (min 2 chars)</span>
  }

  <input name="email" ngModel required email #email="ngModel" />
  @if (email.invalid && email.touched) {
    <span class="error">Valid email required</span>
  }

  <button [disabled]="registerForm.invalid">Register</button>
</form>
```

Import `FormsModule`. Log form value on valid submit.

---

## Exercise 2: Reactive Employee Form

### Step 2.1 — Generate component

```bash
ng generate component employees/employee-form --standalone
```

### Step 2.2 — Build FormGroup

```typescript
import { Component, inject, output } from '@angular/core';
import { FormBuilder, ReactiveFormsModule, Validators } from '@angular/forms';

@Component({
  imports: [ReactiveFormsModule, /* HighlightInvalidDirective */],
  // ...
})
export class EmployeeFormComponent {
  private fb = inject(FormBuilder);
  saved = output<Employee>();

  form = this.fb.group({
    name: ['', [Validators.required, Validators.minLength(2)]],
    email: ['', [Validators.required, Validators.email]],
    role: ['', Validators.required],
    department: ['', Validators.required],
    salary: [0, [Validators.required, Validators.min(0)]],
    status: ['active' as const, Validators.required],
  });
}
```

### Step 2.3 — Template with validation display

```html
<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="name" [appHighlightInvalid]="form.get('name')!.invalid && form.get('name')!.touched" />
  @if (form.get('name')?.errors?.['required']) {
    <span class="error">Name is required</span>
  }

  <!-- Repeat for email, role, department, salary, status -->

  <button [disabled]="form.invalid">Save Employee</button>
</form>
```

---

## Exercise 3: Custom Validators

### Step 3.1 — Salary range validator

```typescript
// shared/validators/salary-range.validator.ts
import { AbstractControl, ValidationErrors } from '@angular/forms';

export function salaryRangeValidator(min: number, max: number) {
  return (control: AbstractControl): ValidationErrors | null => {
    const value = control.value;
    if (value == null) return null;
    return value >= min && value <= max
      ? null
      : { salaryRange: { min, max, actual: value } };
  };
}
```

Apply: `salary: [0, [Validators.required, salaryRangeValidator(30000, 250000)]]`

### Step 3.2 — No whitespace validator

Reject names that are only spaces. Apply to `name` control.

---

## Exercise 4: Async Email Validator

### Step 4.1 — Create validator service

```typescript
@Injectable({ providedIn: 'root' })
export class EmployeeValidationService {
  private takenEmails = ['admin@company.com', 'hr@company.com'];

  emailTakenValidator(): AsyncValidatorFn {
    return (control: AbstractControl): Observable<ValidationErrors | null> => {
      if (!control.value) return of(null);
      return of(this.takenEmails.includes(control.value)).pipe(
        delay(500),
        map(taken => taken ? { emailTaken: true } : null)
      );
    };
  }
}
```

### Step 4.2 — Apply to email control

```typescript
email: ['', [Validators.required, Validators.email], [this.validationService.emailTakenValidator()]]
```

Show "Email already registered" when async error present. Disable submit while `form.pending`.

---

## Exercise 5: Dynamic Form — Skills FormArray

### Step 5.1 — Add skills array

```typescript
form = this.fb.group({
  // ...existing fields
  skills: this.fb.array([]),
});

get skills(): FormArray {
  return this.form.get('skills') as FormArray;
}

addSkill(): void {
  this.skills.push(this.fb.control('', Validators.required));
}

removeSkill(index: number): void {
  this.skills.removeAt(index);
}
```

### Step 5.2 — Template

```html
<fieldset formArrayName="skills">
  <legend>Skills</legend>
  @for (skill of skills.controls; track $index) {
    <div>
      <input [formControlName]="$index" placeholder="Skill name" />
      <button type="button" (click)="removeSkill($index)">Remove</button>
    </div>
  }
  <button type="button" (click)="addSkill()">Add Skill</button>
</fieldset>
```

---

## Exercise 6: Dynamic Form from Metadata

### Step 6.1 — Form field config

```typescript
interface FormFieldConfig {
  key: string;
  label: string;
  type: 'text' | 'email' | 'number' | 'select';
  options?: string[];
  validators: ValidatorFn[];
}

const EMPLOYEE_FIELDS: FormFieldConfig[] = [
  { key: 'name', label: 'Full Name', type: 'text', validators: [Validators.required] },
  { key: 'department', label: 'Department', type: 'select', options: ['Engineering', 'HR', 'Sales'], validators: [Validators.required] },
  // ...
];
```

### Step 6.2 — Build form dynamically

```typescript
buildFormFromConfig(fields: FormFieldConfig[]): FormGroup {
  const group: Record<string, unknown> = {};
  for (const field of fields) {
    group[field.key] = ['', field.validators];
  }
  return this.fb.group(group);
}
```

Render fields with `@for` over config — one generic template for all field types.

---

## Exercise 7: Signal-Based Form State

### Step 7.1 — Form status signals

```typescript
import { toSignal } from '@angular/core/rxjs-interop';

formStatus = toSignal(this.form.statusChanges, { initialValue: this.form.status });
formValid = computed(() => this.formStatus() === 'VALID');
formDirty = toSignal(this.form.valueChanges.pipe(map(() => this.form.dirty)), { initialValue: false });
```

### Step 7.2 — Template feedback

```html
@if (formValid()) {
  <span class="status-ok">Form is valid</span>
} @else {
  <span class="status-error">Please fix validation errors</span>
}
@if (formDirty()) {
  <span class="unsaved">Unsaved changes</span>
}
```

---

## Exercise 8: Wire Form to Service

On valid submit in `employee-form`:

```typescript
onSubmit(): void {
  if (this.form.invalid) return;
  const employee = this.form.value as Employee;
  this.employeeService.add({ ...employee, id: Date.now() });
  this.notificationService.success('Employee saved');
  this.saved.emit(employee);
  this.form.reset();
}
```

Open form from employee list "Add Employee" button.

---

## Verification Checklist

- [ ] Template-driven registration form with `ngModel` validation
- [ ] Reactive employee form with `FormGroup`
- [ ] Custom `salaryRange` validator works
- [ ] Async email validator shows pending state
- [ ] `FormArray` adds/removes skill fields dynamically
- [ ] Metadata-driven form renders from config array
- [ ] `toSignal()` exposes form validity to template
- [ ] Submit persists employee via `EmployeeService`

---

## Stretch Goals

1. Cross-field validator: end date must be after start date.
2. `ControlValueAccessor` custom input component.
3. Edit mode: populate form when editing existing employee.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `formControlName` not found | Import `ReactiveFormsModule` |
| Async validator never completes | Return `of()` or `Observable` from validator |
| FormArray index errors | Use `track $index` in `@for` |

---

## Day 3 Lab Summary

Your portal now has a service layer, notification system, and full employee CRUD forms.

**Next:** Day 4 — **[Lab 09 — RxJS Essentials](Module-09-RxJS-Essentials-for-Angular.md)**
