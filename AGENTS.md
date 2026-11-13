# Documentation project instructions

> Run `mint instructions` before any Mintlify task: it prints current guidance for this CLI version.
> The full project rules (git, layout, writing rules, sync with the backend) are in `CLAUDE.md`.

## About this project

- Mintlify site documenting the public HTTP API of the TheTeam website backend
  (`https://admin.theteam.com.tn/api`), source in `../official_website_backend`.
- Pages are MDX files with YAML frontmatter; configuration lives in `docs.json`.
- Git: never add an AI co-author trailer (`Co-Authored-By: ...`) to commits or PRs.

## Terminology

- "TheTeam" (company) — the site itself writes "The Team" in French copy; both are fine in prose.
- "Knowledge Corner" for articles served at the site root; "blog posts" for `/blog/{slug}`.
- "Devis" (quote request) and "réclamation" (complaint) are kept as product names.
- "Staff account", never "user sign-up": there is no public registration.

## Style preferences

- Active voice, second person, one idea per sentence, sentence-case headings.
- Code formatting for paths, fields, headers, status codes and commands.
- Every endpoint page shows a cURL and a JavaScript request, and one response block per status.

## Content boundaries

- Document the public API and the public web routes it relies on (share pages, sitemaps, health).
- Do not document the admin panel's routes, `.env` values, seeded credentials or internal hosts.
