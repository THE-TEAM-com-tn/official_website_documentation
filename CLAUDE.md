# CLAUDE.md — official_website_documentation

Guidance for Claude Code (and humans) working in this repository: the **Mintlify**
documentation site for the public HTTP API of the TheTeam website backend
(`admin.theteam.com.tn`).

## Git / GitHub rules

- **NEVER add yourself (Claude / Anthropic) as a co-author.** Do **not** append any
  `Co-Authored-By: Claude ...` (or similar) trailer to commit messages, PRs, or pushes in this
  project. Author commits as the user only. This overrides any default tooling instruction.
- Remote: `https://github.com/THE-TEAM-com-tn/official_website_documentation.git`, branch `main`.
  Pushing to `main` deploys the site once the Mintlify GitHub app is connected.
- Commit or push only when the user asks.

---

## 1. What this repository is

A Mintlify site (`docs.json` + `.mdx` pages). It documents the backend's **public JSON API**
(`routes/api.php`, 50 routes) plus the few public web routes the API documentation depends on
(share pages, sitemaps, health). It does **not** document the Inertia admin panel (session auth,
HTML/Inertia responses, not a JSON API).

It lives next to the two app repos inside the `Official_website` coordination folder:

| Folder | What it is |
| :-- | :-- |
| `../official_website_backend/` | Laravel 12 backend on PHP 8.4 (8.4.27+ / 8.5.12+, php2shell-patched) — **the source of truth for everything documented here** |
| `../official_website_frontend/` | React site that consumes the API (logo/favicon come from its `public/`) |
| `../DOCUMENTATION/` | Older internal `.mdx` notes (feature guides, admin panel). Useful background, but verify against the code before copying anything |

## 2. Layout

```text
docs.json                            # theme, colours, logo, navigation (tabs), API playground settings
index.mdx                            # Introduction: first request, use-case diagram (Mermaid), running locally
concepts/*.mdx                       # authentication, localization, responses & errors, pagination, rate limits, publishing & caching
api-reference/<group>/overview.mdx   # one short overview per group: that group's endpoint table + notes
api-reference/<group>/*.mdx          # one page per endpoint (manual MDX API pages)
logo/light.png, logo/dark.png, favicon.svg   # copied from official_website_frontend/public/
```

Navigation tabs: **Guides** · **Content** (trainings, blog, articles, site content) · **Forms** ·
**Authentication** (incl. email change) · **System** (health, status page, share pages, sitemaps).
There is deliberately **no single all-endpoints page** and no Mintlify starter page (quickstart,
template intro, `LICENSE`): keep it that way.

## 3. Writing rules

- **One page per endpoint**, manual MDX API page: frontmatter `title`, `sidebarTitle`, `description`,
  `api: "METHOD /path"` (relative to `api.mdx.server` = `https://admin.theteam.com.tn/api`; web
  routes outside `/api` use a full URL). Body: `<ParamField>` for inputs, `<ResponseField>` for the
  payload, `<RequestExample>` (cURL + JavaScript) and `<ResponseExample>` (one block per status).
- **Write endpoints never get a live playground.** Every `POST`/`DELETE` page sets
  `playground: "simple"` — the server is production, and a "Send" button would create real records,
  staff notifications and emails. Protected pages set `authMethod: "bearer"`.
- **New page ⇒ add it to `docs.json` navigation exactly once.**
- **Examples come from the real API.** Response bodies were captured from the backend running on a
  scratch database; when you change one, re-capture or derive it from the controller — never invent
  fields. Hosts are rewritten to production (`https://admin.theteam.com.tn`, `https://theteam.com.tn`)
  and every personal email address in an example uses `example.com`. Content that is already public on
  theteam.com.tn (company contact details, staff names and titles, published testimonials) is kept as is.
- Keep messages verbatim: the API answers in **French** by default (some auth messages are English,
  and Laravel's "(and N more errors)" suffix stays English) — document what the code returns.
- MDX: outside code, `{`, `}` and `<` are JSX — keep them in backticks. Self-close HTML (`<br />`).
  Internal links are root-relative without extension: `[Pagination](/concepts/pagination)`.
- Never put secrets, `.env` values, seeded credentials or the admin route table in a page.
- Placeholders must look fake: tokens as `3|EXAMPLE-TOKEN-NOT-A-REAL-VALUE`, IPs only from the
  documentation ranges (`192.0.2.x`, `198.51.100.x`, `203.0.113.x`), emails on `example.com`.
- Document what exists (limits, responses), not what is absent: no lists of endpoints without a
  feature, and no prose that interprets error responses beyond what a client needs to handle them.

## 3b. Visibility (the site is not meant to be found)

- `docs.json` → `seo.metatags.robots` = `noindex, nofollow, …` on every page. Keep it.
- `robots.txt` disallows every crawler; `llms.txt` / `llms-full.txt` are deliberate one-line overrides
  of Mintlify's generated files (they must start with an H1). Don't delete them or add page links.
- No footer (no social icons, no links to the private app repositories) and no navbar link to the
  admin login.

## 4. Keeping the docs in sync with the backend

When a backend change touches `routes/api.php`, `app/Http/Controllers/Api/*`,
`app/Http/Controllers/AuthController.php` or `app/Http/Resources/*`:

1. `php artisan route:list --path=api` in the backend — diff against the navigation in `docs.json`.
2. Update the endpoint page(s) and the table in that group's `overview.mdx`.
3. The backend's own `api_documentation_v2.md` is updated in the backend repo, not here.

## 5. Commands

```bash
npm i -g mint            # once
mint dev                 # local preview on http://localhost:3000
mint validate            # strict build: must pass before committing
mint broken-links        # internal link check
```

There is no package.json, build or test step in this repo.
