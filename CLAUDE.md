# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev     # next dev (http://localhost:3000)
npm run build   # next build
npm run start   # serve the production build
npm run lint    # eslint (flat config, no path arg needed)
npx tsc --noEmit  # type-check (no separate script)
```

There is no test runner configured.

## Read the bundled Next.js docs first

This project is on **Next.js 16.4 / React 19.3**, which differs from older Next.js versions (see `AGENTS.md`). Before writing Next.js code, read the matching guide in `node_modules/next/dist/docs/` (`01-app/` is the one that applies; the repo uses the App Router only). Heed deprecation notices there rather than relying on memory.

## Architecture

Currently a fresh `create-next-app` scaffold (App Router, TypeScript, Tailwind v4) — `src/app/page.tsx` is still the template page and `layout.tsx` still has the default metadata. Things that are non-default and easy to miss:

- **Cache Components is on** (`next.config.ts`: `cacheComponents: true`, `partialPrefetching: true`). Data fetching is dynamic by default; opt into caching with the `use cache` directive (plus `cacheLife` / `cacheTag`). Routes are prerendered as a static shell with dynamic content streamed in (PPR is the default), and client navigation uses `<Activity>`, so previously visited routes stay mounted-but-hidden with state preserved. Requires the Node.js runtime — no `runtime = 'edge'`. See `node_modules/next/dist/docs/01-app/03-api-reference/05-config/01-next-config-js/cacheComponents.md`.
- **Tailwind v4 via Turbopack loader**: `next.config.ts` routes `*.css` through `@tailwindcss/turbopack`. Tailwind is configured in CSS (`@import "tailwindcss"` and `@theme inline` in `src/app/globals.css`), not in a `tailwind.config` file. Theme colors are CSS variables (`--background`, `--foreground`) with a `prefers-color-scheme` dark override.
- **Typed route props**: `layout.tsx` uses the globally available `LayoutProps<"/">` type instead of hand-written props; generated into `.next/types` (included by `tsconfig.json`), so run `next dev` or `next build` once if those types are missing.
- Path alias: `@/*` → `./src/*`.
- Fonts: Geist / Geist Mono via `next/font/google`, exposed as `--font-geist-sans` / `--font-geist-mono` and mapped into Tailwind's `font-sans` / `font-mono`.

## Repo notes

- `.env*` files are gitignored.
- `Project1Prompts.docx` (and its Word lock file `~$oject1Prompts.docx`) sit in the repo root but are excluded via `.git/info/exclude` and unrelated to the app build.
