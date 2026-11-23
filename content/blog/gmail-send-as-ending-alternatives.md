---
title: "Gmail Ends External “Send As” in 2027: What to Use Instead"
description: "Gmail stops sending from third-party addresses, Gmailify and POP in January 2027. Compare the best alternatives, including self-hosted WhelkMail."
date: 2026-11-21
lastmod: 2026-11-21
sitemap:
  priority: 0.7
  changefreq: monthly
author: "WhelkMail Team"
keywords: ["gmail send as ending", "gmail external alias", "gmail alternative custom domain", "self-hosted webmail", "send mail as third-party"]
---

If you use Gmail as a hub for other mailboxes, one of the most convenient
features is going away. Starting in **January 2027**, Gmail will no longer let
you send email from third-party addresses through “Send as”, and it will also
remove Gmailify and POP fetching on the web. In practice, that means a Gmail
account can no longer act as a single inbox for your Yahoo, Outlook, work or
custom-domain addresses.

The short answer: you need a real multi-account mail client, not a forwarding
trick. A self-hosted webmail client such as [WhelkMail](https://whelkmail.eu/) keeps
every IMAP/SMTP account in one interface, sends from the correct address and
leaves the mail where it already lives. This guide explains exactly what Google
is changing, who is affected, and the realistic options before the deadline.

## What exactly changes in Gmail?

Google published the change in its help centre: [changes to third-party email
account support in
Gmail](https://support.google.com/mail/answer/17101213). Three features are
affected.

### 1. “Send as” for third-party addresses ends

The “Send as” feature lets you compose a message in Gmail and choose a sender
address you own on another service, such as `you@yourdomain.com` hosted by a
third-party provider. From January 2027, Gmail will no longer send from those
non-Google addresses on the web or in the mobile apps.

Crucially, **Gmail-to-Gmail aliases and Google Workspace “Send as” are not
affected**. The change targets third-party, non-Google accounts.

### 2. Gmailify and POP fetching on the web end

Gmailify applies Gmail features such as spam filtering and inbox organisation
to a third-party account. POP fetching pulls mail from another provider into
Gmail. Both are being removed on the web. As previously announced, you can
already no longer add or sync a third-party account in Gmail on the web.

### 3. What does not change

- The Gmail mobile app can still access third-party accounts.
- Messages already imported into Gmail remain in your mailbox.
- Forwarding from another provider to Gmail still works.
- Gmail accounts can still be read from third-party clients over IMAP, POP or the
  Gmail API.

## Who is affected?

You are affected if any of these sound familiar:

- You run a **custom domain** on a host that is not Google Workspace, and you
  added it to Gmail with “Send as”.
- You manage a **small business inbox** and rely on Gmail to reply from
  `info@`, `sales@` or `support@` addresses.
- You consolidated **Yahoo, Outlook, iCloud or Fastmail** accounts into Gmail.
- You use Gmail as a **universal inbox** and expected it to keep fetching mail
  from other providers.

If you only use an `@gmail.com` address, or you pay for Google Workspace and all
your addresses are Google-hosted, this change does not remove your aliases.

## The realistic options

There is no perfect drop-in replacement for “Send as”, because the feature sat
in a grey area between a mail client and a mail provider. The right choice
depends on whether you want to keep Gmail as your primary inbox or move the
whole workflow somewhere else.

### Option 1: Use each provider’s own webmail

The simplest option is to log in to each provider separately. It works, it is
free, and it avoids third-party access to your credentials. The downside is
obvious: five accounts mean five tabs, five interfaces and five sets of
signatures, filters and contacts.

### Option 2: Use a desktop mail client

Thunderbird, Apple Mail, Outlook and similar desktop clients can connect to
third-party accounts over IMAP and SMTP. They are mature and local-first. The
trade-off is that your inbox is tied to one computer, sync behaviour varies, and
you still need to configure each account, signature and outgoing server by hand.

### Option 3: Keep using the Gmail mobile app

Google explicitly keeps third-party account support in the Gmail app for
Android, iPhone and iPad. If you only read mail on your phone, this may be
enough. On the web, however, you lose the consolidated view and the ability to
send from those addresses.

### Option 4: Move to Google Workspace

If your main goal is to keep using Gmail with a custom domain, Google Workspace
is the official path. You get Google-hosted aliases, “Send as” and Gmail’s
interface. You also get a per-user subscription and your mail stays in Google’s
infrastructure. For a personal domain with a handful of addresses, the cost can
be hard to justify.

### Option 5: Use a self-hosted webmail client

A webmail client connects to the mailboxes you already own over IMAP and SMTP.
You keep your existing providers, your existing storage and your existing
addresses, but you get one interface for all of them — including the ability to
choose the sender address per account.

[WhelkMail](https://whelkmail.eu/) is built for exactly this scenario. It is a
self-hosted webmail client for IMAP and SMTP accounts, deployable on Cloudflare Workers, Pages or Docker. You
connect Gmail, Outlook, iCloud, Fastmail, Yahoo or a custom domain, and each
account keeps its own sender identity, signature and outgoing server. Labels and
filters are shared across accounts, so the consolidated workflow Gmail is
removing does not disappear.

Unlike a browser extension or a “Gmail add-on” that asks for your Google
password, WhelkMail runs on your own infrastructure. Credentials are encrypted at
rest with AES-256-GCM in your own D1 database, HTML email is rendered in a
sandboxed frame with scripts disabled, and remote images stay blocked until you
allow them. The project is MIT licensed and has no telemetry.

### Option 6: Use an alias or forwarding service

Services such as SimpleLogin, addy.io or Firefox Relay generate privacy aliases
that forward to your real inbox. They are excellent for signups and reducing
spam, but they are not a full replacement for “Send as”: they focus on receiving
and replying through aliases, not on managing arbitrary IMAP/SMTP mailboxes.

Plain forwarding is another partial workaround. You can forward mail from your
custom domain to Gmail and keep reading it there. But replying from the original
address is exactly what is being removed, so forwarding alone does not solve the
problem.

## Comparison at a glance

| Option | One inbox | Send from original address | Own your data | Best for |
| --- | --- | --- | --- | --- |
| Provider webmail | No | Yes | Provider-dependent | Occasional use |
| Desktop client | Yes, per device | Yes | Yes, locally | Power users on one computer |
| Gmail mobile app | Partial | Yes, on mobile | No | Phone-only reading |
| Google Workspace | Yes | Yes, Google-hosted | No | Businesses already in Google |
| WhelkMail | Yes | Yes, per account | Yes | Self-hosters and multi-account users |
| Alias service | Partial | Limited | No | Privacy aliases and signups |

## A migration checklist for January 2027

1. **Inventory your accounts.** List every address you send from in Gmail and
   note where it is hosted and whether it uses IMAP or SMTP.
2. **Check your provider’s SMTP access.** Some providers require an app password
   or OAuth instead of your normal password. Confirm the outgoing server, port
   and authentication method now.
3. **Decide where mail should live.** Keeping mail at your provider is simpler
   and avoids a risky migration. A client such as WhelkMail reads it in place.
4. **Export what you need.** Save contacts, filters and any labels that only
   exist in Gmail before you change your workflow.
5. **Verify DNS before you send.** Make sure SPF, DKIM and DMARC are configured
   for every domain you send from, so replies do not land in spam.
6. **Test with a small account first.** Send and receive a few messages, check
   that the correct signature and sender appear, then migrate the rest.

## Frequently asked questions

### Will Gmail delete mail already imported from other accounts?

No. Google says imported messages remain in your mailbox. The change affects
future sending and syncing, not existing messages.

### Can I still forward mail from my domain to Gmail?

Yes. Forwarding is independent of “Send as” and continues to work. You just
cannot reply from the forwarded address inside Gmail after January 2027.

### Does this affect Gmail accounts read in Thunderbird or Apple Mail?

No. Access to Gmail from third-party clients over IMAP, POP and the Gmail API is
unchanged. The restriction applies to Gmail sending as a non-Google address.

### Is a self-hosted webmail client safe?

It can be, if you run it on infrastructure you control and follow the project’s
security guidance. WhelkMail encrypts credentials, uses server-side sessions,
supports optional TOTP two-factor authentication and ships strict security
headers. You can inspect the source because it is open source.

## The bottom line

Gmail is stepping back from being a universal inbox for other people’s mail.
That is a good moment to stop relying on a provider feature you do not control.
A self-hosted webmail client keeps the convenience of one inbox while leaving
your mailboxes, domains and credentials where they belong: with you.

Ready to see the workflow? [Try the live demo](https://demo.whelkmail.eu) or
[deploy WhelkMail on your own Cloudflare account](https://docs.whelkmail.eu/deployment/).
