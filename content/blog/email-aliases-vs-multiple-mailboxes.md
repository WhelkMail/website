---
title: "Email Aliases vs Multiple Mailboxes: What Do You Actually Need?"
description: "Aliases, catch-alls, privacy forwarding services and real IMAP mailboxes compared: what each one can do, what it cannot, and how to combine them."
date: 2026-11-22T14:30:00+01:00
lastmod: 2026-11-22T14:30:00+01:00
sitemap:
  priority: 0.7
  changefreq: monthly
author: "WhelkMail Team"
keywords: ["email alias vs multiple email accounts", "email alias service", "hide my email", "email aliases", "simplelogin alternative", "catch-all email", "multiple mailboxes"]
---

An alias and a second mailbox both give you a new email address, which is why
they get confused. They solve different problems. An alias is a delivery
instruction: mail sent to one address is handed to an existing mailbox. A mailbox
is an independent account with its own storage, credentials, sent folder and
IMAP access.

Choosing the wrong one leads to familiar problems: replies from the wrong
address, forwarding that breaks authentication, aliases that stop working when a
service shuts down, or a pile of mailboxes nobody wants to administer.

This guide compares aliases, catch-alls, privacy forwarding services and real
mailboxes, and shows how to combine them without creating a mess.

## The short definitions

| Thing | What it is | Can you send from it? | Where mail is stored |
| --- | --- | --- | --- |
| Provider alias | A second address on the same account | Usually yes, as an identity | The same mailbox |
| Catch-all | A rule that accepts all unknown addresses on a domain | Depends on the provider | The destination mailbox |
| Privacy alias service | A generated address that forwards to your real inbox | Sometimes, with limits | The destination mailbox |
| Plus-addressing | `you+tag@domain.com` variants | No separate identity | The same mailbox |
| Send-only relay | An SMTP account for an address on a domain | Yes | Nowhere; it only sends |
| Separate mailbox | An independent IMAP/SMTP account | Yes, with its own identity | That mailbox |

The key distinction is **identity versus routing**. An alias controls where mail
goes. A mailbox controls where mail lives and how you send it.

## When an alias is the right tool

Aliases are excellent for:

- **Signups and newsletters.** Use `you+shop@domain.com` or a generated alias so
  you can identify who leaked your address.
- **Role addresses.** `info@`, `support@` and `billing@` can all deliver to one
  person without creating three mailboxes.
- **Hiding your primary address.** Privacy alias services generate unique
  addresses that forward to your real inbox.
- **Domain consolidation.** A catch-all can collect mail for addresses you never
  explicitly created.
- **Low-cost expansion.** Adding an alias is usually cheaper than adding a
  mailbox.

Aliases are not a good fit when you need independent storage, separate sending
credentials, a distinct sent folder or a mailbox you can move to another
provider without touching the original account.

## When you need a separate mailbox

Choose a real mailbox when:

- **You need to send as that address independently.** A separate mailbox has its
  own SMTP credentials, signature and sent items.
- **You need storage isolation.** Work, personal and client mail should not share
  one archive if you may need to hand over or delete one of them.
- **You need independent recovery.** If one mailbox is compromised, the others
  should not fall with it.
- **You are onboarding a person.** A shared alias is not a user account.
- **You need portability.** A mailbox can be migrated; an alias tied to a
  provider cannot.

The cost is administrative: more credentials, more filters, more backups and more
places for mail to hide.

## The alias options compared

### Provider aliases

Most mailbox providers let you add extra addresses to one account. Fastmail,
Zoho Mail, Migadu, Purelymail and many hosting providers support this. You get a
new address and often the ability to send from it, but it is still the same
mailbox underneath.

This is the simplest option if you already trust your provider and only need a
few extra addresses.

### Catch-all addresses

A catch-all accepts mail for any address on a domain that does not have an
explicit mailbox. It is useful for domains where you expect variable addresses,
and dangerous if you do not filter spam. Catch-alls attract dictionary attacks
and can fill a mailbox with noise.

Use a catch-all only if you actively monitor it, or route it through a filtering
step before it reaches your primary inbox.

### Privacy alias services

Services such as **SimpleLogin** (now part of Proton), **addy.io**, **Firefox
Relay**, **Apple’s Hide My Email**, **DuckDuckGo Email Protection** and
**Fastmail’s masked email** generate unique addresses that forward to your real
inbox. They are excellent for signups, trials and anything you expect to leak.

Their limits are predictable:

- **Sending is often restricted.** Some let you reply through the alias; others
  only receive.
- **The service becomes a dependency.** If it shuts down or you lose access, the
  aliases stop working.
- **Forwarding can affect authentication.** SPF alignment may break unless the
  service handles it carefully.
- **Abuse controls vary.** Some services limit the number of aliases or
  recipients.
- **They are not a mailbox.** You still need a real inbox behind them.

For privacy-focused signups, they are hard to beat. For a business address you
will use for years, a real mailbox or a provider alias is usually sturdier.

### Plus-addressing

`you+newsletters@domain.com` is supported by many providers and requires no
setup. It is useful for filtering and identifying leaks, but it is trivial for
spammers to strip the `+tag`, and it does not create a separate identity.

### Send-only relays

A send-only relay gives you an SMTP account for an address without a mailbox.
This is the right tool when you need to send from `info@yourdomain.com` but
receive mail somewhere else. It is not an alias in the receiving sense, and it is
not a mailbox. Our guide to
[sending from a custom domain without a mail server](/blog/send-email-custom-domain-without-mail-server/)
covers the setup.

## The forwarding trap

Forwarding an address to a mailbox looks like an alias, but it changes the
message path. Common issues:

- **SPF alignment can fail.** The forwarding server is not the original sender,
  so the receiving provider may see a mismatch.
- **DKIM can survive, but not always.** Some forwarders modify headers or
  bodies.
- **Replies may come from the wrong address.** The destination mailbox does not
  automatically know which alias received the message.
- **Unsubscribes and tracking can behave oddly.** The original sender may not see
  the real recipient.
- **A single destination outage takes down every alias.** That is the cost of
  consolidation.

Good forwarding services handle some of this with SRS or ARC, but no forwarding
setup is as clean as reading the mailbox directly.

## A hybrid setup that works

Most people need a mix, not a winner. A durable pattern:

1. **One primary mailbox** for your main identity, with a strong password and
   two-factor authentication.
2. **Provider aliases** for role addresses you will keep for years.
3. **A privacy alias service** for signups, trials and anything you do not fully
   trust.
4. **Separate mailboxes** for work, clients or anything that needs its own
   storage and sending identity.
5. **A send-only relay** if you need to send from addresses that have no mailbox.
6. **A multi-account client** so all of this appears in one place.

[WhelkMail](https://whelkmail.eu/) is built for step 6. It connects to multiple
IMAP/SMTP accounts, shows which account each message belongs to, sends from the
right identity and supports send-only relay accounts. It does not create aliases
or host mailboxes; those stay with your provider. That separation is deliberate:
you can change the client without changing your addresses.

If you want the full multi-account workflow, see
[how to manage multiple email accounts in one inbox](/blog/unified-inbox-multiple-email-accounts/).

## How to choose in five questions

1. **Do you need to send as the address?** If no, an alias is often enough.
2. **Do you need separate storage or recovery?** If yes, use a mailbox.
3. **Will you keep the address for years?** If yes, prefer a provider alias or a
   real mailbox over a free forwarding service.
4. **Is the address for signups or for people?** Signups tolerate privacy aliases;
   important contacts deserve a real mailbox.
5. **Who administers it?** A solo user can manage many aliases; a team needs
   ownership, offboarding and a documented process.

## Common mistakes

- **Using a catch-all as a spam filter.** It does the opposite.
- **Creating a mailbox for every role address.** You end up with ten inboxes and
  no time.
- **Relying on a free alias service for a business-critical address.** Services
  change terms or shut down.
- **Forwarding everything into Gmail and assuming replies will work.** Gmail’s
  third-party “Send as” is ending in January 2027.
- **Forgetting DNS.** If you send from an alias on your domain, SPF, DKIM and
  DMARC still apply.
- **Not documenting which alias belongs to which service.** Future you will need
  the map.

## Frequently asked questions

### Is an email alias the same as a second email account?

No. An alias delivers mail to an existing account. A second account has its own
storage, credentials, sent folder and IMAP access.

### Can I send email from an alias?

Sometimes. Provider aliases often support sending as an identity; privacy alias
services may support replies through the alias; plus-addressing and catch-alls
generally do not create a separate sender.

### Are email aliases good for privacy?

They are good for reducing correlation and hiding your primary address. They do
not make you anonymous, and the forwarding service can still see metadata. Choose
a provider with a clear privacy policy and, if needed, a jurisdiction you trust.

### What happens if an alias service shuts down?

Addresses issued by that service stop working unless you can migrate them. That
is why important addresses should be provider aliases or real mailboxes you
control.

### Can I have multiple mailboxes in one inbox?

Yes. A multi-account client connects to each mailbox over IMAP and displays them
together, while each account keeps its own identity and storage. WhelkMail is
designed around that model.

## The bottom line

Aliases are for routing and privacy. Mailboxes are for identity, storage and
sending. Most durable setups use both: a small number of real mailboxes, a
handful of provider aliases, privacy aliases for signups and a client that brings
them together without forwarding everything into one fragile destination.

[Open the WhelkMail demo](https://demo.whelkmail.eu) to see multiple accounts and
shared labels in one inbox, or read the
[deployment guide](https://docs.whelkmail.eu/deployment/) to run your own copy.
