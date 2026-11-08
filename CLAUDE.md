# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Personal Korean tech blog: an Obsidian vault (`content/`) built with a locally customized fork of Quartz 5 (`quartz/`) and deployed to GitHub Pages. See `README.md` for the full list of repo-specific customizations to preserve when upgrading Quartz.

## Commands

```bash
npm ci
npx quartz build --serve            # local preview at localhost:8080 (watches content)
npx quartz build -v -o <dir>        # verbose build into another dir; prints every [emit:<Plugin>] file
npm run check                       # tsc --noEmit + prettier --check
npm test                            # all quartz/**/*.test.ts (node test runner via tsx)
npx tsx --test quartz/components/registry.test.ts   # single test file
```

Node >= 22. `npm run prebuild` / `npx quartz plugin install` installs the `@quartz-community/*` plugins.

## Branches & deploy

- Working/deploy branch is **`v5`**; pushing to it triggers `.github/workflows/deploy.yml` (build → GitHub Pages). `v4` is the old main branch.
- The user reviews changes before committing. Don't commit or push unless asked.

## Architecture

- **All site config lives in `quartz.config.yaml`**: global `configuration` (theme colors, fonts, `ignorePatterns`), the plugin list (`source`, `enabled`, `options`, `order`), and each plugin's `layout` slot. `quartz/plugins/loader/config-loader.ts` reads it, installs and instantiates the plugins, and always prepends the built-in emitters `ComponentResources`, `Assets`, `Static`.
- **Build pipeline** (`quartz/build.ts` → `quartz/processors/emit.ts`): glob `content/` (respecting `ignorePatterns` **and** `.gitignore`) → transformers → filters → emitters. `ComponentResources` runs first, then `PageTypeDispatcher` (content/folder/tag/bases/canvas pages), then all other emitters in parallel.
- **Emitter failures don't fail the build**: `runEmitter` only traces exceptions, so a broken emitter leaves silently incomplete output. Check `public/` directly, or use `-v`.
- **`Assets` emitter** copies every non-`.md` file (excluding page-type extensions `.base`/`.canvas`) that isn't matched by `ignorePatterns`. That's how images in `content/Utillities/attatchments/` and `content/robots.txt` reach the site.
- Styling: tokens come from `theme.colors` in the YAML. Overrides go in `quartz/styles/custom.scss`, which re-declares existing selectors and callout CSS variables (`--color/--border/--bg` from `callouts.scss`) instead of adding new components. Fonts load via `<link>` in `quartz/components/Head.tsx` (`fontOrigin: local`).

## Content gotchas

- `ignorePatterns` must hide only the template notes (`Utillities/**/*.md`). Ignoring the whole `Utillities/attatchments` folder drops every image from the build.
- Any publishable file inside a folder (e.g. a `.base`) makes that folder appear in the explorer. `Utillities/` has no folder page, so keep published files out of it (`DA Map.base` lives at the content root and is embedded by `content/index.md`).
- `content/_Private/` and `content/_Inbox/` are gitignored and therefore never built.
- `explicit-publish` is disabled, so every non-ignored, non-draft note is published.
- `og-image` is disabled because Pretendard isn't on Google Fonts and the build fails. Giscus `categoryId` is still a placeholder.
