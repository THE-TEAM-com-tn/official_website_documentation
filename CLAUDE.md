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
(share pages, sitemaps, health). The admin panel is **not** documented as an API (session auth,
HTML/Inertia responses); it has a separate **French user guide** for staff instead — tab
**Guide utilisateur** (`guide-utilisateur/`), written for non-technical admins and content managers.

It lives next to the two app repos inside the `Official_website` coordination folder:

| Folder | What it is |
| :-- | :-- |
| `../official_website_backend/` | Laravel 12 backend on PHP 8.4 (8.4.27+ / 8.5.12+, php2shell-patched) — **the source of truth for everything documented here** |
| `../official_website_frontend/` | React site that consumes the API (the navbar logo comes from its `public/`) |

The coordination repo's old `DOCUMENTATION/` folder (internal feature guides, admin panel) was
removed on 2026-11-14; its php2shell page moved here as `security/php2shell.mdx`. Any other old page
can still be read for background with `git -C .. show 550c1c9:DOCUMENTATION/<name>.mdx` — verify
against the code before copying anything.

## 2. Layout

```text
docs.json                            # theme, colours, logo, navigation (tabs), API playground settings
index.mdx                            # Introduction: first request, use-case diagram (Mermaid), running locally
concepts/*.mdx                       # authentication, localization, responses & errors, pagination, rate limits, publishing & caching
api-reference/<group>/overview.mdx   # one short overview per group: that group's endpoint table + notes
api-reference/<group>/*.mdx          # one page per endpoint (manual MDX API pages)
security/php2shell.mdx               # php2shell notice: patched PHP releases, backend guards, upgrade steps
security/client-ip-and-rate-limits.mdx  # plain-language: why per-IP limits need the real client IP, how to check
security/cloudflare-and-proxies.mdx     # Cloudflare/proxy setup (trustProxies with Cloudflare ranges only), hiding the PHP version
troubleshooting/forms-cors-preflight.mdx  # incident: nginx 405 on the OPTIONS preflight breaks every public form; the nginx fix
images/troubleshooting/*.png         # screenshots for troubleshooting pages (personal details blurred)
guide-utilisateur/*.mdx              # FRENCH user guide: Administrateur tab (full panel) + Côté visiteur pages (site-public, formulaire-*)
guide-utilisateur/gestionnaire/*.mdx # Gestionnaire de contenu tab: self-contained guide written for that role, CM screenshots
en/guide-utilisateur/**              # ENGLISH twins of every guide page (same slugs); images in images/guide/en/
images/guide/<section>/*.webp        # its screenshots: scratch DB with fictional data (example.com), FR UI, 2x → WebP
                                     # images/guide/site-public/ = the PUBLIC site's forms (frontend on :4000 with BASE_URL
                                     # temporarily on the local backend; prod host blocked in the browser; never committed)
style.css                            # loaded on every page by Mintlify; hides the reader "Log out" link, sizes the navbar logo (22px / 20px mobile)
logo/light.png, logo/dark.png               # copied from official_website_frontend/public/
favicon.svg                          # docs-only icon (green page + white T) — never the website's favicon
```

Navigation = **two languages × two products** (`navigation.languages`; the language switcher and a
per-language navbar come from Mintlify):

- **fr** (default) and **en**, each with the products **API** and the user guide.
- **API** — the same English pages in both languages (tabs Guides · Content · Forms · Authentication · System).
- **Guide utilisateur** (fr) / **User guide** (en) — tabs Administrateur / Administrator (full guide) ·
  Gestionnaire de contenu / Content manager (`gestionnaire/`) · Côté visiteur / Visitor side (public forms).
- **English twin of every guide page:** `guide-utilisateur/x.mdx` ⇄ `en/guide-utilisateur/x.mdx` (same slug —
  the switcher maps pages by path). English screenshots live in `images/guide/en/` with the same file names,
  taken from the English admin panel and the `/en/` public site. **Changing a guide page ⇒ change its twin.**
  A change in the panel usually touches both role tabs too: keep them consistent.

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

## 3a. The French user guide (`guide-utilisateur/`)

- Audience: staff who are **not** IT people. French only, plain words, one task per section,
  `<Steps>` for procedures, a screenshot in `<Frame caption>` for each screen. No code, routes or `.env`.
- Say which role may do what at the top of each page (`<Info>` / `<Warning>` for admin-only), from
  `Role::DEFAULTS` in the backend — the panel hides what a role cannot do.
- UI labels are quoted **exactly** as the French admin panel shows them (`resources/js/i18n/fr/*`,
  `lang/fr.json`). When the panel changes, re-capture the affected screenshots.
- Screenshots come from the backend on a scratch database with fictional people (`example.com`
  emails) — never production data, never seeded credentials. Not blurred, since nothing is real.
- Avoid heading anchors with accents in links (`#les-témoignages`); link to the page instead.

## 3b. Visibility (the site is not meant to be found)

- `docs.json` → `seo.metatags.robots` = `noindex, nofollow, …` on every page. Keep it.
- `robots.txt` disallows every crawler; `llms.txt` / `llms-full.txt` are deliberate one-line overrides
  of Mintlify's generated files (they must start with an H1). Don't delete them or add page links.
- Footer = three link columns per language (`footer` in each `navigation.languages[]` entry, plus a
  top-level French fallback like the navbar): Documentation · Ressources/Resources · TheTeam (public
  site pages). Keep FR and EN in sync. Still no social icons, no links to the private app
  repositories, and no navbar or footer link to the admin login.
- The site is **Private** (Mintlify dashboard → Access → Authenticated: readers sign in with a
  Mintlify account of the organization). Mintlify then adds a reader **"Log out"** link to the navbar
  and mobile sidebar; `style.css` hides it via the documented `.logout-link` hook. Keep that file.
- Rate limits: when the backend adds or changes a `throttle`, update `concepts/rate-limits.mdx` and
  the "Rate limit:" line on that endpoint's page.

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
