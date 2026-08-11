> [!NOTE]
> **Archived.** The slides now live in my talks monorepo, where they share one
> Slidev theme with every other deck. This repository is a point-in-time copy
> kept so existing links keep working; it is no longer edited.

# Waiting is a Feature

**Modelling processes that stop, in Laravel** — a 35 minute talk for [Laravel Wales](https://laravelwales.co.uk), September 2026.

Steve McDougall · [juststeveking.com](https://juststeveking.com) · @juststeveking

## The argument

Frameworks, queues, and tutorials are all about making things **run**. Real business processes spend almost all of their wall-clock life **waiting** — on a person, on a payment provider, on a courier, on a date. We model the running carefully, and we model the waiting with a `status` column.

The running example is order #4471: **54h 21m** of wall clock, **0.89s** of actual execution. Everything in between is waiting that nothing in our stack has a name for.

The talk is in four acts:

1. **The code we already wrote** — status enums, scattered state machines, and the five ways they fail.
2. **Four kinds of waiting** — running, signal, human, sleep. They are not the same thing and they should not share a column.
3. **If waiting were first-class** — what would have to be true, then `juststeveking/workflow-engine` as one answer.
4. **The bits that will bite you** — webhooks that arrive before you commit, steps that run twice, deploying mid-flight, compensation that itself fails, and when not to use any of this.

The payoff is the package; the spine is the argument. Slides serve the argument.

## Running it

[Slidev](https://sli.dev). The lockfile is `bun.lock`, so use bun locally:

```bash
bun install
bun run dev      # opens http://localhost:3030
```

Press `o` for slide overview, `p` for presenter mode — the presenter notes carry the actual spoken argument, so they are worth having open.

```bash
bun run build    # static SPA into dist/
bun run export   # PDF/PNG/PPTX (prompts to install playwright-chromium on first run)
```

The deck is offline-safe by design: self-hosted fonts, no remote assets, every visual is CSS or a local Vue component. Meetup wifi is not to be trusted.

## What's where

- [slides.md](./slides.md) — the entire deck, 43 slides. The first frontmatter block is both deck config and the cover slide.
- [theme/](./theme/) — `slidev-theme-juststeveking`, a local theme. Owns every colour, font, and layout. Dark only, serif body and sans headings, palette lifted from juststeveking.com.
- [components/Timeline.vue](./components/Timeline.vue) — the order #4471 wall-clock bar. `scale="linear"` is the honest, unreadable version; `scale="log"` is the one you can read.
- [components/BigStat.vue](./components/BigStat.vue) — a large figure with label and sub-label.

Deploys to Netlify ([netlify.toml](./netlify.toml)) or Vercel ([vercel.json](./vercel.json)) as a static SPA out of `dist/`.

## Links from the talk

- Package: `juststeveking/workflow-engine`
- Walkthrough: juststeveking.com/articles/building-an-order-fulfilment-workflow
