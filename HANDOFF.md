# SHE (Saudi House of Expertise) — Website Handoff

## What this is
A single-file, bilingual (English/Arabic) B2B corporate marketing + lead-gen website for **Saudi House of Expertise (SHE)**, a Saudi trading/procurement company (est. 1971). Built as a static site with no build step, no framework, no backend — deployed via GitHub Pages.

- **Live source file:** `index.html` (~1450+ lines — all HTML, CSS, and JS inline in one file)
- **404 page:** `404.html`
- **Images:** `img/` directory
- **Repo:** `tabassum24khanam-max/she`
- **Working branch:** `claude/business-website-build-7kdx2h` (all recent work is here; check whether it has been merged to `main` before continuing — `main` may be behind)

## Architecture

**No build tooling at all.** Open `index.html` directly, or serve the folder with any static file server. Everything — CSS and JS — lives inline in `<style>` and `<script>` tags inside `index.html`.

### Bilingual (EN/AR) system
- Every translatable element carries `data-en="..."` and `data-ar="..."` attributes holding the English/Arabic text.
- A JS routine walks the DOM and swaps `.textContent` (or attributes like placeholder) based on the active language.
- Language choice persists via `localStorage`.
- Arabic mode adds a `body.ar` class which flips layout to RTL using **CSS logical properties** (`margin-inline`, `padding-inline`, `inset-inline`) rather than hardcoded left/right — this is intentional and should be preserved in any new CSS.
- Some elements were deliberately restructured to put `data-en`/`data-ar` on dedicated `<span>` children instead of the parent, because swapping `.innerHTML`/`.textContent` on a parent wipes sibling nodes (icons, nested elements). If you add new bilingual elements with icons or nested markup inside, follow this pattern — put the swappable text in its own span.

### Key JS pieces (search `index.html` for these)
- `initForm()` — handles the RFQ (Request for Quotation) form submission. Builds a `mailto:` link with pre-filled fields (no backend, no email service — it just opens the user's email client).
- `renderSupply()` — dynamically builds the "what we supply" horizontal carousel cards from a JS data array (search for the array of product lines). A 13th "CTA card" is appended after the real product cards.
- Marquee logic — client-logo marquee with pause-on-click (click pauses, resumes after ~3s) and pause-on-hover.
- IntersectionObserver — drives `.reveal` scroll-triggered fade/slide-in animations throughout the page.
- Horizontal carousel — drag-to-scroll + auto ping-pong motion for the supply cards and projects.

### Section order (top to bottom, roughly)
Loader → Nav → Hero → Quick Contact CTA → About → Team → Supply carousel → Stats → Projects → Sectors → Process → Clients (marquee) → Contact/RFQ form → Footer.

## Branding
- Real SHE logo is at `img/logo.png` (575×434, transparent PNG) — used in header, footer, and the loading-screen intro.
- `img/logo-og.png` is a square 1200×1200 crop used only for `og:image`/`twitter:image` social preview meta tags.
- Loader intro: logo sits on a white circular backdrop (`::before` pseudo-element, `inset:-20px`, not an inline size on the `<img>`) with a white horizontal line beneath it (`::after`).
- Hero background gradient is intentionally darker on the right side (flips to darker-left in `body.ar` via a separate rule) so hero text stays readable in both LTR and RTL.
- Client/partner logos in the marquee are shown in full color (grayscale filter was deliberately removed per client feedback).

## Known inconsistencies / unfinished cleanup (IMPORTANT — read before touching contact info)

The site's contact details have been edited incrementally across many sessions and are **currently inconsistent**. Some spots were updated to the new email/phone, others were missed. As of the last commit (`38e6fb2`):

**Correct / already updated:**
- Footer → single phone `+966 50 316 4927`, email `zubairceo@gmail.com`
- "Reach us directly" block (mid-page, next to RFQ form) → Head Office address, single phone `+966 50 316 4927`
- RFQ form success message → only shows a WhatsApp button (`wa.me/966503164927`); the old `800 6070 000` call button was removed per explicit request
- Website URL was requested to change to `sa-she.com` in an earlier session — **verify this actually landed everywhere**, see stale references below

**Still stale — NOT yet fixed, flagged but out of scope for the last few asks:**
- Line ~812 and ~1251ish: the RFQ form's `mailto:` target and the form-note text still say `dr.khan@she.sa.com` (old email) instead of `zubairceo@gmail.com`. **This is functionally important** — the RFQ form literally emails the wrong address right now.
- Line ~850: a "Customer Care (Toll-free)" block in the "Reach us directly" section still shows `800 6070 000` — this block was NOT part of the last cleanup request (user only asked to trim the *phone* line above it to a single number) but it contradicts the "only one number" instruction given for that same section, so it's likely an oversight the user will want gone too.
- Line ~857: "Email & Web" block still shows `dr.khan@she.sa.com` and `www.she.sa.com` — both are stale (old email, old domain — should probably be `zubairceo@gmail.com` / `sa-she.com`).

**Recommended immediate next step for whoever picks this up:** grep the whole file for `dr.khan`, `she.sa.com`, `8006070000`, `800 6070 000`, and `011` to find every remaining stale reference, confirm with the user which should become `zubairceo@gmail.com` / `+966 50 316 4927` / `sa-she.com`, and fix them all in one pass so the contact info is finally consistent site-wide. Do this before doing anything else — a lead-gen site silently emailing an abandoned inbox is the highest-priority bug here.

## Working conventions established in this project
- No comments in code unless explaining a non-obvious workaround.
- All contact-info edits are surgical (`Edit` tool, exact string match) — never a full-file rewrite.
- Commits are small and scoped to one user-requested change each; pushed directly to `claude/business-website-build-7kdx2h`, no PR opened so far (never open one unless explicitly asked).
- The user communicates via voice-to-text (expect phrasing like "zero five zero three..." for phone digits, filler words, self-corrections mid-sentence) — read requests for the underlying intent, not literal transcription artifacts.
- The user reviews changes primarily via **mobile screenshots**, so mobile layout/spacing matters as much as desktop.
- User has repeatedly asked for extremely literal fixes (e.g. "remove this number" from a screenshot) — don't over-scope beyond what's shown/asked, but do flag related inconsistencies you notice (as above) rather than silently fixing or silently ignoring them.

## How to preview locally
No install needed:
```bash
cd /path/to/repo
python3 -m http.server 8000
# open http://localhost:8000/index.html
```
Or just open `index.html` directly in a browser (all assets are relative paths, no CORS issues expected for local file:// either, but a local server is safer for the fetch-free features present).

## Deployment
GitHub Pages, auto-builds from `main` on push (per earlier session notes) — confirm the Pages source branch in repo settings before assuming a push to the feature branch goes live. Merge to `main` (or open a PR) when the user confirms the branch is ready.
