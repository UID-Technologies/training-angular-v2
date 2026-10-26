# Lab 01 — Angular Fundamentals & Architecture

| | |
|---|---|
| **Module** | 1 — Angular Fundamentals & Architecture |
| **Duration** | 60 minutes |
| **Presentation** | `presentations/Module-01-Angular-Fundamentals-and-Architecture.pdf` |

---

## Learning Objectives

By completing this lab, you will:

- Install and verify the Angular CLI toolchain
- Scaffold an Angular 21 standalone application
- Navigate the project structure and explain each key folder
- Identify Angular building blocks in a real project

---

## Prerequisites

- Node.js (Latest LTS) and npm installed
- Visual Studio Code with Angular Language Service extension
- Git installed
- Terminal access with write permissions

---

## Exercise 1: Verify Your Development Environment

### Step 1.1 — Check installed versions

```bash
node --version
npm --version
ng version
```

If Angular CLI is not installed:

```bash
npm install -g @angular/cli@latest
```

### Step 1.2 — Record your versions

Create a file `environment-notes.txt` in your project root and record:

- Node.js version
- npm version
- Angular CLI version

---

## Exercise 2: Create the Employee Management Portal

### Step 2.1 — Generate the application

```bash
ng new employee-portal --standalone --routing --style=scss --ssr=false
cd employee-portal
```

When prompted, accept defaults. Confirm the project uses **standalone** architecture (no `app.module.ts`).

### Step 2.2 — Start the development server

```bash
ng serve -o
```

Confirm the app loads at `http://localhost:4200/`.

### Step 2.3 — Explore the project structure

Open the project in VS Code. Locate and document the purpose of each item:

| Path | Your Notes (1 sentence each) |
|------|------------------------------|
| `src/main.ts` | |
| `src/app/app.config.ts` | |
| `src/app/app.component.ts` | |
| `src/app/app.routes.ts` | |
| `angular.json` | |
| `tsconfig.json` | |
| `package.json` | |

---

## Exercise 3: Understand the Bootstrap Process

### Step 3.1 — Trace application startup

1. Open `src/main.ts` — identify what is bootstrapped.
2. Open `src/app/app.config.ts` — list the providers registered at startup.
3. Open `src/app/app.component.ts` — note the `@Component` metadata: `selector`, `standalone`, `imports`, `templateUrl`.

### Step 3.2 — Answer these questions (write answers in `environment-notes.txt`)

1. What is the root component selector?
2. Where is the router configured?
3. What does `bootstrapApplication()` do compared to legacy `platformBrowserDynamic().bootstrapModule()`?

---

## Exercise 4: Identify Building Blocks

### Step 4.1 — Annotate the starter app

In `app.component.ts`, add comments labeling:

- **Component** — the class itself
- **Template** — the HTML file reference
- **Service** — (none yet; note where you will add one later)

### Step 4.2 — Generate a pipe and a directive (preview only)

```bash
ng generate pipe shared/format-employee-id --standalone
ng generate directive shared/highlight --standalone
```

Do not implement them yet. Open the generated files and note:

- What decorator is used (`@Pipe`, `@Directive`)?
- How does `standalone: true` affect imports?

Delete or keep these files — you will implement them properly in Lab 04.

---

## Exercise 5: Customize the App Shell

### Step 5.1 — Update branding

In `app.component.html`, replace the default Angular welcome content with:

```html
<header class="app-header">
  <h1>Employee Management Portal</h1>
  <p>Angular 21 Enterprise Training — Day 1</p>
</header>
<main>
  <router-outlet />
</main>
```

### Step 5.2 — Add minimal styles

In `app.component.scss`:

```scss
.app-header {
  padding: 1.5rem 2rem;
  background: #1a237e;
  color: white;

  h1 { margin: 0 0 0.25rem; font-size: 1.5rem; }
  p  { margin: 0; opacity: 0.85; font-size: 0.9rem; }
}
```

---

## Verification Checklist

- [ ] `ng serve` runs without errors
- [ ] Browser shows "Employee Management Portal" header
- [ ] You can explain the role of `main.ts`, `app.config.ts`, and `app.component.ts`
- [ ] Project uses standalone components (no NgModule)
- [ ] `environment-notes.txt` contains version info and bootstrap answers

---

## Stretch Goals

1. Configure a path alias `@app` in `tsconfig.json` pointing to `src/app/`.
2. Add Angular ESLint: `ng add @angular-eslint/schematics`.
3. Initialize Git and make your first commit: `git init && git add . && git commit -m "chore: scaffold employee portal"`.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `ng` command not found | Reinstall CLI globally; restart terminal |
| Port 4200 in use | Run `ng serve --port 4300` |
| Node version too old | Upgrade to latest LTS from [nodejs.org](https://nodejs.org) |
| `#` in path causes issues on Windows | Use quotes around paths in terminal commands |

---

## Next Lab

Continue to **[Lab 02 — Components & Templates](Module-02-Components-and-Templates.md)** to build the portal UI with standalone components and data binding.
