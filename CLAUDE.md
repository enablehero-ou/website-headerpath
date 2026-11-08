# CLAUDE.md — website-headerpath

Website-specific guidance. Read alongside the parent CLAUDE.md one level up.

> **🔴 CONTENT RULE — what belongs in CLAUDE.md.**
>
> This file holds **infrastructure + system context only**. NO activity logs, fix
> histories, "added on YYYY-MM-DD" notes, or status updates of any kind. Past work
> lives in git history; current/forward work lives on the GitHub Project
> [headerpath-website tasks](https://github.com/orgs/enablehero-ou/projects/4) — see *Task tracking*.

> **🔴 DEPLOY RULE — one writer, one direction: `dev` → PR → `main` → GitHub Pages.**
>
> Work on `dev`. `/ship` opens a PR to `main`; `/main` squash-merges, which runs
> `.github/workflows/deploy.yml` (GitHub Pages, custom domain via `public/CNAME`), then
> hard-resets `dev` to `main`. **Never push to `main`.** The repo is **public** (free Pages);
> never commit secrets. No per-branch previews — check on `localhost:4321` before `/main`.

> **🔴 HIBERNATION RULE — HeaderPath is hibernating; the site is static brochure only.**
>
> No signup, promo bar, cookie banner, forms, or CTAs. `/pricing` shows the single plan with **no
> buttons** (just a "Contact us" link). The **only** call to action is the `/contact` page (a `mailto:` to
> `brand.email.team`), linked from the header nav, the footer, and in-article "Contact us"
> links. `/signup`, `/schedule` and `/get-started` 301 to `/contact`. Never add a form or
> signup button. Plan prices live in `brand.pricing` and render on `/pricing` only.
> The pre-hibernation site is the git tag `archive/vercel-pre-hibernation-2026-10-06`;
> the relaunch runbook and the removed files live in
> `headerpath-app/docs/website-relaunch/`; the pause plan is `headerpath-app/docs/hibernation-plan.md`.

> **🔴 HELP-CENTER RULE — read [`HELP-CENTER-GUIDE.md`](HELP-CENTER-GUIDE.md) before
> touching any article in `src/content/docs/en/`.**
>
> Articles are conversion and customer-education assets, not technical documentation.
> **NEVER mention `superadmin`** or any synonym anywhere customer-facing — roles are
> admin, manager, learner. **NEVER write articles for** open project-board tasks, handoff
> docs, features in progress, internal-facing tooling, or features we have retired.
> Only what is live for customers today.

> **🔴 POSITIONING RULE — HeaderPath is the product, not a service.**
>
> HeaderPath is an **AI-native academy SaaS** — self-serve software that builds and runs
> branded learning academies. It is **not** a service/agency that designs programs for
> clients. All copy is product-voiced and self-serve ("launch your academy", "AI builds
> your courses"), never done-for-you service language ("we audit", "we design your
> curriculum"). Watch for regressions to the old service narrative on any copy edit.

> **🔴 BRAND / STRINGS RULE.**
>
> Brand name is **HeaderPath** (capitalized). Never hardcode it — pull from `brand.ts`.
> Every user-visible string comes from `src/i18n/translations.ts`, never inline in a
> component. All brand URLs/emails from `brand.ts`.

GTM-style note: this is a static marketing site, so the "things that run" are pages and
content, not workflows. The doc below is the reference map for those.

## About HeaderPath

HeaderPath is the **AI-native academy platform** — organizations launch a branded learning
academy, AI drafts the courses, and they publish on their own domain. Audiences: customer
education, partner enablement, employee onboarding/upskilling. One plan, shown unnamed on `/pricing` ("Starts at" price + contact link) — no free plan or trial; closed
to new signups while the product hibernates. The product app lives at `app.qurioos.com`; this repo
is the **marketing website** only.

## Scope

- **Marketing pages** — homepage, product, pricing, contact, and supporting static pages
- **Content** — blog, techniques, alternatives, and a self-hosted help center (`/help`)
- **Brand surface** — positioning, design system, and copy for the public site
- Out of scope: the product app (`app.qurioos.com`), the help CMS (Featurebase), billing

## Connections

| Surface | Role | Details |
|---|---|---|
| **GitHub Pages** | Hosting | `.github/workflows/deploy.yml` (`withastro/action`) builds and deploys on every push to `main`. Custom domain `headerpath.com` via `public/CNAME`. No previews. |
| **GitHub** | Source / CI | `enablehero-ou/website-headerpath` (private). `dev` → PR → `main`. No CI workflows yet (`.github/workflows` empty). |
| **app.qurioos.com** | Product app (backend) | Login/product app — stays on qurioos infra until the app migrates. Not built by this repo. |
| **help.qurioos.com** | External help (Featurebase) | The original docs destination. The site now **self-hosts** help at `/help`; footer "Help" points internal. |
| **Google Fonts** | Webfonts | Inter, Fraunces, JetBrains Mono — loaded in `BaseHead.astro`. |
| **Webflow / Loveable** | Retiring | Old site (`quriooscom.webflow.io`) was the content + asset source — all assets are now copied into `public/images/` so nothing depends on it. Loveable still serves the live `qurioos.com` until cutover. |

## Environment

**No env vars, no secrets.** Fully static site with zero external calls; the repo is public.
Local preview needs nothing beyond `node` + `pnpm`.

## Stack

- **Framework**: Astro 6, `output: 'static'`, no adapter (plain static files for GitHub Pages)
- **Styling**: Tailwind CSS v4 via `@tailwindcss/vite`, tokens in `src/styles/global.css`
- **Interactive**: React 19 islands (`client:load` / `client:visible` / `client:idle`) — mobile nav, FAQ accordion, journey steps only
- **Content**: Markdown / MDX content collections (`src/content.config.ts`)
- **Package manager**: pnpm (`~/.local/bin/pnpm`)

```bash
~/.local/bin/pnpm dev      # Dev server — localhost:4321
~/.local/bin/pnpm build    # Production build
~/.local/bin/pnpm preview  # Preview the build
```

Local preview also runs continuously at `localhost:4321` via the `com.headerpath.website`
launch agent (auto-starts, `KeepAlive`, logs `/tmp/headerpath-website-dev.log`). Use it for
visual checks instead of pushing to preview. Restart:
`launchctl kickstart -k gui/$(id -u)/com.headerpath.website`.

## Brand & Config

- **`src/config/brand.ts`** — all brand strings: name, tagline, description, domains, `appUrl`, emails, social, stats, plan pricing (`/pricing` only). Never hardcode brand data elsewhere.
- **`src/config/navigation.ts`** — header nav (last item → `/contact`), footer columns. No CTA button.
- **`src/i18n/translations.ts`** — all UI strings for en/es/fr + `useTranslations(locale)`.

## i18n

- Default locale English, no URL prefix (`/blog`). Spanish `/es/…`, French `/fr/…`.
- `useTranslations(locale)` returns that locale's object — **no deep-merge**, so render
  values directly (a missing key renders blank, never throws). es/fr are currently
  English-only with untranslated new keys; treat them as effectively inactive until
  translated (pending decision on the project board).
- `Astro.currentLocale` gives the locale in any `.astro` file.

## Content Collections

Defined in `src/content.config.ts`. Content lives in `src/content/[type]/en/` (then `es/`,
`fr/` when translated).

| Collection | Path | Renders at | Notes |
|---|---|---|---|
| `blog` | `content/blog/en/` | `/blog`, `/blog/[slug]` | 29 posts; category enum: strategy/case-studies/product-updates/tutorials/live-events |
| `techniques` | `content/techniques/en/` | `/techniques`, `/techniques/[slug]` | 17; has `bestFor`, optional `category`, `order` |
| `docs` (help) | `content/docs/en/[category]/` | `/help`, `/help/[slug]` | 36 articles, self-hosted help center |
| `alternatives` | `content/alternatives/en/` | `/alternatives/[slug]` | competitor comparisons |
| `legal` | `content/legal/en/` | `/legal/[slug]` | privacy, services-agreement, end-user-policy. Optional `version` renders next to "Last updated". Counterparty is **Header Ventures Ltd** (`brand.legalEntity`); all contact routes to `team@headerpath.com` |

Glob loaders accept `**/*.{md,mdx}` for blog/techniques; `docs` is `en/**/*.md`.

## Design System

Tokens + utilities in `src/styles/global.css` (Tailwind v4 `@theme`). **Light, warm
editorial palette** (getmodern.ai-inspired). Components use semantic classes only — never
raw colors — so the theme is reskinnable from one place.

```
--color-bg-primary    #ffffff      --color-text-primary    #1f2a39  (brand ink)
--color-bg-secondary  #f5f8f9      --color-text-secondary  #55606f
--color-bg-tertiary   #e9eff1      --color-text-muted      #8792a1
--color-bg-card       #ffffff      --color-accent          #5ce1e6  (brand cyan)
--color-border        #dfe6ea      --color-accent-hover    #3ccdd4
--color-border-subtle #eef3f5      --color-accent-fg       #1f2a39  (text ON cyan)

--color-action        #1f2a39      --color-action-hover    #2c3a4d
--color-action-fg     #ffffff
```

**Brand colors are cyan `#5ce1e6` + ink `#1f2a39`, and the two have different jobs:**

- **`accent` (cyan) — decoration, and only at small scale or low opacity.** Arrows,
  borders, rules, dots, icon tiles, gradient washes, and faint tints (`accent/5`–`accent/10`).
  Cyan is light: it is never a text color on white. **Never fill a large element with solid
  cyan** — a full-strength `bg-accent` block reads washed-out and cheapens the brand. If an
  element is bigger than an icon or a hairline, it uses ink.
- **`action` (ink) — interaction and any solid fill.** Every button, CTA, focus ring, and
  selected/active state. Solid fills are ink with white type (`--color-action-fg`).

Never use `bg-accent` for a button or for any solid block. `bg-accent/5`–`/10` tints are fine.

- **Fonts**: `--font-sans` Inter (UI/body) · `--font-serif` Fraunces (display + h1/h2) · `--font-mono` JetBrains Mono (eyebrows/labels). Loaded in `BaseHead.astro`.
- **Shadows**: `--shadow-hairline` / `--shadow-soft` / `--shadow-lift` (also `shadow-soft` utility).
- **Component utilities**: `.eyebrow` (+`.eyebrow--accent`) mono uppercase label · `.surface` (+`.surface-hover`) soft card (hairline ring + layered shadow) · `.grain` faint film-grain overlay (parent must be `relative`) · `.atmosphere` soft hero gradient wash.
- Headings `h1,h2` are serif via base rule; eyebrows/UI stay sans/mono.
- **Logo**: all marks live in `public/images/brand/`; paths come from `brand.logo`, never hardcoded.
  - `headerpath-wordmark-light.png` (500×150) — full lockup, icon + "HeaderPath". **Header + footer use this.**
  - `headerpath-wordmark-dark.png` (500×150) — same lockup for dark surfaces: transparent background, white type. **The default on-dark asset.**
  - `headerpath-wordmark-dark-solid.png` (500×150) — dark variant with its own ink background baked in (not transparent). Only for self-contained tiles — email, third-party embeds.
  - `headerpath-icon-light.png` (256×256) — cyan disc + ink H; favicons and square contexts.
  - `headerpath-icon-dark.png` (256×256) — ink disc + cyan H; square contexts on dark.
- **Favicon**: `public/favicon.ico` (32px) + the 256px PNG + `apple-touch-icon.png`, all derived from the light mark.
- **OG image**: `public/images/og/default.png` (1200×630), the default for every page. Regenerate from `notes/og-image-template.html` — substitute `LOGO_SRC` with a `file://` path to the light wordmark, then headless-Chrome screenshot at 1200×630. Needs network for the Google Fonts.

## Layouts

- `PageLayout.astro` — standard pages (header + main + footer)
- `BaseLayout.astro` — raw HTML shell (custom layouts)
- `ContentLayout.astro` — prose article (techniques, help) with a back-link
- `BlogLayout.astro` — blog post (cover, author, date, reading time)

## Components

`src/components/ui/`: `Container` (max-w-7xl wrapper) · `Button` (primary/secondary/ghost,
pill) · `Card` (uses `.surface`) · `Badge` (accent pill). Sections in
`src/components/sections/`; layout chrome in `src/components/layout/` (Header, Footer,
BaseHead, MobileNav, FAQAccordion).

## Help / Docs Article Conventions

**File naming:** kebab-case — `user-management.md`. Lives in `src/content/docs/en/<category>/`.

**Required frontmatter:** `title`, `description` (one sentence). Optional: `category`, `order`.

**Live categories** (from the imported help center): `accounts`, `create-content`,
`customize-your-headerpath-account`, `hosting-domains`, `localization`, `certifications`,
`progress-tracking`, `reports`, `emails`, `integrations-api`, `security`. Labels/order are
set in `src/pages/help/index.astro`. (The archived hand-written set used a different
taxonomy — see Content Collections.)

## Docs Article Writing Guide

### Tone & Voice
- **Professional-casual** — confident and direct. No jargon, no corporate stiffness, no enthusiasm filler ("and more!").
- **Second person** for the reader ("you/your"). "HeaderPath" (third person) for the product. "We/our" sparingly for company voice only.
- **Active voice** dominant (~80%). Use passive only for technical descriptions.

### Length & Structure
- **Target: 200–300 words.** Go longer only when the feature genuinely demands it.
- **Opening paragraph:** 1–3 sentences of context. Never jump straight into steps or a list.
- **Headings:** `###` (H3) as the primary section heading. `##` (H2) only in longer multi-section articles. No H1 in the body.

### Formatting
- **Bold** for UI element names, key terms, and emphasis.
- **Ordered lists** only for step-by-step instructions. **Unordered** for features/benefits/tips.
- `Code formatting` for technical values, URLs, file formats, config parameters.
- **No screenshots** unless a visual is genuinely irreplaceable.

### Copywriting Patterns
- **"Need to know"** — bold-labeled callout at the end for caveats/edge cases. Format: bold heading + bullet list.
- **Problem → solution** — open feature articles by naming the user's need.
- **Feature-benefit pairing** — never state a feature alone; pair with its use case.
- End with a "Need to know" block, a "Note:" block, or a link to a related article. No sign-off, no support CTA.

### Terminology

| Use | Avoid |
|-----|-------|
| Content page / content pages | Course, courses, Path, Module |
| Page | Course |
| Step | Lesson, Block |
| Section step | Title step |
| Account | Academy, workspace, organization, tenant |
| User / users | Learner, learners, student, member |
| Admin | Administrator |
| Certification | Certificate (for the feature/setting) |

- Product nouns capitalized: **Step**, **Page**, **Certification**, **Level**, **Subject**
- Support contact: **support@headerpath.com**
- External links: "Name ↗" with arrow symbol

## Naming Conventions

- **Routes**: lowercase, content slugs match the content filename (locale prefix stripped for the URL).
- **Content files**: kebab-case `.md` / `.mdx` under `src/content/<type>/<locale>/`.
- **Components**: PascalCase `.astro` (or `.tsx` for React islands).
- **Imported brand assets**: `public/images/webflow/` (kebab-case; `int-*` for integration logos, person-name for headshots).
- **Changelog entries**: `src/content/changelog/YYYY-wNN.md` — written by the `/changelog` skill in `headerpath-app` at the end of each release cycle, committed on `dev` here; `/ship` + `/main` publish it at `/changelog`.

## Repo Structure

```
website-headerpath/
├── CLAUDE.md                 # This file — infrastructure + system context
├── README.md
├── astro.config.mjs          # static output, i18n, redirects
├── .claude/commands/         # repo-specific skills (ship.md)
├── public/
│   └── images/               # static assets (brand/ = logo marks, webflow/ = imported assets)
├── src/
│   ├── pages/                # routes (.astro) + sitemap.xml.ts, robots.txt.ts
│   ├── content/              # content collections (blog, techniques, docs, …)
│   ├── content.config.ts     # collection schemas
│   ├── components/           # ui/ · sections/ · layout/
│   ├── layouts/              # Page/Base/Content/Blog layouts
│   ├── config/               # brand.ts, navigation.ts
│   ├── i18n/                 # translations.ts
│   └── styles/global.css     # design tokens + utilities
├── notes/                    # reference docs (NOT pages) — site-structure, design refs
```

**Rule**: anything that **renders** (a page, a content file, a component) lives under `src/`
or `public/`. Reference material — plans, design refs, structure maps — lives in `notes/`.
Keep the repo root to config + `CLAUDE.md` + `README.md`.

## Task tracking

- All tasks live on the GitHub Project **[headerpath-website tasks](https://github.com/orgs/enablehero-ou/projects/4)** (org `enablehero-ou`, project #4). Create and update every task there — nowhere else.
- New task = issue in `enablehero-ou/website-headerpath` + `gh project item-add 4 --owner enablehero-ou --url <issue-url>`. Issue rules (assignee, due date, labels) are in the parent CLAUDE.md.
- Keep Status current: Backlog → To Do → In progress → QA → Done.

## Git Workflow

- Work on `dev`. `/ship` → commit + push + PR to `main`. `/main` → squash-merge to `main` (GitHub Pages deploy), then hard-reset `dev` to `main`.
- **Never push directly to `main`.** Never merge to `main` without an explicit `/main`.
- **Every `/ship`**: move the tasks this branch ships to QA on the project board and open new tasks for follow-ups it surfaced (the `/ship` skill does this).

## File Conventions

- **Pages** (`src/pages/*.astro`): thin renderers — fetch from collections/config, render. Dynamic `[slug].astro` use `getStaticPaths` + `render()`.
- **Content** (`src/content/<type>/<locale>/*.md[x]`): frontmatter validated by `content.config.ts` schemas. The markdown body is the article.
- **Config** (`brand.ts`, `navigation.ts`, `translations.ts`): the single source for brand data, nav, and copy. Edit here, not in components.
- **Styles** (`global.css`): the only place colors/fonts/shadows are defined. Components reference tokens/utilities.
- **Images**: processed/hero images via Astro `<Image>` from `astro:assets`; bulk/imported assets live in `public/images/` and are referenced by path.
- **Reference docs** (`notes/*.md`): plans, structure maps, design references. If a doc describes work that then ships, delete it — git history is the record.

## Reference docs

- [`notes/site-structure.md`](notes/site-structure.md) — full URL map + page-structure mockups (blog/help/technique templates, redirects)

## Key Rules

- Zero JS on static content pages — React islands only where interactivity is needed.
- All user-visible strings via `translations.ts`; all brand data via `brand.ts`.
- Product positioning only — never service/agency language (see Positioning rule).
- Tasks live on the project board only — never in a markdown backlog.
