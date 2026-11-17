# WhelkMail website

Marketing site for [WhelkMail](https://github.com/WhelkMail/WhelkMail), built with [Hugo](https://gohugo.io/).

## Local development

```bash
hugo server
```

## Build

```bash
hugo
```

Output is written to `public/`.

## Google Tag Manager

The GTM container is injected at build time from an environment variable. No ID is committed to the repository.

| Variable | Required | Description |
| --- | --- | --- |
| `GTM_ID` | no | GTM container ID, e.g. `GTM-ABC123` |
| `HUGO_GTM_ID` | no | Fallback name, useful if the Hugo `getenv` allowlist is restricted |

The ID is validated against `^GTM-[A-Z0-9]+$`; invalid or empty values disable the snippet entirely.

Local build:

```powershell
$env:GTM_ID = "GTM-ABC123"
hugo
```

Cloudflare Pages: add `GTM_ID` under **Settings > Environment variables** for the production (and preview, if desired) environment, then redeploy.

## License

MIT
