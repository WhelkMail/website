---
title: "SPF, DKIM and DMARC: A Practical Setup Checklist"
description: "How SPF, DKIM and DMARC work together, the records you need, a safe rollout from p=none to reject, and the mistakes that break deliverability."
date: 2026-11-22T13:30:00+01:00
lastmod: 2026-11-22T13:30:00+01:00
sitemap:
  priority: 0.7
  changefreq: monthly
author: "WhelkMail Team"
keywords: ["spf dkim dmarc", "spf dkim dmarc checker", "dmarc record", "spf record", "email deliverability", "dkim selector", "dmarc p=none"]
---

SPF, DKIM and DMARC are three DNS records that answer three different questions
about an email:

- **SPF:** which servers are allowed to send mail for this domain?
- **DKIM:** was this message signed by someone who holds the domain’s private key,
  and was it modified in transit?
- **DMARC:** what should the receiver do when SPF or DKIM does not align with the
  visible From domain, and where should reports go?

They are authentication controls, not spam filters. A message can pass all three
and still be unwanted. A message can fail all three and still be legitimate. But
without them, receivers have less reason to trust your domain, and with a bad
DMARC policy you can silently lose real mail.

This checklist covers the records, the rollout order and the mistakes that cause
the most damage.

## How the three records work together

| Record | Protects | Looks at | Published at |
| --- | --- | --- | --- |
| SPF | The envelope sender (MAIL FROM / Return-Path) | A list of authorised sending IPs | `example.com` TXT |
| DKIM | Message integrity and domain ownership | A cryptographic signature and public key | `selector._domainkey.example.com` |
| DMARC | The visible From domain | SPF and DKIM alignment plus policy | `_dmarc.example.com` TXT |

The important word is **alignment**. SPF can pass for a domain that is not the
one recipients see in the From field. DKIM can pass with a `d=` domain that does
not match From either. DMARC checks whether at least one of them aligns with the
From domain, then applies your policy.

That is why “SPF pass” alone is not enough, and why third-party senders need
careful setup.

## SPF: the authorised sender list

An SPF record is a single TXT record on the domain:

```text
v=spf1 include:_spf.provider.example include:relay.example -all
```

It lists mechanisms that authorise sending IPs. Common mechanisms:

- `include:` pulls in another domain’s SPF record. Every include counts toward
  the DNS lookup limit.
- `ip4:` and `ip6:` authorise specific addresses.
- `a`, `mx` and `ptr` authorise hosts based on DNS records. Use them sparingly.
- `all` is the catch-all. `-all` means fail; `~all` means softfail; `?all` means
  neutral.

Practical rules:

1. **Publish exactly one SPF record per domain.** Multiple `v=spf1` records are
   invalid and produce unpredictable results.
2. **Stay under the 10-DNS-lookup limit.** Includes, `a`, `mx`, `ptr`, `exists`
   and redirects all count. Nesting includes burns lookups quickly.
3. **Include every sender.** Your mailbox provider, transactional relay,
   marketing platform, calendar service and any application that sends as your
   domain all need to be represented.
4. **Prefer `-all` once you are confident.** `~all` is common during migration,
   but it is weaker.
5. **Do not use `ptr`.** It is slow, unreliable and deprecated for this purpose.

SPF has a size limit too. If you exceed it, receivers may return a permanent
error. When the record gets too complex, flattening or a dedicated subdomain for
bulk senders is often cleaner than adding more includes.

## DKIM: the signature

DKIM signs selected headers and the body with a private key. The public key is
published in DNS under a selector:

```text
selector._domainkey.example.com TXT "v=DKIM1; k=rsa; p=MIIBIjANBg..."
```

The sender’s mail system adds a `DKIM-Signature` header with a `d=` domain and an
`s=` selector. The receiver looks up the key and verifies the signature.

Practical rules:

1. **Enable DKIM at every sender.** Your provider or relay usually generates the
   key and gives you the DNS record.
2. **Publish the exact selector they give you.** Do not guess the selector name.
   A mismatch means no verification.
3. **Use 2048-bit keys where supported.** Some DNS providers need the key split
   across multiple strings; follow their instructions.
4. **Do not edit signed content after signing.** Mailing lists and forwarders can
   break DKIM if they modify the body or headers.
5. **Rotate keys deliberately.** Keep old selectors published long enough for
   in-flight mail to verify.

DKIM is resilient to forwarding in a way SPF is not, because the signature
travels with the message. But it is not indestructible: a forwarder that adds a
footer or rewrites headers can break it.

## DMARC: policy and reporting

DMARC ties SPF and DKIM to the From domain and tells receivers what to do when
neither aligns:

```text
_dmarc.example.com TXT "v=DMARC1; p=none; rua=mailto:dmarc@example.com; fo=1"
```

Key tags:

- **`p=none`** monitors without changing delivery. Start here.
- **`p=quarantine`** asks receivers to treat failing mail as suspicious.
- **`p=reject`** asks receivers to reject failing mail outright.
- **`rua=`** is where aggregate reports go. Use a mailbox you actually read.
- **`ruf=`** requests forensic reports. Support varies, and they can contain
  personal data, so many organisations skip them.
- **`adkim=` and `aspf=`** control strict or relaxed alignment. Relaxed (`r`) is
  the default and allows subdomains; strict (`s`) requires an exact match.
- **`pct=`** applies the policy to a percentage of messages. Useful for gradual
  rollouts, though support varies.
- **`sp=`** sets a different policy for subdomains.

Practical rules:

1. **Start with `p=none` and reporting enabled.** Do not jump to `reject`.
2. **Inventory all senders before tightening.** Include CRM, support desk,
   invoicing, calendars and anything else that uses your domain.
3. **Review reports for at least a few weeks.** Look for legitimate sources that
   fail alignment.
4. **Move to `quarantine`, then `reject`.** Give internal teams time to fix
   configuration.
5. **Publish DMARC on subdomains too, or use `sp=`.** Attackers like unmanaged
   subdomains.
6. **Keep the reporting address stable.** Losing it blinds you.

## A safe rollout, step by step

### 1. Inventory every sender

List every system that sends email as your domain: the mailbox provider, the
transactional relay, the newsletter platform, the CRM, the helpdesk, the
invoicing tool, the website contact form and any application using
`no-reply@yourdomain.com`.

For each one, record the sending domain, the envelope sender, the DKIM selector
and whether you control its DNS records.

### 2. Publish SPF

Build one SPF record that includes every legitimate sender. Remove obsolete
includes. Check the DNS lookup count before publishing. Use `~all` during the
transition if you are unsure, then move to `-all`.

### 3. Enable DKIM everywhere

Generate or retrieve the DKIM records from each sender and publish the selectors.
Send a test message from each system and inspect the `DKIM-Signature` header.

### 4. Publish DMARC in monitor mode

Start with:

```text
v=DMARC1; p=none; rua=mailto:dmarc@yourdomain.com; fo=1
```

Do not add `p=reject` yet. You are collecting data, not enforcing.

### 5. Send tests across providers

Send to Gmail, Outlook, Yahoo and a corporate mailbox. Open the full headers and
confirm:

- SPF passed for the expected domain.
- DKIM passed for the expected selector and domain.
- DMARC passed with alignment.
- The message did not land in spam.

Useful tools include **MXToolbox**, **Google Admin Toolbox**, **mail-tester**,
**Port25’s verifier**, **dmarcian** and **EasyDMARC**. Each has limits; no single
score proves deliverability.

### 6. Read the reports

Aggregate DMARC reports show which IPs and domains are sending as you and whether
they align. Expect noise at first. Fix legitimate sources that fail. Investigate
sources you do not recognise.

### 7. Tighten the policy

When legitimate mail passes consistently, move to `p=quarantine`, then
`p=reject`. Announce the change to anyone who sends on your behalf. Keep
monitoring; new tools and new subdomains appear over time.

## The mistakes that break deliverability

- **Two SPF records.** Invalid. Merge them into one.
- **Missing a sender.** A CRM or website form that sends without authorisation
  will fail DMARC once you enforce.
- **Too many DNS lookups.** Includes nest, and the limit is easy to exceed.
- **Wrong DKIM selector.** The record exists, but nobody looks it up.
- **Publishing DMARC at the wrong name.** It belongs at `_dmarc.example.com`, not
  on the root.
- **Jumping straight to `p=reject`.** You will block legitimate mail you forgot
  about.
- **Ignoring subdomains.** An unprotected subdomain can be used for spoofing.
- **Treating authentication as a spam fix.** Content, list quality and reputation
  still matter.
- **Forgetting forwarding.** Forwarding can break SPF alignment; DMARC reports
  will show it.
- **Not documenting ownership.** When the person who configured DNS leaves, the
  records become archaeology.

## Where the mail client fits

A webmail client such as [WhelkMail](https://whelkmail.eu/) sends through your
mailbox provider or an SMTP relay. It does not publish DNS records and it cannot
make an unauthenticated domain trustworthy. What it can do is make the setup
easier to live with: connect multiple accounts, use the correct sender identity
per account, and support send-only relay accounts for domains that do not run
their own SMTP server.

If you are setting up a domain without a mail server, the
[send-only relay guide](/blog/send-email-custom-domain-without-mail-server/) walks
through the DNS side. If you are choosing a provider, the
[Gmail alternatives comparison](/blog/gmail-alternatives-custom-domain-2026/)
includes the authentication caveats for each option.

## Frequently asked questions

### Do I need all three records?

For a domain that sends mail, yes. SPF and DKIM authenticate senders; DMARC ties
them to the From domain and gives you reporting and policy. Google, Microsoft and
many other receivers expect them.

### Can I use SPF without DKIM?

You can, but DMARC can still pass on SPF alignment alone. DKIM adds integrity and
survives some forwarding scenarios, so running both is the practical default.

### What does DMARC `p=none` do?

It monitors. Receivers collect and report alignment results but do not change
delivery based on the policy. It is the right starting point, not the destination.

### Why does my SPF pass but DMARC fail?

The SPF domain (envelope sender) probably does not align with the From domain.
Check the Return-Path and the DMARC alignment mode. A third-party sender may need
a custom Return-Path or a DKIM signature with your domain.

### How long before I can move to `p=reject`?

It depends on how many senders you have and how clean your reports are. Many
organisations take several weeks. Do not rush it; a premature reject policy can
silently drop invoices, password resets and customer replies.

### Does Cloudflare DNS support these records?

Yes. Cloudflare DNS can publish TXT records for SPF, DKIM and DMARC. Some providers
require long DKIM keys to be split into multiple quoted strings; Cloudflare’s
editor handles that pattern.

### Can WhelkMail fix my SPF or DKIM?

No. WhelkMail is a client. It sends through your provider or relay, so
authentication is configured at the domain and sender. WhelkMail supports
send-only relay accounts, which is often the missing piece for a domain without
SMTP.

## The bottom line

Publish one SPF record that lists every sender, enable DKIM at each sender,
publish DMARC in monitor mode, read the reports, then tighten the policy. The
work is mostly inventory and patience, not DNS wizardry. Do it before you need
it: authentication is much easier to fix while your mail is still arriving.

[Try the WhelkMail demo](https://demo.whelkmail.eu) to see how multiple accounts
and send-only relays work in one interface, or read the
[deployment guide](https://docs.whelkmail.eu/deployment/) to run your own copy.
