# Angular 21 Developer Training

> **Enterprise Angular Development Program**  
> **Duration:** 5 Days · 40 Hours  
> **Format:** Instructor-Led Training · Hands-on Labs · Enterprise Capstone Project

---

## Table of Contents

- [Course Overview](#course-overview)
- [Quick Start](#quick-start)
- [Course Objectives](#course-objectives)
- [Target Audience](#target-audience)
- [Prerequisites](#prerequisites)
- [Software Requirements](#software-requirements)
- [Capstone Project](#capstone-project)
- [Module Index](#module-index)
- [Training Schedule](#training-schedule)
- [Hands-on Learning Approach](#hands-on-learning-approach)
- [Repository Structure](#repository-structure)
- [Learning Outcomes](#learning-outcomes)
- [Course Completion](#course-completion)

---

## Course Overview

Welcome to the **Angular 21 Developer Training Program** — a comprehensive, instructor-led course for building **modern, scalable, enterprise-grade Angular applications** using Angular 21 features and current best practices.

This course follows a **project-based learning approach**. Participants progressively build a complete **Employee Management Portal** while applying concepts through realistic enterprise scenarios.

### Topics Covered

| Category | Topics |
|----------|--------|
| **Architecture** | Standalone components, dependency injection, service layer design |
| **Reactivity** | Signals, computed state, effects, RxJS, `toSignal()` / `toObservable()` |
| **Templates** | Modern control flow (`@if`, `@for`, `@switch`), directives, pipes |
| **Data & Forms** | Reactive forms, validation, dynamic forms, HttpClient, interceptors |
| **Navigation** | Routing, lazy loading, functional guards, resolvers |
| **Enterprise** | State management, Angular Material, performance, accessibility, AI-assisted development |

### Time Allocation (40 Hours)

| Activity | Hours |
|----------|-------|
| Presentations & demos | ~14 |
| Hands-on labs | ~18 |
| Q&A, breaks & reviews | ~5 |
| Assignments & capstone polish | ~3 |

---

## Quick Start

### For Participants

1. Complete [Software Requirements](#software-requirements) below.
2. Clone this repository and open it in VS Code.
3. Review the [Module Index](#module-index) — each module has a presentation and a lab.
4. Start with [Lab 01](labs/Module-01-Angular-Fundamentals-and-Architecture.md) on Day 1.
5. Work through labs **in order** — each builds on the previous.

### For Instructors

1. Deliver the PDF from `presentations/` for each module.
2. Guide participants through the matching lab in `labs/`.
3. Allow ~15 minutes at end of each day for solution review.
4. Pair participants for Labs 08, 10, and 12 (highest complexity).

### Pre-Course Setup Commands

```bash
node --version          # Requires Node.js LTS
npm --version
npm install -g @angular/cli@latest
ng version

# Optional — mock API for Day 5 (Lab 12)
npx json-server --watch resources/mock-api/db.json --port 3000
```

---

## Course Objectives

By the end of this course, participants will be able to:

- Build modern Angular 21 applications using **standalone components**
- Design **enterprise-grade** Angular architectures with clear separation of concerns
- Develop **reusable UI components** with content projection and signal-based inputs/outputs
- Build **reactive applications** using Signals and RxJS appropriately
- Implement **modern control flow** (`@if`, `@for`, `@switch`) in templates
- Consume **REST APIs** using HttpClient, interceptors, and a layered service architecture
- Secure applications using **functional route guards** and role-based authorization
- Build **dynamic, validated forms** with reactive forms and async validators
- Apply **signal-based state management** and evaluate when NgRx is warranted
- Optimize applications with **OnPush**, `@defer`, and performance best practices
- Develop **accessible** Angular applications following WCAG principles
- Leverage **AI tools and MCP** to accelerate scaffolding, debugging, and refactoring

---

## Target Audience

| Role | Relevance |
|------|-----------|
| Frontend / Angular Developers | Deepen Angular 21 skills |
| Full Stack, Java, .NET Developers | Add modern frontend capability |
| React / Vue Developers | Structured transition to Angular |
| Technical Leads & Architects | Enterprise patterns and architecture decisions |

---

## Prerequisites

Participants should have:

- Basic **HTML & CSS** knowledge
- Basic **JavaScript / TypeScript** knowledge
- Familiarity with **object-oriented programming**
- Basic understanding of **REST APIs** (recommended)

No prior Angular experience is required.

---

## Software Requirements

Install before Day 1:

| Tool | Purpose | Notes |
|------|---------|-------|
| [Node.js](https://nodejs.org) (Latest LTS) | Runtime | v20+ recommended |
| npm | Package manager | Included with Node.js |
| [Angular CLI](https://angular.dev/tools/cli) | Project scaffolding | `npm install -g @angular/cli@latest` |
| [Visual Studio Code](https://code.visualstudio.com) | IDE | Install Angular Language Service extension |
| Git | Version control | |
| Chrome or Edge | DevTools, debugging | |
| [Postman](https://www.postman.com) | API testing | Used from Day 5 |
| GitHub account | Source control | Optional but recommended |
| GitHub Copilot / AI assistant | AI-assisted labs | Recommended for Module 16 |

---

## Capstone Project

Throughout the course, participants build an **Employee Management Portal** — a multi-feature enterprise application that evolves each day as new Angular concepts are introduced.

### Application Features

| Feature | Introduced |
|---------|------------|
| Authentication & authorization | Day 4 |
| Dashboard with live metrics | Day 2 |
| Employee management (CRUD) | Day 3–5 |
| Department management | Day 5 |
| User management | Day 5 |
| Reports | Day 5 |
| Settings | Day 4 |

### Capstone Evolution by Day

| Day | What You Build |
|-----|----------------|
| **Day 1** | App shell, layout components, employee list with modern control flow |
| **Day 2** | Signal-based dashboard, custom directives/pipes, reusable card components |
| **Day 3** | Service layer, dependency injection, employee registration and edit forms |
| **Day 4** | Multi-page routing, auth guards, lazy-loaded features, RxJS pipelines |
| **Day 5** | REST API integration, global state store, Material UI, accessibility, AI workflow |

---

## Module Index

All 16 modules include a **presentation** (PDF) and a **hands-on lab** (Markdown).

| # | Module | Presentation | Lab | Day |
|---|--------|-------------|-----|-----|
| 1 | Angular Fundamentals & Architecture | [PDF](presentations/Module-01-Angular-Fundamentals-and-Architecture.pdf) | [Lab](labs/Module-01-Angular-Fundamentals-and-Architecture.md) | 1 |
| 2 | Components & Templates | [PDF](presentations/Module-02-Components-and-Templates.pdf) | [Lab](labs/Module-02-Components-and-Templates.md) | 1 |
| 3 | Modern Angular Control Flow | [PDF](presentations/Module-03-Modern-Angular-Control-Flow.pdf) | [Lab](labs/Module-03-Modern-Angular-Control-Flow.md) | 1 |
| 4 | Directives & Pipes | [PDF](presentations/Module-04-Directives-and-Pipes.pdf) | [Lab](labs/Module-04-Directives-and-Pipes.md) | 2 |
| 5 | Signals & Angular Reactivity | [PDF](presentations/Module-05-Signals-and-Angular-Reactivity.pdf) | [Lab](labs/Module-05-Signals-and-Angular-Reactivity.md) | 2 |
| 6 | Component Communication & Content Projection | [PDF](presentations/Module-06-Component-Communication-and-Content-Projection.pdf) | [Lab](labs/Module-06-Component-Communication-and-Content-Projection.md) | 2 |
| 7 | Dependency Injection & Services | [PDF](presentations/Module-07-Dependency-Injection-and-Services.pdf) | [Lab](labs/Module-07-Dependency-Injection-and-Services.md) | 3 |
| 8 | Forms in Angular | [PDF](presentations/Module-08-Forms-in-Angular.pdf) | [Lab](labs/Module-08-Forms-in-Angular.md) | 3 |
| 9 | RxJS Essentials for Angular | [PDF](presentations/Module-09-RxJS-Essentials-for-Angular.pdf) | [Lab](labs/Module-09-RxJS-Essentials-for-Angular.md) | 4 |
| 10 | Routing & Navigation | [PDF](presentations/Module-10-Routing-and-Navigation.pdf) | [Lab](labs/Module-10-Routing-and-Navigation.md) | 4 |
| 11 | Route Guards & Data Resolution | [PDF](presentations/Module-11-Route-Guards-and-Data-Resolution.pdf) | [Lab](labs/Module-11-Route-Guards-and-Data-Resolution.md) | 4 |
| 12 | HTTP Communication & API Integration | [PDF](presentations/Module-12-HTTP-Communication-and-API-Integration.pdf) | [Lab](labs/Module-12-HTTP-Communication-and-API-Integration.md) | 5 |
| 13 | State Management | [PDF](presentations/Module-13-State-Management.pdf) | [Lab](labs/Module-13-State-Management.md) | 5 |
| 14 | Angular Material & Performance Optimization | [PDF](presentations/Module-14-Angular-Material-and-Performance-Optimization.pdf) | [Lab](labs/Module-14-Angular-Material-and-Performance-Optimization.md) | 5 |
| 15 | Accessibility in Angular | [PDF](presentations/Module-15-Accessibility-in-Angular.pdf) | [Lab](labs/Module-15-Accessibility-in-Angular.md) | 5 |
| 16 | AI-Powered Development & MCP Integration | [PDF](presentations/Module-16-AI-Powered-Angular-Development-and-MCP-Integration.pdf) | [Lab](labs/Module-16-AI-Powered-Angular-Development-and-MCP-Integration.md) | 5 |

> Full lab details, durations, and instructor notes: [`labs/README.md`](labs/README.md)

---

## Training Schedule

### Day 1 — Angular Fundamentals, Components & Modern Control Flow

**Theme:** Project foundation and template essentials

| Module | Topics |
|--------|--------|
| **1 — Angular Fundamentals & Architecture** | Angular ecosystem, project structure, CLI, bootstrap process, building blocks (components, templates, directives, services, pipes) |
| **2 — Components & Templates** | Standalone components, metadata, composition, interpolation, property/event/two-way binding, lifecycle hooks, ViewChild |
| **3 — Modern Angular Control Flow** | `@if` / `@else`, `@for` with tracking, `@switch`, migration from `*ngIf` / `*ngFor` / `*ngSwitch` |

**Labs:** [01](labs/Module-01-Angular-Fundamentals-and-Architecture.md) · [02](labs/Module-02-Components-and-Templates.md) · [03](labs/Module-03-Modern-Angular-Control-Flow.md)

**Outcomes:** Angular 21 app scaffolded, multi-component layout, modern control flow throughout templates.

---

### Day 2 — Directives, Pipes, Signals & Component Communication

**Theme:** Reusable components and reactive state

| Module | Topics |
|--------|--------|
| **4 — Directives & Pipes** | Built-in directives (`ngClass`, `ngStyle`), custom attribute directives, built-in and custom pipes, pipe performance |
| **5 — Signals & Angular Reactivity** | Writable/computed signals, effects, signal inputs/outputs, `linkedSignal()`, signal best practices |
| **6 — Component Communication** | Parent-child data flow, shared state via services, single/multi-slot content projection, anti-patterns |

**Labs:** [04](labs/Module-04-Directives-and-Pipes.md) · [05](labs/Module-05-Signals-and-Angular-Reactivity.md) · [06](labs/Module-06-Component-Communication-and-Content-Projection.md)

**Outcomes:** Signal-based dashboard, custom directives and pipes, reusable projected card/panel components.

---

### Day 3 — Dependency Injection, Services & Forms

**Theme:** Business logic layer and data entry

| Module | Topics |
|--------|--------|
| **7 — Dependency Injection & Services** | DI concepts, provider scope, injector hierarchy, `inject()` API, injection tokens, service patterns |
| **8 — Forms in Angular** | Template-driven vs reactive forms, `FormGroup`/`FormArray`, built-in/custom/async validators, dynamic forms, signal-based form state |

**Labs:** [07](labs/Module-07-Dependency-Injection-and-Services.md) · [08](labs/Module-08-Forms-in-Angular.md)

**Outcomes:** Service layer with DI, employee registration and edit forms with full validation.

---

### Day 4 — RxJS, Routing & Navigation

**Theme:** Multi-page application and security

| Module | Topics |
|--------|--------|
| **9 — RxJS Essentials** | Observables, subscription lifecycle, `map`/`filter`/`tap`/`switchMap`/`mergeMap`/`catchError`, Subjects, Signals ↔ RxJS interop |
| **10 — Routing & Navigation** | Route configuration, route/query parameters, nested routes, programmatic navigation, lazy loading, route-level providers |
| **11 — Route Guards & Data Resolution** | Functional `canActivate`/`canDeactivate`, auth patterns, role-based access, route resolvers, data prefetching |

**Labs:** [09](labs/Module-09-RxJS-Essentials-for-Angular.md) · [10](labs/Module-10-Routing-and-Navigation.md) · [11](labs/Module-11-Route-Guards-and-Data-Resolution.md)

**Outcomes:** Multi-page portal with lazy-loaded features, authentication guards, and API resolvers.

---

### Day 5 — API Integration, State Management, Performance & AI

**Theme:** Production-ready enterprise application

| Module | Topics |
|--------|--------|
| **12 — HTTP & API Integration** | HttpClient, CRUD, error handling, interceptors, loading/retry strategies, API service layer |
| **13 — State Management** | Component, service, and signal-based state; store patterns; choosing Signals vs Services vs NgRx |
| **14 — Angular Material & Performance** | Material components, theming, OnPush, `@defer`, rendering optimization |
| **15 — Accessibility** | ARIA, keyboard navigation, semantic HTML, accessible Material usage |
| **16 — AI & MCP Integration** | AI-assisted scaffolding, Angular CLI MCP Server, debugging, refactoring guardrails |

**Labs:** [12](labs/Module-12-HTTP-Communication-and-API-Integration.md) · [13](labs/Module-13-State-Management.md) · [14](labs/Module-14-Angular-Material-and-Performance-Optimization.md) · [15](labs/Module-15-Accessibility-in-Angular.md) · [16](labs/Module-16-AI-Powered-Angular-Development-and-MCP-Integration.md)

**Outcomes:** REST API–connected portal with global state, Material UI, accessibility compliance, and documented AI development workflow.

---

## Hands-on Learning Approach

The course follows a **learn-by-building** methodology — no isolated hello-world examples. Every concept is applied to the Employee Management Portal.

```
Day 1 ──► App shell, components, control flow
Day 2 ──► Signals, directives, reusable UI
Day 3 ──► Services, DI, forms
Day 4 ──► RxJS, routing, authentication
Day 5 ──► HTTP, state, Material, a11y, AI
```

Each lab includes:

- Learning objectives aligned with the presentation
- Step-by-step exercises with verification checklists
- Stretch goals for advanced participants
- Troubleshooting guides for common classroom issues

---

## Repository Structure

```text
training-angular-v2/
│
├── README.md                 # This file — course overview and schedule
│
├── presentations/            # 16 module slide decks (PDF)          ✅ Available
├── labs/                       # 16 hands-on lab guides (Markdown)   ✅ Available
│   └── README.md               # Lab index, durations, instructor notes
│
├── resources/                  # Supporting assets                   ✅ Available
│   └── mock-api/
│       └── db.json             # Sample data for JSON Server (Lab 12)
│
├── source-code/                # Per-day starter projects            🔜 Planned
├── solutions/                  # Reference implementations           🔜 Planned
├── exercises/                  # Short in-class micro-exercises      🔜 Planned
└── assignments/                # Take-home assignments               🔜 Planned
```

---

## Learning Outcomes

After completing this training, participants will be able to:

- Design and structure **enterprise Angular applications**
- Build **reusable standalone components** with signals and modern templates
- Implement **reactive forms** and **REST API integration** with proper error handling
- Apply **authentication, authorization, and route guards**
- Manage application state with **signal-based stores**
- Optimize performance using **OnPush**, `@defer`, and change detection strategies
- Build **accessible** UIs that meet WCAG guidelines
- Apply **Angular Material** for consistent enterprise UI
- Use **AI tools responsibly** to accelerate development
- Follow **enterprise coding standards** and architectural best practices

---

## Course Completion

Upon successful completion, participants will have:

- A **production-ready Angular 21 application** (Employee Management Portal)
- **Hands-on experience** with enterprise development practices across the full stack of Angular concerns
- Practical skills in **Signals, RxJS, Routing, Forms, State Management, Performance, Accessibility**, and **AI-assisted development**
- A solid foundation for building and leading **modern enterprise Angular** projects

### Recommended Next Steps

1. Compare your solution with reference code in `solutions/` (when available)
2. Complete take-home work in `assignments/` (when available)
3. Add E2E tests with Playwright or Cypress
4. Deploy the portal to Azure Static Web Apps, Netlify, or similar

---

*Angular 21 Developer Training · Enterprise Development Program*
