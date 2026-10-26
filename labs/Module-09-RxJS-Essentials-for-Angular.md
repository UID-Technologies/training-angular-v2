# Lab 09 — RxJS Essentials for Angular

| | |
|---|---|
| **Module** | 9 — RxJS Essentials for Angular |
| **Duration** | 75 minutes |
| **Presentation** | `presentations/Module-09-RxJS-Essentials-for-Angular.pdf` |
| **Prerequisites** | Day 3 labs complete |

---

## Learning Objectives

By completing this lab, you will:

- Build Observable pipelines with common RxJS operators
- Manage subscription lifecycle with `takeUntilDestroyed()`
- Use Subjects for multicasting
- Bridge Signals and Observables with `toSignal()` and `toObservable()`

---

## Exercise 1: Observable Basics — Search Typeahead

### Step 1.1 — Create search service

```bash
ng generate service core/services/employee-search --standalone
```

```typescript
import { Injectable, inject } from '@angular/core';
import { Subject, debounceTime, distinctUntilChanged, map, switchMap, of } from 'rxjs';
import { EmployeeService } from './employee.service';

@Injectable({ providedIn: 'root' })
export class EmployeeSearchService {
  private employeeService = inject(EmployeeService);
  private searchTerms$ = new Subject<string>();

  results$ = this.searchTerms$.pipe(
    debounceTime(300),
    distinctUntilChanged(),
    map(term => term.trim().toLowerCase()),
    switchMap(term => {
      if (!term) return of([]);
      const all = this.employeeService.employees();
      return of(all.filter(e =>
        e.name.toLowerCase().includes(term) ||
        e.department.toLowerCase().includes(term)
      ));
    }),
  );

  search(term: string): void {
    this.searchTerms$.next(term);
  }
}
```

### Step 1.2 — Consume in component

```typescript
private searchService = inject(EmployeeSearchService);
private destroyRef = inject(DestroyRef);

searchResults = toSignal(this.searchService.results$, { initialValue: [] });
```

Template: bind input `(input)` to `searchService.search($any($event.target).value)`.

---

## Exercise 2: Operators — map, filter, tap

### Step 2.1 — Employee analytics pipeline

In `dashboard.component.ts`:

```typescript
import { interval, map, filter, tap, take } from 'rxjs';
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

ngOnInit(): void {
  interval(2000).pipe(
    take(5),
    map(tick => this.totalEmployees() + tick),
    filter(count => count < 140),
    tap(count => console.log('[Analytics] Simulated count:', count)),
    takeUntilDestroyed(),
  ).subscribe(count => {
    // Demonstrate pipeline — do not overwrite real data in production
  });
}
```

Document what each operator does in a code comment.

---

## Exercise 3: switchMap vs mergeMap

### Step 3.1 — Simulate employee lookup

```typescript
lookupEmployee(id: number): Observable<Employee | undefined> {
  return of(id).pipe(
    delay(300),
    switchMap(empId => of(this.employeeService.getById(empId))),
  );
}
```

### Step 3.2 — Compare behavior

Create a rapid-fire test button that calls lookup with different IDs. With `switchMap`, only the latest request result appears. Switch to `mergeMap` and observe overlapping results.

Write a 2-sentence comparison in comments.

---

## Exercise 4: Error Handling — catchError

### Step 4.1 — Wrap a failing observable

```typescript
import { catchError, of } from 'rxjs';

fetchWithFallback(): Observable<Employee[]> {
  return this.http.get<Employee[]>('/api/employees').pipe(
    tap(() => console.log('API success')),
    catchError(err => {
      console.error('API failed, using local data', err);
      return of(this.employeeService.employees());
    }),
  );
}
```

> HttpClient will be configured in Lab 12. For now, mock the failing call with `throwError(() => new Error('Network'))`.

---

## Exercise 5: Subjects

### Step 5.1 — BehaviorSubject for current user

```typescript
@Injectable({ providedIn: 'root' })
export class AuthService {
  private currentUser$ = new BehaviorSubject<{ name: string; role: string } | null>(null);
  readonly user$ = this.currentUser$.asObservable();

  login(name: string, role: string): void {
    this.currentUser$.next({ name, role });
  }

  logout(): void {
    this.currentUser$.next(null);
  }

  get isLoggedIn$(): Observable<boolean> {
    return this.user$.pipe(map(u => u !== null));
  }
}
```

### Step 5.2 — Subscribe in header

```typescript
currentUser = toSignal(this.authService.user$, { initialValue: null });
```

Replace hardcoded `isLoggedIn` with computed signal from `currentUser()`.

---

## Exercise 6: Signals ↔ RxJS Interop

### Step 6.1 — toSignal

Convert `AuthService.user$` to `currentUser` signal (done above).

### Step 6.2 — toObservable

```typescript
selectedDepartment = signal<string | null>(null);
selectedDepartment$ = toObservable(this.selectedDepartment);

// Use in pipe
departmentStats$ = this.selectedDepartment$.pipe(
  switchMap(dept => this.getStatsForDepartment(dept)),
);
```

### Step 6.3 — Decision guide

Add comment in code:

```
Use Signals when: synchronous UI state, computed derivations, template bindings
Use RxJS when: async streams, HTTP, debounce, event composition, multicasting
Use toSignal/toObservable when: bridging between both worlds
```

---

## Verification Checklist

- [ ] Search typeahead uses `debounceTime`, `distinctUntilChanged`, `switchMap`
- [ ] `takeUntilDestroyed()` prevents memory leaks
- [ ] `catchError` provides fallback data on failure
- [ ] `BehaviorSubject` manages auth state in `AuthService`
- [ ] `toSignal()` bridges Observable to template
- [ ] `toObservable()` converts signal for pipe processing

---

## Stretch Goals

1. Implement `ReplaySubject(3)` for last 3 search terms history.
2. Combine `employees$` and `department$` with `combineLatest`.
3. Use `shareReplay(1)` to avoid duplicate HTTP calls (preview for Lab 12).

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Memory leak warnings | Add `takeUntilDestroyed(this.destroyRef)` |
| Signal not updating from Observable | Check `toSignal` initial value and source emits |
| switchMap cancels too aggressively | Use `mergeMap` if all results needed |

---

## Next Lab

**[Lab 10 — Routing & Navigation](Module-10-Routing-and-Navigation.md)**
