# AGENTS.md — ClientPulse CRM

This file provides guidance for agentic coding agents working in this repository.

## Project Overview

ClientPulse CRM is a single-page, AI-native customer relationship management web application. The entire application is contained in a single `index.html` file (~1470 lines) with embedded CSS and JavaScript. It uses Supabase for authentication.

## Repository Structure

```
index.html    — The entire application (HTML + inline CSS + inline JavaScript)
prd.md        — Product requirements document (40 features across 7 categories)
Plan.md       — Implementation roadmap with phase tracking
AGENTS.md     — This file
CLAUDE.md     — Guidance for Claude Code
package.json  — Dev server config (npx serve)
env.local     — Supabase credentials (do not commit to public repos)
```

## Build / Dev / Test Commands

```bash
# Start dev server
npm run dev     # Serves on port 3000 via npx serve

# No build required — the app is a single static HTML file
# Alternative: open index.html directly in a browser (auth requires server)
```

- **No test runner** — manual testing via browser
- **No linter** — no ESLint, Stylelint, or HTMLHint configured
- **No formatter** — no Prettier or similar tool configured

## External Dependencies

- **Google Fonts** (Inter) via CDN `<link>` tag
- **Supabase JS v2** via CDN `<script>` tag — used for email/password authentication

## Architecture

The app follows a single-file architecture with three inline sections:

1. **CSS** (`<style>` block in `<head>`) — All styles, no external stylesheets
2. **HTML** (`<body>`) — Semantic markup with hyphen-separated class naming
3. **JavaScript** (`<script>` block at end of `<body>`) — Vanilla JS, no frameworks

Data is stored in client-side JavaScript arrays (`customers`, `deals`, `leads`, `products`, `activities`, `workflows`, `documents`, `esignatures`, etc.). No backend persistence — state resets on reload. Supabase is used only for authentication.

### App Structure

1. **Login screen** — Supabase Auth email/password (sign in + sign up), gates the entire CRM
2. **Sidebar** — 12-page navigation with logo, nav sections, user info, logout button
3. **Topbar** — page title, search, live clock, notification icons
4. **Pages** — dashboard, contacts, pipeline, leads, ai-copilot, analytics, reports, communication, workflows, products, admin, settings
5. **Modal system** — single `openModal(type)` function for CRUD forms
6. **Toast notifications** — auto-dismissing success/danger messages

### Key JavaScript Functions

- `esc(text)` — XSS prevention using DOM text node method
- `showToast(message, type)` — displays auto-dismissing notification (2500ms)
- `navigate(page)` — client-side page routing
- `openModal(type)` / `closeModal()` — modal form management
- `renderContacts()`, `renderPipeline()`, `renderLeads()`, etc. — full re-render functions
- `handleLogin()` / `handleSignup()` / `handleLogout()` — Supabase auth
- `checkDuplicateContact()` / `checkDuplicateLead()` — de-duplication on add
- `getInitials(name)` / `getColor(name)` — avatar generation helpers

## Code Style Guidelines

### General

- **Single-file architecture** — keep all CSS, HTML, and JS inside `index.html`
- **No comments in code** unless explicitly requested
- **Indentation**: 4 spaces for CSS, 4 spaces for HTML, 4 spaces for JavaScript
- **Max line length**: keep lines readable; long CSS values on a single line are acceptable
- **No emoji in code** unless explicitly requested (HTML entity references like `&#128100;` are acceptable)

### CSS Conventions

- **Dark theme** using Tailwind CSS color values as design tokens:
  - Background: `#0f172a` (slate-900)
  - Cards: `rgba(30, 41, 59, 0.5)` (slate-800 with opacity)
  - Accent: `#6366f1` (indigo-500), `#a855f7` (purple-500)
  - Text: `#e2e8f0` (slate-200), `#94a3b8` (slate-400), `#64748b` (slate-500)
- **Glassmorphism pattern**: `backdrop-filter: blur()` with semi-transparent backgrounds and subtle borders (`rgba(255,255,255,0.06)`)
- **CSS custom properties** are not used — colors are defined inline. If the project grows, extract to CSS variables.
- **Class naming**: lowercase, hyphen-separated (e.g., `.stat-card`, `.btn-primary`, `.hero-banner`)
- **Responsive breakpoints**:
  - `768px` — 2-column stats, single-column form, stacked hero
  - `480px` — single-column stats
- **Animations**: use CSS `@keyframes` with `ease-in-out` timing. Durations: 0.15–0.4s for interactions, 18–24s for background orbs
- **Border radius**: 10px for inputs/buttons, 16–20px for cards/banners, 20px for badges
- **Font**: `Inter` (Google Fonts CDN), fallback to `Segoe UI` then `sans-serif`

### HTML Conventions

- **Semantic elements**: `<header>`, `<form>`, `<table>`, `<tbody>`, `<thead>`
- **SVG icons** used inline (no icon library dependency)
- **External dependency**: Google Fonts (`Inter`) via `<link>` in `<head>` — this is the only external request besides optional illustration images
- **Form inputs**: always include `id`, `placeholder`, and `required` attributes
- **Event delegation** on `<tbody>` for click handlers rather than per-row listeners
- **No inline event handlers** in HTML (e.g., no `onclick=`); all event binding done in JavaScript via `addEventListener`
- **External images** must include `onerror="this.style.display='none'"` for graceful fallback

### JavaScript Conventions

- **ES5-compatible syntax** — use `var`, `function() {}`, no arrow functions, no template literals, no `let`/`const`
- **No modules** — everything is in global scope within a single `<script>` block
- **Variable declarations**: `var` only, declared at top of scope
- **DOM queries**: cache references at the top of the script (`var form = document.getElementById(...)`)
- **Data model**: array of plain objects with fixed schema:
  ```javascript
  { name: String, email: String, phone: String, company: String }
  ```
- **XSS prevention**: always use `escapeHtml()` helper when inserting user data into the DOM
- **String concatenation** for building HTML (no template literals)
- **Event handling**:
  - Form submit: `form.addEventListener("submit", function(e) { e.preventDefault(); ... })`
  - Delegated clicks: `tbody.addEventListener("click", function(e) { ... })`
  - Search: `searchInput.addEventListener("input", function() { ... })`
- **Rendering**: full re-render on data change (`tbody.innerHTML = ""` then rebuild). No virtual DOM or diffing.
- **Toast notifications**: `showToast(message, type)` with types `"success"` or `"danger"`, auto-dismiss at 2500ms
- **New customers** are added via `customers.unshift()` (newest first)

### Error Handling

- HTML5 `required` attribute for form validation
- `.trim()` on all input values before use
- Graceful fallback for external images: `onerror="this.style.display='none'"`
- No `try/catch` blocks used — add them if introducing network requests or JSON parsing

## Things to Avoid

- Do not split into multiple files unless explicitly asked
- Do not add npm, Node.js, or any package manager
- Do not add a framework (React, Vue, Angular, etc.)
- Do not add TypeScript or any transpilation step
- Do not introduce external JavaScript libraries or CSS frameworks
- Do not add comments unless requested
- Do not use `let`, `const`, arrow functions, template literals, or other ES6+ syntax
- Do not use `alert()`, `prompt()`, or `confirm()` — use the toast notification system

## Potential Improvements (if asked)

- Extract CSS custom properties for the color palette
- Add `localStorage` persistence for customer data
- Add form validation for email format and phone number patterns
- Extract inline SVGs to a reusable icon system
- Add sorting to the customer table columns
- Add pagination for large customer lists
