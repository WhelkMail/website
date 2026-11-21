---
title: "Send Email From Your Domain Without a Mail Server"
description: "Use a send-only SMTP relay with your custom domain: how to receive mail elsewhere, send from your own address and get SPF, DKIM and DMARC right."
date: 2026-10-10
lastmod: 2026-10-10
sitemap:
  priority: 0.7
  changefreq: monthly
author: "WhelkMail Team"
keywords: ["send email from custom domain", "send-only smtp", "smtp relay", "email without mail server", "cloudflare email"]
---

A custom domain makes an email address memorable and trustworthy. But you do not
need to run Postfix, Dovecot or a VPS to get one. A common and often overlooked
setup separates the two jobs: **receive mail with one provider** and **send mail
through an SMTP relay**. You get `you@yourdomain.com` without owning a mail
server, and you can manage it from a webmail client such as
[WhelkMail](https://whelkmail.eu/).

This guide explains how receive-only, send-only and full mailbox setups differ,
how to configure a relay correctly, and which deliverability details actually
matter.

## Receiving and sending are separate problems

Email is often described as one service, but a mailbox does two independent
things:

- **Receiving** requires an MX record, a server that accepts SMTP, storage and
  spam filtering.
- **Sending** requires an SMTP server with a good reputation and DNS
  authentication.

Many providers bundle both. But you can mix them. For example, you can receive
mail at a mailbox provider and send through a transactional email service. This
is sometimes called a **send-only domain** or **relay account**.

## When a send-only setup makes sense

- You have a domain used for a website and want a professional address without
  paying for a full mailbox per address.
- You receive mail through a forwarding service or a single mailbox, but need to
  reply from several addresses on the domain.
- You run an application that sends notifications from `no-reply@yourdomain.com`.
- You already have a mailbox provider with good storage but weak outbound
  deliverability.
- You want to avoid running a mail server while keeping control of the domain.

It is less suitable when you need rich mailbox features for many users. In that
case, a normal mailbox provider or Google Workspace/Microsoft 365 is simpler.

## The three common domain setups

| Setup | Receiving | Sending | Best for |
| --- | --- | --- | --- |
| Full mailbox provider | Provider MX | Provider SMTP | Most individuals and teams |
| Receive + forward | MX to forwarder | Relay or provider | Domains with one real inbox |
| Send-only relay | MX elsewhere | Relay SMTP | Apps, side projects, extra addresses |

## How a send-only relay works

1. Your domain’s MX records point to the service that receives mail.
2. You create a relay account with an SMTP provider such as SMTP2GO, Resend,
   Mailgun, Brevo, SendGrid, Mailjet or Postmark.
3. The relay verifies that you own the domain, usually through DNS records.
4. Your mail client authenticates to the relay over SMTP and sends with a sender
   address on your domain.
5. Replies go to the address you choose, which may be a real mailbox elsewhere.

WhelkMail supports send-only relay accounts alongside normal IMAP/SMTP
accounts. You can reply from any address on the relay’s verified domain without
creating a separate mailbox for each address. That is useful for `info@`,
`sales@` or `hello@` addresses that all land in one inbox.

## DNS records you cannot skip

A relay will usually give you the exact records to add. The concepts are the
same everywhere.

### SPF

SPF lists the servers allowed to send for your domain. Include the relay’s
mechanism, for example `include:_spf.provider.com`. Keep the record under the
10-DNS-lookup limit; every include counts.

### DKIM

DKIM signs outgoing messages with a private key and publishes the public key in
DNS. The relay handles signing, but you must publish the selector it gives you.
Do not guess the selector format.

### DMARC

DMARC tells receivers what to do when SPF or DKIM alignment fails. Start with
`p=none` and a reporting address, review the reports for a few weeks, then move
to `p=quarantine` and eventually `p=reject` when you are confident.

### MX

MX records control receiving, not sending. Changing your relay does not require
changing MX. Changing your mailbox provider does.

## Step-by-step: a custom domain with a relay

1. **Choose where mail will be received.** This can be a mailbox provider, a
   forwarding service or an existing inbox.
2. **Add the domain to the relay.** Verify ownership with the DNS record the
   provider supplies.
3. **Publish SPF, DKIM and DMARC.** Use a single SPF record per domain and
   subdomain.
4. **Create the addresses you need.** Many relays let you send from any address
   on a verified domain.
5. **Connect the relay in your mail client.** WhelkMail treats it as a send-only
   account: no IMAP, just SMTP.
6. **Send a test to multiple providers.** Check Gmail, Outlook and a corporate
   mailbox, then inspect the headers for SPF, DKIM and DMARC pass results.
7. **Monitor DMARC reports.** Fix alignment problems before increasing the
   policy.

## Deliverability habits that matter more than the relay

- **Send from a real address.** `no-reply@` is acceptable for notifications, but
  conversations need a reply path.
- **Keep your list clean.** Bounces and spam complaints hurt reputation faster
  than any DNS tweak helps it.
- **Warm up new domains.** Do not send a large campaign on day one from a brand
  new domain.
- **Use consistent From domains.** Changing domains frequently resets trust.
- **Avoid link shorteners in cold email.** They are a common spam signal.
- **Check your content.** Authentication proves who you are; it does not make
  unwanted mail welcome.

## What you give up with a send-only account

A relay is not a mailbox. It does not store received mail, and it usually does
not provide folders, labels or search. You need a receiving setup somewhere
else, and you need a client that can combine the two views.

WhelkMail handles that by showing send-only relay accounts next to normal
mailboxes. You can send from the relay address while reading replies in the
mailbox where they actually arrive.

## Frequently asked questions

### Can I send email from my domain without receiving it?

Yes. Sending uses SMTP and DNS authentication; receiving uses MX records and a
mailbox. A send-only relay is a valid setup for addresses that only need to send.

### Do I need an SMTP relay if I already have a mailbox?

Not necessarily. Many mailbox providers include SMTP. A relay becomes useful when
you need better deliverability, more sending volume or additional addresses
without extra mailboxes.

### Is a send-only domain bad for deliverability?

No, as long as SPF, DKIM and DMARC are configured and the domain has a
reasonable sending history. A dedicated relay with a good reputation can be
better than a poorly configured mailbox server.

### Can WhelkMail send from a domain without SMTP?

Yes. WhelkMail supports send-only relay accounts for domains that do not have
their own SMTP server, with several relay providers supported.

## Own the address, not the server

A custom domain should not require a mail server, a VPS or a weekend of DNS
debugging. Receive where it is convenient, send through a reputable relay and
use a client that keeps both sides in one place.

[Try WhelkMail](https://demo.whelkmail.eu) or read the
[deployment guide](https://docs.whelkmail.eu/deployment/) to connect your first domain.
