# Invitation Engine

A single self-contained HTML page that makes a digital invitation: edit it, get a
shareable link, and export a share image, a 300 dpi print file or an animated MP4.

No build step, no framework, no database, no accounts. One file.

## How a link works

The whole invitation is compressed into the part of the URL after the `#`.

```
https://<user>.github.io/<repo>/#i=<payload>
```

Browsers never transmit a URL fragment to a web server, so the page is served as a
static file and the invitation itself only ever exists in the link and in the reader's
browser. Nothing about the event — the child's name, the address, the time — reaches
any server, including this one.

One hosted page therefore serves any number of invitations. There is nothing to
provision per event.

## The three views

| URL | Who it is for |
| --- | --- |
| `/` | the editor — fill it in and build a link |
| `/#i=<payload>` | the guest — the invitation itself |
| `/#i=<payload>&host=1` | the host — RSVP tracker and guest list |

Add `&edit=1` to a guest link to reopen it in the editor.

## RSVP without a backend

A guest's reply is composed as a message that is readable first and machine-readable
second: plain lines a host can act on at a glance, plus an `r1.<code>` on the last
line. Pasting that message into the host view fills a row in one tap. A guest who just
texts normally still works — the host types the row in.

The guest list is held in the host's own browser (`localStorage`), keyed by a hash of
the event. That is where its privacy property comes from: someone else opening the same
host link sees their own empty list, never the host's. Because it is per-device, the
host view can also emit an `ie1.<code>` backup string to move a list between devices.

Optionally the RSVP form can post to a form endpoint (Formspree, Basin, Getform) instead.

## Design

The card is drawn twice — as HTML for the live page, and onto a canvas for the PNG and
the video. Ornaments and background motes are stored once as SVG path strings and handed
to both renderers, to `<path d>` in the DOM and to `Path2D` on the canvas, so the two
cannot drift apart.

A theme is a row in a table: palette, two typefaces, a set of motif glyphs and an
ornament. Adding a look is a data entry, not a code change.

## What it needs from the browser

Feature detection is live, and the editor prints the results. Everything degrades to
something that still works rather than failing:

- `CompressionStream` shortens the link payload; without it the link is longer.
- `MediaRecorder` records the video, preferring MP4 and falling back to WebM.
- Web fonts come from Google Fonts; offline, each theme falls back to a system stack.

The QR encoder is written from scratch — byte mode, correction level L, versions 1 to 20.

## Licence

All rights reserved.
