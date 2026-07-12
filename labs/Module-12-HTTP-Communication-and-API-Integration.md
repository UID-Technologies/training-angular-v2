# Lab 12 — HTTP Communication & API Integration

| | |
|---|---|
| **Module** | 12 — HTTP Communication & API Integration |
| **Duration** | 90 minutes |
| **Presentation** | `presentations/Module-12-HTTP-Communication-and-API-Integration.pdf` |
| **Prerequisites** | Day 4 labs complete |

---

## Learning Objectives

By completing this lab, you will:

- Configure `HttpClient` and perform CRUD operations
- Design a layered API service architecture
- Handle errors gracefully with `catchError`
- Implement HTTP interceptors for auth tokens and loading state

---

## Exercise 1: Setup HttpClient & Mock API

### Step 1.1 — Enable HttpClient

In `app.config.ts`:

```typescript
import { provideHttpClient, withInterceptors } from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor, loadingInterceptor])),
    // ...
  ],
};
```

### Step 1.2 — Start mock API

Create `resources/mock-api/db.json` with employees array, or use JSON Server:

```bash
npx json-server --watch resources/mock-api/db.json --port 3000
```

Sample `db.json`:

```json
{
  "employees": [
    { "id": 1, "name": "Jane Doe", "email": "jane@company.com", "role": "Engineer", "department": "Engineering", "status": "active", "salary": 95000 }
  ],
  "departments": [
    { "id": 1, "name": "Engineering", "head": "John Smith" }
  ]
}
```

---

## Exercise 2: API Service Layer

### Step 2.1 — Base API service

```typescript
@Injectable({ providedIn: 'root' })
export class ApiService {
  private http = inject(HttpClient);
  private config = inject(API_CONFIG);

  get<T>(path: string): Observable<T> {
    return this.http.get<T>(`${this.config.baseUrl}/${path}`);
  }

  post<T>(path: string, body: unknown): Observable<T> {
    return this.http.post<T>(`${this.config.baseUrl}/${path}`, body);
  }

  put<T>(path: string, body: unknown): Observable<T> {
    return this.http.put<T>(`${this.config.baseUrl}/${path}`, body);
  }

  delete<T>(path: string): Observable<T> {
    return this.http.delete<T>(`${this.config.baseUrl}/${path}`);
  }
}
```

### Step 2.2 — Employee API service

```typescript
@Injectable({ providedIn: 'root' })
export class EmployeeApiService {
  private api = inject(ApiService);

  getAll(): Observable<Employee[]> {
    return this.api.get<Employee[]>('employees');
  }

  getById(id: number): Observable<Employee> {
    return this.api.get<Employee>(`employees/${id}`);
  }

  create(employee: Omit<Employee, 'id'>): Observable<Employee> {
    return this.api.post<Employee>('employees', employee);
  }

  update(id: number, employee: Partial<Employee>): Observable<Employee> {
    return this.api.put<Employee>(`employees/${id}`, employee);
  }

  delete(id: number): Observable<void> {
    return this.api.delete<void>(`employees/${id}`);
  }
}
```

---

## Exercise 3: Refactor EmployeeService to Use HTTP

```typescript
@Injectable({ providedIn: 'root' })
export class EmployeeService {
  private api = inject(EmployeeApiService);
  private notification = inject(NotificationService);

  private readonly _employees = signal<Employee[]>([]);
  private readonly _loading = signal(false);
  private readonly _error = signal<string | null>(null);

  readonly employees = this._employees.asReadonly();
  readonly loading = this._loading.asReadonly();
  readonly error = this._error.asReadonly();

  loadAll(): void {
    this._loading.set(true);
    this._error.set(null);
    this.api.getAll().pipe(
      tap(employees => this._employees.set(employees)),
      catchError(err => {
        this._error.set('Failed to load employees');
        this.notification.success(''); // use error method
        return of([]);
      }),
      finalize(() => this._loading.set(false)),
    ).subscribe();
  }

  create(employee: Omit<Employee, 'id'>): void {
    this.api.create(employee).pipe(
      tap(created => this._employees.update(list => [...list, created])),
      catchError(() => { this._error.set('Create failed'); return EMPTY; }),
    ).subscribe();
  }

  // update, delete similarly
}
```

Call `loadAll()` in `employee-list` `ngOnInit`.

---

## Exercise 4: Error Handling

### Step 4.1 — Global error handler service

```typescript
@Injectable({ providedIn: 'root' })
export class ErrorHandlerService {
  handle(error: HttpErrorResponse): string {
    if (error.status === 0) return 'Network error — check your connection';
    if (error.status === 401) return 'Session expired — please login again';
    if (error.status === 403) return 'You do not have permission';
    if (error.status === 404) return 'Resource not found';
    return error.error?.message ?? 'An unexpected error occurred';
  }
}
```

### Step 4.2 — Display errors in template

```html
@if (employeeService.loading()) {
  <p>Loading...</p>
}
@if (employeeService.error(); as error) {
  <div class="alert alert-error">{{ error }}</div>
}
```

---

## Exercise 5: HTTP Interceptors

### Step 5.1 — Auth interceptor

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = sessionStorage.getItem('auth_token');
  if (token) {
    req = req.clone({ setHeaders: { Authorization: `Bearer ${token}` } });
  }
  return next(req);
};
```

### Step 5.2 — Loading interceptor

```typescript
const activeRequests = signal(0);

export const loadingInterceptor: HttpInterceptorFn = (req, next) => {
  activeRequests.update(c => c + 1);
  return next(req).pipe(
    finalize(() => activeRequests.update(c => c - 1)),
  );
};

export const httpLoadingService = { activeRequests: activeRequests.asReadonly() };
```

Show global spinner in `app.component.html` when `activeRequests() > 0`.

### Step 5.3 — Retry strategy

```typescript
this.api.getAll().pipe(
  retry({ count: 2, delay: 1000 }),
  catchError(/* ... */),
)
```

---

## Exercise 6: Update Resolver for HTTP

```typescript
export const employeeResolver: ResolveFn<Employee> = (route) => {
  const api = inject(EmployeeApiService);
  const router = inject(Router);
  const id = Number(route.paramMap.get('id'));

  return api.getById(id).pipe(
    catchError(() => {
      router.navigate(['/employees']);
      return EMPTY;
    }),
  );
};
```

---

## Verification Checklist

- [ ] JSON Server (or mock API) running on port 3000
- [ ] `HttpClient` configured with interceptors
- [ ] CRUD operations work against REST API
- [ ] Loading and error states displayed in UI
- [ ] Auth interceptor attaches Bearer token
- [ ] Loading interceptor tracks active requests
- [ ] Resolver fetches employee from API
- [ ] Service layer separates HTTP from components

---

## Stretch Goals

1. Add pagination: `GET /employees?_page=1&_limit=10`.
2. Implement `HttpContext` to skip loading interceptor for background requests.
3. Add optimistic UI update on delete.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| CORS errors | Configure JSON Server or proxy in `angular.json` |
| 404 on API calls | Verify `API_CONFIG.baseUrl` matches server |
| Double loading indicators | Check interceptor not duplicated in providers |

---

## Next Lab

**[Lab 13 — State Management](Module-13-State-Management.md)**
