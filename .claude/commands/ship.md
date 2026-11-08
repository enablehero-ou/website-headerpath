Delete any `PLAN-*.md` files in the project root (use `rm -f PLAN-*.md` — silent if none exist).

## Tasks — project board, never a markdown file

All tasks live on the GitHub Project **headerpath-website tasks** (`enablehero-ou`, project #4 — https://github.com/orgs/enablehero-ou/projects/4). Update it before committing:

- **Shipped by this branch** → find the matching tasks (`gh project item-list 4 --owner enablehero-ou --format json`) and set their Status to **QA**. `/main` moves them to Done.
- **New follow-ups surfaced by the work** → one issue each in `enablehero-ou/website-headerpath` (assignee `qurioos`, `**Due: YYYY-MM-DD**` at the top of the body, one `type: …` and one `priority: …` label), then `gh project item-add 4 --owner enablehero-ou --url <issue-url>` and set Status to **To Do**.
- Set Status with `gh project item-edit --id <item-id> --project-id <project-id> --field-id <Status field id> --single-select-option-id <option id>`; read the ids with `gh project field-list 4 --owner enablehero-ou --format json`.
- Reference each shipped issue in the PR body as `Refs #<n>` so the task links to the PR.

Run `git add -A`, then inspect all changes on this branch (compare to main) to infer a concise summary of everything built. Commit with the message `feat: <inferred summary>`.

Before pushing, check if any of the shipped changes affect areas documented in CLAUDE.md (new routes, changed config/tokens, new components, new i18n strings, new content collections, connections, etc.). If yes, update only the affected sections of CLAUDE.md to reflect the current reality. CLAUDE.md is infrastructure/context only — no logs or dated markers. Then `git add CLAUDE.md` and amend the commit with `git commit --amend --no-edit`.

Then run `git push origin HEAD`. Then run `gh pr create --base main --title "<commit subject>" --body "<2–4 bullet summary + one \`Refs #<n>\` line per shipped task>"` (or find the existing PR with `gh pr view` and add any missing `Refs` lines with `gh pr edit --body`).

## Report — exact URLs to check, nothing else

The report must be a 1-click checklist: every line a **full, clickable URL** on the local preview. Never report a bare path or a slug.

**Base host:** `http://localhost:4321` (the always-on local dev server — GitHub Pages has no per-branch previews; production deploys only from `main`).

**Derive the URLs from the diff** (`git diff main...HEAD --name-only`), one per changed surface:
- `src/pages/<path>.astro` → `http://localhost:4321/<path>` (`index` → `/`).
- `src/pages/es/**`, `src/pages/fr/**` → the same with the locale prefix.
- Content collections (`src/content/<collection>/<slug>.md`, locale prefix stripped) → the rendered page URL per `notes/site-structure.md` (e.g. help articles → `/help/<slug>`, blog → `/blog/<slug>`); for a translated file give the locale URL.
- Shared components / layouts / `brand.ts` / `navigation.ts` / global CSS → one representative page per affected template (home, one help article, one blog post, `/contact`) — not every page.
- Redirects in `astro.config.mjs` → the **source** URL, with the expected destination after the dash.
- i18n string changes → one URL per affected locale.

**Output format** (bullets only, no prose):
- PR: `<url>`
- Check:
  - `<full url>` — what to look for (≤ 8 words)
  - `<full url>` — …

## 👉 NEXT STEP — always the very last thing you print

Print this block last, after everything else, every single time. Plain words, no jargon.
One command only. If the user must choose, list each case with its own command.

```
👉 NEXT STEP
Run:    /<command>
Where:  <folder, e.g. ~/Documents/CC/headerpath-app — "this folder" if unchanged>
Why:    <one short sentence>
```

Cases:
- PR created → `Run: /main` · Where: this folder · Why: check the URLs above first, then publish.
