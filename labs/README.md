# Angular 21 Developer Training — Hands-on Labs

> **Capstone Project:** Employee Management Portal  
> **Approach:** Progressive, module-aligned labs that build one production-style application across 5 days.

---

## How to Use These Labs

1. **Attend the module presentation** (see `presentations/`) before starting the corresponding lab.
2. **Work in order** — each lab assumes the previous labs are complete.
3. **Use the starter project** from `source-code/` when provided; otherwise create a fresh Angular 21 app as directed in Lab 01.
4. **Compare your work** against reference implementations in `solutions/` (available after instructor review or at end of day).
5. **Time yourself** — duration estimates are per lab, not including breaks.

---

## Lab Index

| Day | Module | Lab | Duration | Focus |
|-----|--------|-----|----------|-------|
| 1 | 1 | [Angular Fundamentals & Architecture](Module-01-Angular-Fundamentals-and-Architecture.md) | 60 min | CLI setup, project structure, building blocks |
| 1 | 2 | [Components & Templates](Module-02-Components-and-Templates.md) | 90 min | Standalone components, data binding, lifecycle |
| 1 | 3 | [Modern Angular Control Flow](Module-03-Modern-Angular-Control-Flow.md) | 60 min | `@if`, `@for`, `@switch` |
| 2 | 4 | [Directives & Pipes](Module-04-Directives-and-Pipes.md) | 75 min | Custom directives, custom pipes |
| 2 | 5 | [Signals & Angular Reactivity](Module-05-Signals-and-Angular-Reactivity.md) | 90 min | Writable/computed signals, effects, linkedSignal |
| 2 | 6 | [Component Communication & Content Projection](Module-06-Component-Communication-and-Content-Projection.md) | 75 min | Inputs/outputs, shared state, ng-content |
| 3 | 7 | [Dependency Injection & Services](Module-07-Dependency-Injection-and-Services.md) | 75 min | Services, providers, inject(), tokens |
| 3 | 8 | [Forms in Angular](Module-08-Forms-in-Angular.md) | 120 min | Reactive forms, validation, dynamic forms |
| 4 | 9 | [RxJS Essentials for Angular](Module-09-RxJS-Essentials-for-Angular.md) | 75 min | Operators, subjects, toSignal/toObservable |
| 4 | 10 | [Routing & Navigation](Module-10-Routing-and-Navigation.md) | 90 min | Routes, params, lazy loading |
| 4 | 11 | [Route Guards & Data Resolution](Module-11-Route-Guards-and-Data-Resolution.md) | 75 min | Functional guards, resolvers, auth |
| 5 | 12 | [HTTP Communication & API Integration](Module-12-HTTP-Communication-and-API-Integration.md) | 90 min | HttpClient, interceptors, CRUD |
| 5 | 13 | [State Management](Module-13-State-Management.md) | 75 min | Signal-based store, service state |
| 5 | 14 | [Angular Material & Performance](Module-14-Angular-Material-and-Performance-Optimization.md) | 90 min | Material UI, OnPush, @defer |
| 5 | 15 | [Accessibility in Angular](Module-15-Accessibility-in-Angular.md) | 45 min | ARIA, keyboard nav, semantic HTML |
| 5 | 16 | [AI-Powered Development & MCP](Module-16-AI-Powered-Angular-Development-and-MCP-Integration.md) | 45 min | AI scaffolding, MCP tools, refactoring |

**Total hands-on time:** ~18 hours (remaining course time covers presentations, Q&A, and assignments)

---

## Capstone Evolution by Day

| Day | Portal Feature Delivered |
|-----|--------------------------|
| 1 | App shell, header, employee list with control flow |
| 2 | Dashboard with signals, reusable cards, custom formatting |
| 3 | Employee service layer, add/edit employee forms |
| 4 | Multi-page navigation, auth guards, lazy-loaded features |
| 5 | REST API integration, global state, Material UI, accessibility |

---

## Lab Document Structure

Every lab follows this format:

- **Objectives** — aligned with presentation learning outcomes
- **Prerequisites** — prior labs and tools required
- **Exercise Steps** — numbered, actionable instructions
- **Verification Checklist** — how to confirm success
- **Stretch Goals** — optional challenges for advanced learners
- **Troubleshooting** — common issues and fixes

---

## Instructor Notes

- Pair participants for Labs 08, 10, and 12 (highest complexity).
- Allow 15-minute solution walkthrough at end of each day.
- Use `assignments/` for take-home work; labs are in-class exercises.
- Mock API: recommend [JSON Server](https://github.com/typicode/json-server) or the provided `resources/mock-api/` when available.
