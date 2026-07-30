# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A [Slidev](https://sli.dev) presentation for a talk at Laravel Wales, September 2026. It is a slide deck, not an application: there is no test suite, no linter, and no build output other than a static SPA.

**The talk:** "Waiting is a Feature — modelling processes that stop, in Laravel". It argues that frameworks, queues, and tutorials are all about making things *run*, while real business processes spend almost all of their wall-clock life *waiting* — on a person, a payment provider, a courier, or a date. We model the running carefully and the waiting with a `status` column. Every slide is evidence for that thesis; the payoff is the `juststeveking/workflow-engine` package.

Four acts: the code we already wrote (status enums, scattered state machines) → four kinds of waiting → a spec for first-class waiting → the package, then an honest section on what bites you. Keep that spine intact when editing; slides serve the argument, not the API.

**Running example:** order #4471 — 54h 21m wall clock, 0.89s of actual execution. Those numbers appear on three slides and inside [components/Timeline.vue](components/Timeline.vue). If you change one, change all of them.

## Commands

The lockfile is `bun.lock`, so use bun locally:

```
bun install
bun run dev      # slidev --open, serves at http://localhost:3030
bun run build    # static SPA into dist/
bun run export   # PDF/PNG/PPTX export
```

Deploy configs ([netlify.toml](netlify.toml), [vercel.json](vercel.json)) hardcode `npm run build` — that's fine since the script bodies are package-manager agnostic, but keep `package.json` scripts working under plain npm.

`bun run export` needs `playwright-chromium`, which is **not** in `dependencies` and is not currently installed; Slidev will prompt to install it on first export. [pnpm-workspace.yaml](pnpm-workspace.yaml) pre-approves its postinstall build script for anyone using pnpm instead.

There is nothing to lint or test. Verification means running `bun run dev` and looking at the slide.

## Structure

- [slides.md](slides.md) — the entire deck, 43 slides. Slides are separated by `---`; per-slide YAML frontmatter follows each separator and sets `layout`, `class`, etc. The **first** frontmatter block is deck-wide config (theme, title, `duration: 35min`) *and* the cover slide's own frontmatter.
- [theme/](theme/) — the custom local theme (see below). Owns every colour, font and layout.
- [components/Timeline.vue](components/Timeline.vue) — the order #4471 wall-clock bar. Owns the segment data as a default prop. `scale="linear"` is the honest, unreadable version used for the cold open; `scale="log"` is the labelled version used for analysis. Also takes `labels` / `notes` / `height`, and `reveal` — `none` (default, all at once), `auto` (segments wipe in left to right on slide enter, `stagger` ms apart) or `click` (one segment per click). `reveal="click"` claims one click per segment from the slide's click budget, so any `v-click` after it on the same slide shifts along and the presenter notes' `[click]` markers need updating to match.
- [components/BigStat.vue](components/BigStat.vue) — a large figure with label and sub-label, `tone` mapped to the same four colours.
- [pages/](pages/), [snippets/](snippets/), [components/Counter.vue](components/Counter.vue) — leftover Slidev starter files. Nothing references them; safe to delete.

Components in [components/](components/) are auto-registered by filename — no import needed in markdown.

## The theme

`slidev-theme-juststeveking`, a **local** theme referenced as `theme: ./theme` in the headmatter. Slidev resolves any theme name starting with `.` relative to the entry file's directory, reads [theme/package.json](theme/package.json) for `slidev.defaults`, auto-imports `styles/index.{ts,js,css}`, and globs `layouts/*.vue` to override built-ins.

Design tokens are lifted verbatim from juststeveking.com's Tailwind v4 theme layer, so the deck and the site share one palette:

| Token | Value | Use |
|---|---|---|
| `--jsk-bg-deep` | `#0b0a10` | slide background |
| `--jsk-bg-surface` | `#16141f` | code blocks, chips, terminal |
| `--jsk-heading` | `#f3f1f8` | headings, `<strong>` |
| `--jsk-body` | `#aca6be` | body copy |
| `--jsk-muted` | `#918aa8` | captions, sub-labels |
| `--jsk-accent` | `#a78bfa` | eyebrows, links, rules, inline code |
| `--jsk-border-subtle` / `--jsk-border-strong` | `#262233` / `#635d80` | borders |

The site's signature is **serif body, sans headings** — Source Serif 4 for copy, Geist for headings at tight tracking, Geist Mono for code. Preserve that inversion; it is most of what makes the deck read as his.

Also load-bearing: `--wf-run` / `--wf-signal` / `--wf-human` / `--wf-sleep` (plus `-soft` and `-faint` variants) in [theme/styles/tokens.css](theme/styles/tokens.css). These encode the talk's four kinds of waiting and must agree across the legend, `Timeline.vue`, and the inline spans in slides.md. `sleep` is deliberately the brand violet — the longest wait wears the accent colour.

Conventions:

- **The theme is dark-only**, like the site. `colorSchema: dark` is set in theme defaults; there is no light variant to keep in sync.
- **A markdown `######` (h6) is the accent eyebrow.** Section and cover slides use it for "Part one", "Laravel Wales · September 2026", etc.
- **Fonts are self-hosted** in [theme/fonts/](theme/fonts/) (latin subset, ~103KB total, both OFL). `fonts.provider: none` in theme defaults stops Slidev injecting a Google Fonts `<link>` — without it Slidev fetches Roboto/Fira Code by default and the deck stops being offline-safe. `font-display: block`, because a flash of fallback text mid-talk is worse than a few ms of nothing.
- **No `<style>` blocks in slides.md.** Anything reusable belongs in [theme/styles/utilities.css](theme/styles/utilities.css) (`.rule`, `.kicker`, `.tag`, `.terminal`, `.lede`, `.dim`, `.wf-*`).
- Slidev also auto-loads a `style.css` at the *project root* if one exists. There deliberately isn't one — the theme owns styling.

## Slidev conventions that matter here

- Markdown, Vue templates, and UnoCSS attributify all mix freely in the same file. Attribute-style utilities (`border="~ main rounded-md"`, `hover:bg="gray-400 opacity-20"`, `m="t-4"`) are the house style, not a mistake.
- A `<script setup>` block inside `slides.md` is scoped to the slide it appears on, as is a `<style>` block.
- The last HTML comment (`<!-- ... -->`) on a slide becomes presenter notes. Earlier comments on the same slide are just comments. `[click]` markers inside notes sync note reveal to click animations.
- `components.d.ts` and `dist/` are generated — both gitignored.
- Built-in layouts live in `node_modules/@slidev/client/layouts/`. They are bare `<div class="slidev-layout NAME"><slot/></div>` wrappers — all appearance comes from theme CSS, so only override a layout when you need extra DOM. Note the class for `two-cols` is `.two-columns`, not `.two-cols`.

Two deliberate constraints on this deck:

- **No remote assets.** Every visual is CSS or a local component. Meetup wifi is not to be trusted, and `slidev build` output must work offline.
- **Prefer explicit `v-click` on each child over a `<v-clicks>` wrapper** inside grids and `space-y-*` stacks — the wrapper element becomes the grid/flex child and collapses the layout.

Presenter notes are the second deliverable, not an afterthought: most slides carry the actual spoken argument and `[click]` markers that sync to the reveals. Keep them in step when you edit a slide's clicks.
