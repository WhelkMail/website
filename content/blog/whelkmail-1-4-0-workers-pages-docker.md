---
title: "WhelkMail 1.4.0: Workers by default, Pages for existing installs, Docker for everything else"
description: "WhelkMail 1.4.0 ships one codebase with three deployment targets: Cloudflare Workers, existing Cloudflare Pages projects and a self-contained Docker image with SQLite and S3 storage."
date: 2026-11-23T10:00:00+01:00
lastmod: 2026-11-23T10:00:00+01:00
sitemap:
  priority: 0.7
  changefreq: monthly
author: "WhelkMail Team"
keywords: ["whelkmail 1.4.0", "self hosted webmail docker", "cloudflare workers webmail", "cloudflare pages", "imap smtp webmail"]
---

WhelkMail started as a Cloudflare Pages project. That was a good fit for a
webmail client: static React assets, a small API and an edge runtime that can
open outbound IMAP and SMTP connections. It also had a clear downside: if you
did not want to run on Cloudflare, there was no path.

**1.4.0 changes that.** The application now has one runtime-agnostic API core and
three deployment targets:

| Target | Database | Object storage | Mail transport | Background work |
| --- | --- | --- | --- | --- |
| Cloudflare Workers (default) | D1 | R2 | `cloudflare:sockets` | Worker cron trigger |
| Cloudflare Pages (existing) | D1 | R2 | `cloudflare:sockets` | `worker/` cron |
| Docker / any VPS | SQLite | Any S3-compatible bucket | `node:net` / `node:tls` | Built-in scheduler |

The UI, API handlers, security middleware, mail flows and database schema are
the same everywhere. Only small adapters differ.

## Deploy to Cloudflare in one click

Workers is now the default target. The README has a **Deploy to Cloudflare**
button: it clones the repository into your GitHub or GitLab account, provisions
D1 and R2, configures Workers Builds and deploys the Worker. Secrets such as
`APP_PASSWORD` and `TOKEN_ENC_KEY` are entered during setup or in the dashboard.

Existing Pages installations keep working without a data migration. Keep the
same D1 database, R2 bucket and secrets; deploy the new release. If you have an
old local `wrangler.toml`, remove it: the repository now ships a shared
`wrangler.jsonc`, and Pages does not support custom configuration paths.

## Docker with SQLite and S3

The Docker target is a single Node 24 container:

```bash
cd deploy/docker
cp .env.example .env
# set APP_PASSWORD, TOKEN_ENC_KEY and PUBLIC_ORIGIN
docker compose up -d --build
```

It uses the built-in `node:sqlite` module, so there is no native dependency and
no `node_modules` in the runtime image. The database lives in the `/data`
volume and migrations run on startup. S3-compatible storage is optional: point
the `S3_*` variables at MinIO, AWS S3, Backblaze B2 or Cloudflare R2's S3 API.
The container also runs the notification/sync scheduler itself.

Put Caddy, Traefik or nginx in front for TLS and set `PUBLIC_ORIGIN` to the
public URL, so OAuth callbacks and secure cookies are correct.

## Upgrading

1.4.0 does not add a database migration. Upgrade commands by target:

```bash
# Workers
git fetch --tags && git checkout 1.4.0
npm ci && npm run deploy

# Pages
git fetch --tags && git checkout 1.4.0
npm ci && npm run db:migrate:remote && npm run deploy:pages

# Docker
git pull
docker compose up -d --build
```

Read the [deployment guide](https://docs.whelkmail.eu/deployment/) for the full
matrix: bindings, secrets, OAuth, R2/S3, cron, backups and troubleshooting.
