# AGENTS.md — AI Coding Agent Guide

This file documents conventions, commands, and patterns for AI coding agents working in this repository.

---

## Project Overview

A personal blog built with **Astro 6**, **Tailwind CSS v4**, and **DaisyUI v5**. Blog posts are Markdown files under `src/pages/posts/`. No framework components (React/Vue/Svelte) — pure Astro `.astro` components only. Package manager is **Bun**.

---

## Commands

### Development

```sh
bun dev          # Start dev server at localhost:4321
bun build        # Build production site to ./dist/
bun preview      # Preview production build locally
```

### Linting & Formatting

```sh
bun check        # Run Biome: lint + format + organize imports (auto-fixes in place)
```

> There are **no tests** in this project. `bun check` is the only verification step.

### Astro CLI

```sh
bun astro <cmd>          # Run Astro CLI (e.g. astro add, astro check)
bun astro -- --help      # Help
```

---

## Tech Stack

| Tool | Version | Role |
|------|---------|------|
| Astro | ^6.0.3 | Static site framework |
| Tailwind CSS | ^4.2.1 | Utility-first CSS (via Vite plugin) |
| DaisyUI | ^5.5.19 | Component class library for Tailwind |
| @tailwindcss/typography | ^0.5.19 | Prose styling for Markdown content |
| Biome | ^2.4.6 | Linter + formatter (replaces ESLint/Prettier) |
| Bun | >=1.x | Runtime & package manager |
| Node | >=22.12.0 | Engine requirement |

---

## Project Structure

```
/
├── public/                   # Static assets (favicon.svg, favicon.ico, etc.)
├── src/
│   ├── layouts/
│   │   ├── Base.astro        # Root HTML shell (navbar, footer, DaisyUI nav)
│   │   └── Post.astro        # Blog post layout (wraps Base, renders frontmatter)
│   ├── pages/
│   │   ├── index.astro       # Home page — lists posts sorted by date
│   │   └── posts/
│   │       └── *.md          # Blog post Markdown files
│   └── styles/
│       └── global.css        # Tailwind + DaisyUI + typography imports
├── astro.config.mjs          # Astro config (Tailwind Vite plugin)
├── biome.json                # Biome lint/format config
├── tsconfig.json             # Extends astro/tsconfigs/strict
└── package.json
```

---

## Code Style

### Formatter (Biome)
- **Indent**: spaces (not tabs)
- **Quote style (JS/TS)**: double quotes `""`
- **Imports**: auto-organized by Biome's `organizeImports` action
- Always run `bun check` after editing `.astro`, `.ts`, `.js`, `.css`, or `.md` files

### TypeScript
- Config extends `astro/tsconfigs/strict` — strict mode is ON
- Never use `as any`, `@ts-ignore`, or `@ts-expect-error`
- Type imports: use `import type` where possible (enforced in non-Astro files; disabled in `.astro` by Biome override)

### Astro Components
- Frontmatter (script) lives between `---` fences at top of file
- Props are accessed via `Astro.props` — destructure immediately
- CSS is handled via Tailwind/DaisyUI utility classes inline on elements — no `<style>` blocks
- Layouts receive `title` prop; `Post.astro` receives `frontmatter` prop from Markdown
- `<slot />` is used for child content injection in layouts
- No UI framework components (React, Vue, etc.) — Astro only

### Astro Biome Overrides
These rules are **disabled** for `.astro` files (don't fix them):
- `style/useConst` — off
- `style/useImportType` — off
- `correctness/noUnusedVariables` — off
- `correctness/noUnusedImports` — off

### CSS / Styling
- `src/styles/global.css` uses `@import "tailwindcss"`, `@plugin "daisyui"`, `@plugin "@tailwindcss/typography"` — do not change this pattern
- Tailwind is loaded via `@tailwindcss/vite` Vite plugin, **not** PostCSS
- Use DaisyUI component classes (`btn`, `navbar`, `divider`, `prose`, etc.) for UI elements
- Use `@tailwindcss/typography`'s `prose` class for Markdown-rendered content

### Naming Conventions
- **Component files**: PascalCase (e.g., `Base.astro`, `Post.astro`)
- **Page files**: kebab-case (e.g., `first-post.md`, `index.astro`)
- **Variables**: camelCase in frontmatter scripts
- **CSS classes**: Tailwind/DaisyUI utility strings — no custom class names unless necessary

---

## Blog Posts (Markdown)

All posts live in `src/pages/posts/*.md`. Required frontmatter:

```yaml
---
layout: ../../layouts/Post.astro
title: Post Title
date: YYYY-MM-DD
draft: false
---
```

- `layout` must point to `../../layouts/Post.astro` (relative from the post file)
- `date` format: ISO 8601 (`YYYY-MM-DD`)
- `draft: false` — there is no draft-filtering logic yet, but include the field for consistency
- The site language is Japanese (`<html lang="ja">`); post content may be in Japanese

---

## Routing

Astro file-based routing applies:
- `src/pages/index.astro` → `/`
- `src/pages/posts/my-post.md` → `/posts/my-post`

The index page uses `import.meta.glob` to collect all `.md` files in `posts/` and sorts them by `frontmatter.date` descending.

---

## Verification Checklist (before marking work done)

1. `bun check` passes with no errors
2. `bun build` exits with code 0 (no build errors)
3. Changed `.astro`/`.ts`/`.js` files have no LSP diagnostics errors
4. New blog posts have all required frontmatter fields
