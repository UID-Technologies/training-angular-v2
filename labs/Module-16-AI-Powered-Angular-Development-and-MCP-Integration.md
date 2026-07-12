# Lab 16 — AI-Powered Angular Development & MCP Integration

| | |
|---|---|
| **Module** | 16 — AI-Powered Angular Development & MCP Integration |
| **Duration** | 45 minutes |
| **Presentation** | `presentations/Module-16-AI-Powered-Angular-Development-and-MCP-Integration.pdf` |
| **Prerequisites** | Labs 01–15 complete |

---

## Learning Objectives

By completing this lab, you will:

- Use AI assistants to scaffold Angular components and services
- Configure the Angular CLI MCP Server for IDE integration
- Apply AI-assisted refactoring and debugging workflows
- Establish guardrails for AI-generated code in enterprise projects

---

## Exercise 1: AI-Assisted Component Scaffolding

### Step 1.1 — Generate a Reports feature

Using your AI assistant (GitHub Copilot, Cursor, or ChatGPT), prompt:

```
Create a standalone Angular 21 ReportsComponent for an Employee Management Portal.
Requirements:
- Uses signals for state
- Displays employee count by department in a Material table
- Uses modern control flow (@if, @for)
- OnPush change detection
- Injects EmployeeStore
- No NgModule
```

### Step 1.2 — Review AI output

Before accepting generated code, verify:

- [ ] `standalone: true` on component
- [ ] Signal syntax `()` in templates
- [ ] `@for` with `track` expression
- [ ] Imports array includes all Material modules used
- [ ] No deprecated `*ngIf` / `*ngFor`
- [ ] No hardcoded API URLs (uses `API_CONFIG` token)

### Step 1.3 — Integrate and test

```bash
ng generate component reports/reports --standalone
```

Merge AI suggestions into the generated scaffold. Add route:

```typescript
{
  path: 'reports',
  canActivate: [authGuard, roleGuard(['admin', 'manager'])],
  loadComponent: () => import('./reports/reports.component').then(m => m.ReportsComponent),
}
```

---

## Exercise 2: AI-Assisted Refactoring

### Step 2.1 — Refactor prompt

Select `employee-list.component.ts` and ask AI:

```
Refactor this component to:
1. Remove all business logic — move to EmployeeStore
2. Use signal inputs/outputs only
3. Apply OnPush change detection
4. Keep template under 50 lines by extracting sub-components
Show only the diff, explain each change.
```

### Step 2.2 — Human review checklist

| AI Change | Accept? | Reason |
|-----------|---------|--------|
| Moved filter logic to store | Yes/No | |
| Created `EmployeeTableComponent` | Yes/No | |
| Changed public API | Yes/No | |
| Added unnecessary abstraction | Yes/No | |

**Rule:** Never accept AI refactors without reading every line.

---

## Exercise 3: AI-Assisted Debugging

### Step 3.1 — Introduce a bug (intentionally)

In `employee-form.component.ts`, change:

```typescript
// Bug: signal not invoked
employees = this.employeeStore.employees; // missing ()
```

### Step 3.2 — Debug with AI

Paste the error/symptom into AI:

```
My Angular 21 template shows [object Object] instead of employee names.
Component uses signals. Template: {{ employees() }}
Here's the component class: [paste code]
```

### Step 3.3 — Verify the fix

Confirm AI identifies missing `()` or incorrect signal access.

Restore correct code after exercise.

---

## Exercise 4: Angular CLI MCP Server Setup

### Step 4.1 — Install MCP Server

Follow [Angular CLI MCP documentation](https://angular.dev/ai/mcp):

```bash
npm install -g @angular/cli
ng mcp
```

Or configure in Cursor/VS Code MCP settings:

```json
{
  "mcpServers": {
    "angular-cli": {
      "command": "ng",
      "args": ["mcp"]
    }
  }
}
```

### Step 4.2 — Explore available tools

List MCP tools available (typical set):

| Tool | Purpose |
|------|---------|
| `generate_component` | Scaffold standalone components |
| `generate_service` | Create injectable services |
| `get_best_practices` | Retrieve Angular style guide guidance |
| `search_docs` | Query angular.dev documentation |

### Step 4.3 — Generate via MCP

Use MCP to generate a `DepartmentApiService`:

```
Generate a standalone Angular service DepartmentApiService that:
- Injects ApiService
- Has getAll(), getById(id), create(), update(), delete()
- Returns Observables
- Follows the same pattern as EmployeeApiService
```

Wire into a `DepartmentStore` following Lab 13 patterns.

---

## Exercise 5: AI Guardrails for Enterprise

### Step 5.1 — Create team AI guidelines

Create `docs/ai-development-guidelines.md`:

```markdown
# AI-Assisted Development Guidelines

## Do
- Use AI for scaffolding, boilerplate, and test generation
- Always review generated code against Angular 21 style guide
- Run `ng build` and unit tests after AI changes
- Keep prompts specific: mention standalone, signals, control flow

## Don't
- Paste proprietary code into public AI tools without approval
- Accept AI-generated security code (auth, crypto) without senior review
- Let AI add npm packages without license/security review
- Skip code review because "AI wrote it"

## Required Checks Before Merge
- [ ] No secrets in code
- [ ] Standalone architecture maintained
- [ ] Tests pass
- [ ] Accessibility spot-check
- [ ] Peer review completed
```

---

## Exercise 6: Angular Skills & Agent Workflow

### Step 6.1 — Document your AI workflow

In `docs/ai-development-guidelines.md`, add your personal workflow:

1. **Plan** — Describe feature in prompt with constraints
2. **Scaffold** — AI generates component/service structure
3. **Review** — Check against module lab patterns
4. **Integrate** — Wire routes, services, store
5. **Test** — Manual + `ng test` + Lighthouse
6. **Refine** — AI assists with edge cases only

### Step 6.2 — Compare: with vs without AI

Time yourself building a `SettingsComponent` with:

- **Round A:** Manual (no AI) — 15 minutes
- **Round B:** AI-assisted — 15 minutes

Record in `docs/ai-development-guidelines.md`:

| Metric | Manual | AI-Assisted |
|--------|--------|-------------|
| Time to compile | | |
| Lines of code | | |
| Bugs found | | |
| Quality (1-5) | | |

---

## Verification Checklist

- [ ] Reports feature created with AI assistance and reviewed
- [ ] Refactoring prompt applied with human review documented
- [ ] Debugging exercise completed with AI help
- [ ] Angular CLI MCP Server configured (or setup documented)
- [ ] `DepartmentApiService` generated via MCP or AI
- [ ] `ai-development-guidelines.md` created with team rules
- [ ] AI workflow and comparison documented

---

## Stretch Goals

1. Use AI to generate unit tests for `EmployeeStore`.
2. Create a Cursor rule (`.cursor/rules/angular.md`) enforcing standalone + signals.
3. Use MCP `search_docs` to answer: "What is `linkedSignal` in Angular 21?"

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| MCP server not connecting | Verify `ng mcp` runs; check IDE MCP config JSON |
| AI generates NgModule code | Add "standalone only, no NgModule" to every prompt |
| AI hallucinates APIs | Cross-check with angular.dev via MCP search |

---

## Course Completion

Congratulations! You have completed all 16 labs.

### Final Portal Checklist

- [ ] Standalone Angular 21 architecture
- [ ] Modern control flow throughout
- [ ] Signal-based state management
- [ ] Reactive forms with validation
- [ ] Routing with guards and resolvers
- [ ] REST API integration with interceptors
- [ ] Angular Material UI
- [ ] Accessibility compliance
- [ ] AI-assisted development workflow

### Recommended Next Steps

1. Complete take-home **assignments/** (when provided)
2. Compare your solution with `solutions/`
3. Deploy to Azure Static Web Apps or similar
4. Add E2E tests with Playwright or Cypress

---

**Training complete. You now have a production-ready Employee Management Portal and enterprise Angular 21 skills.**
