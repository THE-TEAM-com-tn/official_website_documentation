# TheTeam API documentation

Mintlify documentation for the public HTTP API of the TheTeam website backend
(`https://admin.theteam.com.tn/api`) — trainings, blog, Knowledge Corner articles, site content,
public forms and staff-account authentication.

The API itself lives in [official_website_backend](https://github.com/THE-TEAM-com-tn/official_website_backend);
the site that consumes it in [official_website_frontend](https://github.com/THE-TEAM-com-tn/official_website_frontend).

## Preview locally

```bash
npm i -g mint
mint dev          # http://localhost:3000
```

## Check before pushing

```bash
mint validate     # strict build
mint broken-links
```

## Structure

| Path | Content |
| :-- | :-- |
| `docs.json` | Theme, colours, logo, navigation, API playground settings |
| `index.mdx`, `quickstart.mdx` | Introduction (with the use-case diagram) and first requests |
| `concepts/` | Authentication, localization, responses & errors, pagination, rate limits, publishing & caching |
| `api-reference/` | One page per endpoint, grouped like the navigation |

Writing rules and the backend-sync checklist are in [`CLAUDE.md`](CLAUDE.md).

## Publishing

Install the Mintlify GitHub app from the [dashboard](https://dashboard.mintlify.com/settings/organization/github-app)
and connect this repository: every push to `main` deploys the site.
