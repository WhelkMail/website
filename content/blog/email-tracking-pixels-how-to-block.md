---
title: "Email Tracking Pixels: How They Work and How to Block Them"
description: "A practical guide to email tracking pixels: what senders learn, why open rates are unreliable, and how to block remote images in Gmail, Outlook and webmail."
date: 2026-11-22T15:30:00+01:00
lastmod: 2026-11-22T15:30:00+01:00
sitemap:
  priority: 0.7
  changefreq: monthly
author: "WhelkMail Team"
keywords: ["email tracking pixels", "stop email tracking", "block email tracking pixel", "turn off email tracking", "email privacy", "block remote images"]
---

A tracking pixel is a tiny image with a unique address. When your mail client
loads it, the sender’s server records that the message was opened. There is no
dialog, no permission prompt and usually no visible sign. For newsletters and
sales tools, open tracking is a standard feature. For the person receiving the
mail, it is a small but real privacy leak that is easy to reduce.

This guide explains what a tracking pixel can and cannot reveal, why open rates
are less reliable than they look, and how to block remote images in the clients
most people use. It also covers the legal side for senders, because “we track
opens” is not automatically allowed.

## What a tracking pixel actually is

A tracking pixel is a 1×1 image, often transparent, embedded in an HTML email:

```html
<img src="https://track.example.com/open/abc123.gif" width="1" height="1" alt="">
```

The `abc123` part is unique to the recipient or the campaign. When the client
requests the image, the tracking server logs the request. No JavaScript is
needed, and the image is designed to be invisible.

Some senders use a slightly different technique: a unique link wrapped in a
redirect, or a CSS background image. The principle is the same. Anything that
causes the client to make a network request can be used as a signal.

## What the sender can learn

A single pixel request can reveal more than “opened”:

- **That the message was opened**, and at what time.
- **How many times** it was opened, if the image is requested repeatedly.
- **Your IP address**, which can be mapped to an approximate location and
  internet provider.
- **Your device and browser or mail client**, from the User-Agent header.
- **Whether the message was forwarded or opened in a different environment**, in
  some configurations.
- **Link clicks**, if the links are wrapped in the same tracking system.

What it cannot do is read your other email, access your files or install
software. The risk is profiling and correlation, not remote code execution. That
distinction matters: tracking pixels are a privacy problem, not usually a malware
problem. Phishing emails can still use images as part of an attack, which is one
more reason to block remote content by default.

## Why open rates are unreliable

Open tracking sounds precise, but it measures image requests, not human
attention. Several things distort the number:

- **Image proxies.** Gmail and other providers load images through a proxy,
  which can trigger the pixel even if you never open the message.
- **Apple Mail Privacy Protection.** Apple Mail can prefetch remote content,
  making messages appear opened regardless of whether you read them.
- **Preview panes.** A message can be marked opened because it appeared in a
  preview pane for a second.
- **Blocked images.** If the client blocks remote images, the open is never
  recorded. Senders then undercount engaged readers.
- **Text-only clients.** No image request means no open, even if the message was
  read carefully.

This is why serious senders treat opens as a directional signal, not a fact.
Click tracking and replies are stronger evidence of engagement.

## How to block tracking pixels

The most reliable defence is to stop remote images from loading automatically.
You can still load them for a specific message when you trust the sender.

### Gmail

Gmail’s web settings include an option to ask before displaying external images.
When it is enabled, Gmail shows a prompt and blocks remote content until you
choose to load it. Gmail also proxies images, which limits what the sender sees,
but the proxy can still request the pixel, so blocking is stronger than relying
on the proxy alone.

### Outlook

Outlook desktop and Outlook on the web have image-blocking settings. In the
desktop client, look for the Trust Center options for automatic picture
downloads and disable them. On the web, check the privacy and image settings in
the reading pane. Corporate tenants can enforce these settings centrally.

### Apple Mail

Apple Mail Privacy Protection hides your IP address and prefetches remote
content. That reduces some profiling, but it can also make open tracking
meaningless rather than preventing it. If you want to block images entirely,
disable remote content loading in the app’s privacy settings.

### Thunderbird

Thunderbird blocks remote images by default and shows a notification bar when a
message contains remote content. You can allow images for a sender or a message
when needed. This is a good default for a desktop client.

### Webmail clients

The quality of image handling varies widely. Some traditional webmail setups
load images immediately; others proxy them; a few block them by default. If
privacy is a priority, choose a client that blocks remote content until you
explicitly allow it, and that sandboxes HTML email instead of rendering it with
full page privileges.

[WhelkMail](https://whelkmail.eu/) blocks remote images by default, renders HTML
email in a sandboxed frame with scripts disabled and lets you load images per
message. It does not fetch tracking pixels just to make a newsletter look
complete.

### Privacy-focused providers

Some providers go further. Proton Mail is known for image proxying and privacy
defaults, and Tutanota is designed around encryption and minimal remote content.
Fastmail also proxies images. These measures help, but they are not a substitute
for understanding what your client loads and when.

## Should you use a plain-text view?

Plain text is the most private way to read email, because there are no remote
images and no HTML tracking tricks. The trade-off is that many newsletters and
transactional emails assume HTML and look broken in plain text.

A practical compromise is to keep HTML but block remote content by default.
Load images only for senders you trust, and use plain text for anything
suspicious.

## A quick way to spot a tracking pixel

You can often find tracking pixels by viewing the raw message source:

1. Open the message’s “Show original”, “View source” or similar option.
2. Search for `<img` tags with `width="1"` or `height="1"`.
3. Look for URLs with long unique identifiers, `open`, `track`, `pixel` or a
   known marketing domain.
4. Check whether links are wrapped in redirect domains.

This is not definitive. Some pixels are larger or disguised, and some tracking
happens through link redirects rather than images. But it is a useful sanity
check when a message feels off.

## The sender’s side: tracking is not free of obligations

If you send email, open tracking is a data processing activity. In the EU, the
ePrivacy rules and the GDPR both matter, and consent requirements vary by context.
A common pattern is:

- **Newsletter and marketing email:** obtain consent where required, document the
  lawful basis and provide an easy opt-out.
- **Transactional email:** track only what you need to operate the service.
- **B2B sales email:** do not assume legitimate interest covers everything;
  document your assessment and respect objections.
- **Sensitive contexts:** health, finance, religion and similar categories
  deserve extra caution.

Popular sales engagement tools such as **Mailtrack, Yesware, HubSpot Sales Hub,
Streak, Mixmax, Outreach and Salesloft** all offer open and click tracking.
Using them does not remove the need for a privacy notice, a lawful basis and a
way to object. Apple’s Mail Privacy Protection and image proxies have also made
the data less reliable, so build your process around replies and clicks rather
than opens alone.

## Common myths

- **“Opening an email downloads a virus.”** A pixel is an image request. It does
  not execute code in your mail client. Malicious attachments and links are the
  bigger risk.
- **“Blocking images breaks all email.”** It makes some newsletters look plain.
  You can load images per message.
- **“Open rates are accurate.”** They are distorted by proxies, prefetching,
  preview panes and blocked images.
- **“Using a privacy provider makes me anonymous.”** It reduces exposure, but
  your address, provider metadata and replies still exist.
- **“Tracking is only a marketing problem.”** Recruiters, CRM tools and even some
  personal senders use tracking, and the privacy implications are the same.

## Frequently asked questions

### Do tracking pixels work if I block images?

No. If the client never requests the image, the tracking server never records the
open. The sender may still see clicks if you click a tracked link.

### Does Gmail block tracking pixels automatically?

Gmail proxies images and offers a setting to ask before displaying external
images. The proxy limits some data exposure, but the pixel can still be requested.
Enable the prompt if you want a real block.

### Can I stop all email tracking?

You can stop most image-based tracking by blocking remote content. Link tracking
is harder, because clicking a tracked link necessarily contacts the sender’s
server. Use a link scanner or avoid clicking suspicious links.

### Are tracking pixels illegal?

Not inherently. Their use depends on context, consent, transparency and applicable
law. In the EU, marketing tracking usually needs a lawful basis and a privacy
notice. This article is not legal advice.

### Does WhelkMail block tracking pixels?

WhelkMail blocks remote images by default and sandboxes HTML email with scripts
disabled. You can choose to load images for a specific message. It does not
display remote content just to make a layout look correct.

## The bottom line

Tracking pixels are simple, common and easy to neutralise. Disable automatic
remote images, load them only when you trust the sender, and prefer clients that
block by default. If you send email, treat tracking as personal data processing,
not as a free analytics feature.

[Try WhelkMail](https://demo.whelkmail.eu) to see privacy-first image handling in
a multi-account inbox, or read the
[deployment guide](https://docs.whelkmail.eu/deployment/) to run your own copy.
