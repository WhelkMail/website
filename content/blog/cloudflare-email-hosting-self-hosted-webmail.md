---
title: "Self-Hosted Email on Cloudflare: What Actually Works in 2026"
description: "Cloudflare Email Routing, Email Workers and a self-hosted webmail client: what Cloudflare can host, what it cannot, and the architecture that works."
date: 2026-11-22T16:30:00+01:00
lastmod: 2026-11-22T16:30:00+01:00
sitemap:
  priority: 0.7
  changefreq: monthly
author: "WhelkMail Team"
keywords: ["cloudflare email hosting", "cloudflare email routing", "self hosted email cloudflare", "cloudflare email worker", "webmail on cloudflare", "email routing custom domain"]
---

“Cloudflare email hosting” is one of those searches that returns a lot of
answers and very little clarity. Cloudflare runs DNS for millions of domains and
offers several email-adjacent products, but it does not sell a normal mailbox the
way Google Workspace, Fastmail or Zoho Mail do. Understanding what each product
actually does saves you from building an architecture that cannot work.

This guide separates the pieces: **Email Routing**, **Email Workers** and the
**Workers platform**, and explains where a self-hosted webmail client fits. The
short version: Cloudflare can route and process mail and can run the webmail
interface, but your mailbox storage and SMTP sending still live somewhere else —
or you bring a relay.

## The three Cloudflare email products

| Product | What it does | What it does not do |
| --- | --- | --- |
| Email Routing | Creates addresses on your domain and forwards incoming mail to a destination mailbox | Store mail, provide IMAP, or send outbound mail |
| Email Workers | Runs code on incoming messages to inspect, route, reject or forward them | Act as a general-purpose inbound SMTP server for your mail client |
| Workers + D1 | Runs the webmail application and stores app data at the edge | Replace an IMAP mailbox or accept inbound SMTP on port 25 |

There is no Cloudflare product that gives you a conventional IMAP mailbox with
folders, quotas and a sent-items folder. If that is what you need, you choose a
mailbox provider. Cloudflare is the network and compute layer around it.

## Email Routing: forwarding, not a mailbox

Cloudflare Email Routing lets you create addresses such as `hello@yourdomain.com`
and forward them to a destination inbox you already own. It supports catch-all
addresses, per-address rules and destination verification. It is free, it is
useful, and it is frequently confused with email hosting.

What Email Routing does well:

- **Role addresses.** Send `info@`, `support@` and `hello@` to one real inbox.
- **Domain consolidation.** Forward a secondary domain to your main mailbox.
- **Catch-all control.** Receive mail sent to addresses you never created.
- **Simple migration.** Change the destination without changing your MX records
  again.

What it does not do:

- **Store messages.** It forwards; the destination mailbox stores.
- **Provide IMAP or a webmail interface.** Your mail client connects to the
  destination, not to Cloudflare.
- **Send mail from your domain.** Replying from a forwarded address requires a
  separate sending setup, usually an SMTP relay.
- **Guarantee the original envelope.** Forwarding can affect SPF alignment and
  break some authentication assumptions unless the forwarder is configured
  carefully.

Email Routing is an excellent front door. It is not the house.

## Email Workers: programmatic handling of incoming mail

Email Workers let you run JavaScript on incoming messages. You can inspect
headers, reject spam, route by recipient, call an API, or forward a message to
another address. This is powerful for automation:

- Route `billing@` to an accounting system and `support@` to a helpdesk.
- Drop messages that fail a custom check.
- Add headers or notify a webhook.
- Build a small inbound pipeline without running a mail server.

Email Workers are not a replacement for a mailbox. They receive mail that
Cloudflare has already accepted through Email Routing, and their outbound options
are deliberately limited. For arbitrary outbound email you still need a
reputable SMTP provider or relay. That is not a Cloudflare limitation so much as
an email deliverability reality: sending from a random Worker without
authentication and reputation is a fast route to the spam folder.

## Can you self-host webmail on Cloudflare?

Yes — if “webmail” means the client, not the mailbox.

A webmail client is a web application that connects to existing IMAP and SMTP
accounts. It does not need to accept inbound SMTP on port 25, and it does not
need to store every message forever. It needs:

- outbound TCP connections to IMAP and SMTP servers;
- a place to run the application;
- a database for sessions, settings, labels, filters and cached metadata;
- secure storage for credentials.

Cloudflare Workers can run the application, D1 can store app data, and
`cloudflare:sockets` provides outbound TCP connections. That is the architecture
[WhelkMail](https://whelkmail.eu/) uses. It is a self-hosted webmail client that
runs on your own Cloudflare account and connects to mailboxes you already own.

That means:

- **No inbound mail server.** You do not accept SMTP connections, so you do not
  own IP reputation for receiving.
- **No mailbox migration.** Gmail, Outlook, iCloud, Fastmail, Yahoo and custom
  IMAP servers stay where they are.
- **No VPS to patch.** The runtime is Cloudflare Workers, not a Linux server with
  Postfix and Dovecot.
- **No second copy of your mail.** Messages are read over IMAP; recent bodies may
  be cached briefly for speed, and long-term archiving is optional.

The trade-off is that you are trusting Cloudflare as your compute and database
provider. If that is acceptable — and for many people already using Cloudflare
DNS it is — the operational footprint is much smaller than a traditional mail
server.

## What you cannot do on Cloudflare

Be realistic about the boundaries:

1. **You cannot receive arbitrary SMTP on Workers.** Cloudflare Email Routing
   accepts mail for your domain and hands it to Email Workers or forwards it. You
   are not running an MX with a general-purpose mailbox behind it.
2. **You cannot host a normal IMAP server for other clients.** D1 is an
   application database, not a mail store with IMAP semantics.
3. **You cannot ignore deliverability.** If you send outbound mail, use an
   authenticated relay with SPF, DKIM and DMARC. The client does not fix DNS.
4. **You cannot treat D1 as unlimited storage.** Database and request limits
   apply. Archive old mail at the provider or in object storage if you need
   long-term retention.
5. **You cannot run a traditional mail stack unchanged.** Postfix, Dovecot,
   Mailcow and Mailu expect a server with ports, disks and a package manager.
   Workers is a different runtime.

## Three architectures that work

### 1. Cloudflare Email Routing + existing mailbox + WhelkMail

Use Email Routing to forward role addresses on your domain to a mailbox you
already own. Connect that mailbox, plus your other accounts, to WhelkMail for a
unified interface. This is the lowest-effort setup: no migration, no new
provider, and your domain gets professional addresses immediately.

### 2. Custom domain + relay for sending + forward for receiving

Point MX records at a forwarding service, add a send-only SMTP relay for
outbound, and connect both in WhelkMail. This is useful when you want to send
from several addresses on a domain without buying a mailbox for each one. Our
guide to
[sending from a custom domain without a mail server](/blog/send-email-custom-domain-without-mail-server/)
covers the DNS details.

### 3. Full mailbox provider + WhelkMail as the interface

Choose a provider such as Fastmail, Migadu or Zoho Mail for storage and SMTP,
then use WhelkMail as the browser client. You get standard IMAP reliability with
a multi-account interface, and you keep the option to switch clients later.

## A practical setup checklist

1. **Decide where mail is stored.** Cloudflare is not the answer to this
   question. Pick a mailbox provider or an existing inbox.
2. **Add the domain to Cloudflare.** Use Cloudflare as authoritative DNS so
   Email Routing can manage the MX records.
3. **Enable Email Routing.** Create the addresses you need and verify each
   destination inbox.
4. **Add a sending path.** Use the mailbox provider’s SMTP or a relay with SPF,
   DKIM and DMARC configured.
5. **Deploy the webmail client.** For WhelkMail, follow the
   [deployment guide](https://docs.whelkmail.eu/deployment/) and connect a
   low-risk mailbox first.
6. **Test in both directions.** Send to Gmail, Outlook and a corporate address,
   then check the authentication results in the headers.
7. **Document the architecture.** Future you will forget which address is
   forwarded and which is a real mailbox.

## Frequently asked questions

### Does Cloudflare offer free email hosting?

Cloudflare Email Routing is free and forwards mail to an existing inbox. It is
not a mailbox, so it does not replace Google Workspace, Fastmail or Zoho Mail if
you need storage, IMAP and a sent folder.

### Can I receive email on Cloudflare Workers?

You can process incoming mail with Email Workers after Email Routing accepts it
for your domain. You cannot run a general-purpose inbound SMTP server on Workers.

### Can I send email from a Cloudflare Worker?

There are limited built-in sending options, usually tied to verified destinations
or an email API. For normal mailboxes and custom domains, use an authenticated
SMTP relay. Treat outbound reputation as a separate problem from running the
client.

### Is a Cloudflare-hosted webmail safe?

It can be, if credentials are encrypted, sessions are server-side, HTML is
sandboxed, remote images are blocked by default and the deployment follows the
project’s security guidance. WhelkMail encrypts credentials with AES-256-GCM in
your own D1 database, supports optional TOTP and ships no telemetry.

### Can I use Cloudflare Email Routing with Gmail?

Yes. Forward your domain addresses to Gmail. Remember that replying from the
forwarded address is a separate sending problem, and Gmail’s third-party “Send
as” is ending in January 2027. A multi-account client avoids that dependency.

## The bottom line

Cloudflare is excellent at DNS, forwarding and running the webmail application.
It is not a mailbox provider, and no amount of Worker code changes that. The
architecture that works is simple: store mail with a provider you trust, forward
role addresses with Email Routing if you need to, send through an authenticated
relay, and run the client on Cloudflare if you want a small operational
footprint.

[Open the WhelkMail demo](https://demo.whelkmail.eu) to see the client side, or
read the [deployment guide](https://docs.whelkmail.eu/deployment/) before you
connect your first domain.
