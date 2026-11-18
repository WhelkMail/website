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

## Google Tag Manager and consent

The GTM container ID is injected at build time from an environment variable. No
ID is committed to the repository.

| Variable | Required | Description |
| --- | --- | --- |
| `GTM_ID` | no | GTM container ID, e.g. `GTM-ABC123` |
| `HUGO_GTM_ID` | no | Fallback name, useful if the Hugo `getenv` allowlist is restricted |

The ID is validated against `^GTM-[A-Z0-9]+$`; invalid or empty values disable
the snippet entirely.

GTM is **not** loaded on page load. `layouts/partials/consent.html` shows a
cookie banner and loads GTM only after the visitor accepts analytics. Before
consent it pushes Google Consent Mode v2 defaults with `analytics_storage`,
`ad_storage`, `ad_user_data` and `ad_personalization` set to `denied`. Rejecting
analytics keeps GTM unloaded and clears existing Google Analytics cookies for
the site. The choice is stored in local storage for 6 months and can be changed
through “Cookie settings” in the footer.

Local build:

```powershell
$env:GTM_ID = "GTM-ABC123"
hugo
```

Cloudflare Pages: add `GTM_ID` under **Settings > Environment variables** for the
production (and preview, if desired) environment, then redeploy.

Recommended configuration in the Google tools:

- In GTM, keep Google Analytics tags limited to the analytics purpose. Consent
  Mode signals are set by the site; do not add tags that fire before consent.
- In Google Analytics 4, set data retention to the value published in the
  Privacy Policy (no more than 14 months).
- Verify Google Search Console with a DNS record or an HTML meta tag rather than
  a GTM tag. Verification does not need to run at page load, and a GTM-based
  verification would only work after consent.

## Legal pages

The Privacy Policy lives in `content/privacy.md` and the Cookie Policy in
`content/cookies.md`. Update the controller details in `hugo.toml` before
deploying:

```toml
privacyController = "WhelkMail"
privacyEmail = "info@whelkmail.eu"
privacyCountry = "Italy"
privacyAuthority = "Garante per la protezione dei dati personali"
privacyAuthorityURL = "https://www.garanteprivacy.it/"
```

Use a real, monitored mailbox for `privacyEmail`. Review the policy whenever a
new third-party script or tag is added.

## License

MIT
