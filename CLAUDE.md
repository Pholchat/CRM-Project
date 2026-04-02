# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

ClientPulse CRM — a single-page, AI-native CRM web application. The entire app lives in one `index.html` file (~1470 lines) with embedded CSS and JavaScript. Dependencies: Google Fonts (Inter) and Supabase JS v2 (CDN) for authentication.

## Commands

```bash
# Run the app (serves on port 3000)
npm run dev          # npx serve -s -p 3000

# No tests, linter, or type-checker configured
# Manual testing only — open http://localhost:3000 in a browser
# Note: auth requires serving via HTTP (not file:// protocol)
```

## Architecture

**Single-file architecture**: all CSS (`<style>`), HTML (`<body>`), and JS (`<script>`) are inline in `index.html`.

**Authentication**: Supabase Auth (email/password). Login screen gates the entire CRM. Session checked on load via `getSession()`. Credentials in `env.local`.

**Data layer**: In-memory JS arrays (`customers`, `deals`, `leads`, `products`, `activities`, `workflows`, `documents`, `esignatures`, etc.). No backend persistence — state resets on reload.

**Routing**: Client-side page switching via `navigate(pageName)`. Pages are `<div class="page" id="page-{name}">` elements toggled by `.active` class. 12 pages: dashboard, contacts, pipeline, leads, ai-copilot, analytics, reports, communication, workflows, products, admin, settings.

**Rendering pattern**: Full re-render on data change — each `render*()` function clears `innerHTML` and rebuilds from the data arrays. No virtual DOM.

**Modal system**: Single `openModal(type)` function builds form HTML based on type string (`addContact`, `addDeal`, `addLead`, `addProduct`).

**Key utility functions**: `esc(text)` for XSS prevention, `showToast(msg, type)` for notifications, `getInitials(name)` and `getColor(name)` for avatar generation.

## Code Style (Critical)

- **ES5 only** — `var`, `function(){}`, string concatenation. No `let`/`const`, arrow functions, template literals, or destructuring.
- **No external JS/CSS libraries** — vanilla JS, inline styles. Only CDN exception: Supabase JS v2 for auth.
- **No code comments** unless explicitly requested.
- **4-space indentation** throughout.
- **Dark glassmorphism theme** — backgrounds use `rgba()` with `backdrop-filter: blur()`, accent colors are indigo-500 (`#6366f1`) and purple-500 (`#a855f7`).
- **Class naming**: lowercase hyphen-separated (`.stat-card`, `.btn-primary`).
- **XSS**: Always use `esc()` when inserting user data into DOM via innerHTML.
- **No `alert()`/`confirm()`/`prompt()`** — use `showToast()` instead.

## Implementation Status

See `Plan.md` for detailed phase-by-phase tracking. **All feature phases (0-8) are 100% complete.** Phase 9 (testing & launch) is in progress. All 40 PRD features have been implemented.
