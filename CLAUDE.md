# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

"qfremove" — a background removal web app built with Svelte 5, TypeScript, and Vite 8. Currently at scaffold stage (default Vite+Svelte template).

## Commands

- `npm run dev` — start dev server with HMR
- `npm run build` — production build (outputs to `dist/`)
- `npm run preview` — preview production build locally
- `npm run check` — typecheck everything (runs `svelte-check` for app code + `tsc` for node-side config)

No test framework configured yet. No linter configured yet.

## Architecture

- **Svelte 5** with runes API (`$state`, etc.) — not legacy Svelte stores
- **Tailwind CSS 4** via `@tailwindcss/vite` plugin — imported in `src/app.css` with `@import "tailwindcss"`
- **Entry point**: `src/main.ts` mounts `App.svelte` into `#app` div
- **Components**: `src/lib/` for reusable components
- **Styles**: `src/app.css` for global styles (Tailwind base)
- **Static assets**: `public/` for files served as-is (favicon)
- **Imported assets**: `src/assets/` for assets processed by Vite

## TypeScript Setup

Two tsconfig files referenced by root `tsconfig.json`:
- `tsconfig.app.json` — app source (`src/`), extends `@tsconfig/svelte`, `allowJs: true`, `checkJs: true`
- `tsconfig.node.json` — Vite config only (`vite.config.ts`), strict linting rules
