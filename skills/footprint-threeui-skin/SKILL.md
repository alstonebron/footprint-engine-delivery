---
name: footprint-threeui-skin
description: Re-skin a self-contained ThreeUI (or similar single-file HTML) template with Footprint's brand story — change ONLY user-visible text while keeping visuals, layout, CSS, JS, and assets pixel-identical. Use when the user wants a ThreeUI template "as a Footprint experience", wants Footprint copy/story on an existing HTML template, or asks to add re-skinned templates to the Template Studio catalog. Covers the copy deck, per-template angle mapping, the text-slot swap workflow, catalog registration (catalog-v3.json + catalog-page-v3.html), and deployment via the footprint-engine-delivery GitHub repo.
---

# Footprint ThreeUI Skin

Re-skin an existing self-contained HTML template (ThreeUI catalog pages, award-style
single-file sites) with Footprint's story. The result must look and behave
pixel-identical to the original — only the words change.

Reference implementation: `footprint-threeui/footprint-kage/index.html`
(the Kage temple-night template re-skinned as "Footprint — Where growth becomes a system").

## The Rule

**Change only user-visible text. Never touch the visual system.**

Change: `<title>`, meta description, headings, paragraphs, nav/brand labels,
buttons, card titles/descriptions, captions, stats, footer text, decorative
label spans (Japanese-style decorative text slots may become short English
CAPS labels), aria-labels, and text-bearing JS data (title/story registries,
card arrays, stage labels).

Never change: CSS, colors, fonts, layout, animations, JS logic, variable
names, class names, IDs, asset paths, inline images/media/base64, SVG
shapes/paths, canvas glyph alphabets.

Keep replacement text similar in length to the original per slot —
fixed-height text masks and curved/circular text break when text grows.

## Footprint Copy Deck

Source of truth: `footprint-site/src/config.js`. Key items:

- **Brand:** Footprint · growwithfootprint.com · hello@growwithfootprint.com
- **Tagline:** "The system between attention and revenue."
- **Intro:** "Footprint builds growth infrastructure around owner-led service
  businesses — no fluff, no rented systems, no disconnected marketing."
- **Hero:** "Your business should not feel this hard to grow." +
  "You built something real. But the leads are scattered, the follow-up is
  manual, the website does not tell the full story, and too much still depends on you."
- **CTA:** "Get Clear" · "No black boxes · Growth leak audit · Response within 24 hrs"
- **Stats:** 19 City Pages Deployed · 60K+ YouTube Subscribers ·
  30-day Full Brand Launch · 6-phase Growth System
- **6 phases:**
  1. CLARIFY — make your value obvious before you spend money getting louder
  2. BUILD TRUST — make your online presence match the quality of your work
  3. CREATE DEMAND — help the right people find you before they find someone else
  4. CONVERT INTEREST — turn attention into calls, forms, bookings, and sales conversations
  5. FOLLOW UP — keep leads from slipping through the cracks with CRM and automation
  6. OPTIMIZE — use real data to improve what works and cut what does not
- **Outcomes:** "Growth feels different when every piece has a job."
- **Case studies:** XactInsure (solo Medicare agent → 19-city SEO machine,
  GHL CRM, daily AI blog, automated nurture) · Golden Memorial/Lincoln Heritage
  (website request → 20-section Master Implementation Blueprint, approved first
  review) · The Clean Hustle (Cape Coral side hustle → automated retail brand
  in 30 days: shop site, GHL pipeline, 5 SMS automations, Android app)
- **Philosophy:** "We don't rent you a system. You own every piece when we're
  done." · "No black boxes. No platform lock-in. Full export rights, day one."
- **Final CTA:** "The business was supposed to give you freedom." +
  "Somewhere between your first client and today, it started running you
  instead. That is what we are here to fix — one phase at a time, with
  nothing rented and nothing locked."
- **Footer blurb:** "We install acquisition, conversion, and retention
  machines that compound while you sleep."
- **Problems:** invisible / unclear / unproven / unfollowed / untracked / owner-dependent

## Per-Template Angle Map

Each template gets ONE distinct Footprint angle mapped to its visual metaphor.
Do not reuse the generic hero on every template.

| Template | Angle |
|---|---|
| kage (temple night walk) | The Growth System as a guided path — 6 phases as chapters |
| sylva (living moss world) | Organic growth — "Growth is a living system", compounds while you sleep |
| tower (construction study) | "The system, floor by floor" — build stages narrate the 6 phases |
| landscape (terrain vista) | "Know your terrain" — market mapping, stop guessing |
| sketchbook (travel journal) | "Field notes from real builds" — case studies as journal entries |
| spark (particle badge) | "Receipts, not promises" — the proof mark (title swap only if canvas-only) |
| synthralos (halftone shader) | "See the pattern in your numbers" — Noise → Signal → Footprint (Optimize) |
| shelf (3D bookshelf) | "The Growth Library" — each volume is a phase + a Case Files volume |
| books (bestsellers showcase) | "Stories that sell" — case studies as featured titles |

## Workflow

1. **Locate text slots.** Files are often 1–17 MB (inline media). Never read
   whole. Use `rg -n` for `<h1`, `<h2`, `<p`, `<span`, `<title`, `textContent`,
   `innerHTML`, `aria-label`, then read ±30 lines around hits. Also check JS
   data registries (e.g. a `STYLES`/`BOOKS`/`PAGES` array) — story text often
   lives there, and changing it updates rendered text with zero JS edits.
2. **Watch for canvas-rasterized text.** If a wordmark is drawn via a bespoke
   glyph alphabet (per-letter bezier paths), swapping the word breaks the
   render (NaN geometry) unless the new word uses only available letters.
   Verify in a browser before changing; if unsafe, re-skin only the HTML text
   (e.g. just the `<title>`). Giant in-scene wordmarks that auto-scale to
   frame width are usually safe.
3. **Swap the copy** per the template's assigned angle. Every nav item, card,
   stat, and footer must carry Footprint story — including alternate states
   (JS-driven style/country/entry cycles must never expose original story text).
4. **Verify:**
   - `rg` for original brand/story keywords → zero user-visible hits
     (asset file names, CSS comments, class names, JS identifiers may remain)
   - tag balance roughly intact
   - `rg` confirms 3–4 new strings landed
   - open in browser (`file://`) and screenshot — original tab may be cached;
     copy the file to a new name to force a fresh load
5. **Register in catalog** (see references/catalog.md).
6. **Deploy** (see references/catalog.md).

## Workspace Hazards

- Files outside `footprint-site/` get swept into `.publish-stash` by the
  publish process and may vanish mid-task. **Commit work to git early and
  often** — committed files survive.
- Robocopy exits 1 on success — do not chain it with `&&`.
- Re-serving an edited `file://` page in the same browser tab serves the
  cached pre-edit version. Copy to a new filename for verification previews.
