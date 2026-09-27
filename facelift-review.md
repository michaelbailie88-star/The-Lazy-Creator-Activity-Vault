# The Activity Vault — Facelift Review (Phase 1 + Phase 2)
Branch: `facelift` (off latest `main`). No code changed. Read-only evaluation + plan.

## 3 DISCREPANCIES TO RESOLVE BEFORE BUILD
1. **Modules: the "6 / 5 / 4" problem.** `<title>` says "Five Modules"; hero eyebrow says "FIVE MODULES"; landing header shows 5 pills (AdaptED, CampED, TeenagED, RegulatED, ParentED); but the app's `MODULE_LIST` has only **4** (AdaptED, CampED, TeenagED, RegulatED). ParentED + UnboxED + "Mrs. Bailie" exist as separate surfaces, not modules. Your brief says **6**. Which 6 are canonical?
2. **Charity give-back program with 8 named partners: NOT FOUND.** Grep of index.html + app.html finds no give-back program and no 8 partners. The only "food bank / shelter / food drive" hits are inside *activity content* (service-project activities). Do you have the 8 partners + copy, or is this to be designed fresh?
3. **"5,330 activities" is partly auto-generated.** ~4,578 are hand-authored objects (`brand:'a'`=1,523, `c`=1,059, `r`=1,479, `t`=517). The rest are produced at runtime by `buildVariants()` (TIER_TARGETS 250/500/700/500) that clones a seed and appends a variant word (Outdoor/Quiet/5-minute…). Many "activities" are near-duplicates. Do we (a) keep the generator, (b) replace generated filler with real authored activities, or (c) both?

---
# PHASE 1 — EVALUATION

## 1. Product
**What it does well**
- Genuinely deep, practitioner-voiced content. The hand-authored activities have specific materials, numbered steps and a real "tip" (e.g. leak-test a shelter, dress salad last minute). This is the moat.
- One-time price, no subscription — strong wedge vs TpT/Twinkl subscription fatigue.
- Breadth of *surfaces*: activity library + Mrs. Bailie AI planner + Supply Planner + UnboxED (material-based ideas) + weather-aware suggestions + printable SEL/AAC/ASL tools + reviews.
- Offline-first, installable PWA, Google-Translate multilingual.

**What's confusing**
- Brand architecture (see Discrepancy #1). Names ending "-ED" + ParentED + UnboxED + tiers (Starter/Pro/Elite/School) + 2 public prices ($67/$397) = too many overlapping taxonomies.
- Two pricing systems coexist: code `TIERS` still defines Starter $27 / Pro $97 / Elite $197 with activity caps (250/750/1500), while the public site sells only Vault $67 / School $397. Dead tier UI still ships.
- "Mina" vs "Mrs. Bailie": leftover `mina`/`MIna` identifiers suggest a rename that wasn't finished.

**What's missing vs TpT/Twinkl/GoNoodle/Scholastic**
- No true resource *types* beyond activities (no printable worksheets, lesson-plan templates at scale, visual schedules library, social stories, parent handouts) — only 5 one-off tools + 3 PDFs.
- No standards/curriculum alignment, no grade-level (K-5) framing (uses age bands only).
- No collections/curation by season, theme, or "sub plans", no ratings per item, weak discoverability of the 5k library.
- No account/sync (localStorage only) — a School buyer with 10 seats can't manage a team roster server-side.

## 2. Visual design (screenshots captured at 1440px and 390px)
- **Theme:** landing = light "paper" (#fffaf3) with neon gradient accents; app = dark-only (#0a0e1a) glassmorphism. The two halves feel like different products.
- **Type:** Fraunces (display) + Inter (body) — a competent, common pairing (Inter is on the design-slop list).
- **Module identity:** each module has palette vars but identity is thin — mostly a pill colour + emoji. No distinct background *treatment* per module as your brief wants.
- **Buttons:** ad-hoc. Many inline-styled one-offs; several button "systems" (`.sp-btn`, `.gate-*`, `.btn-*`, `.demo-btn`, `.filter-dd-btn`) with no shared tokens. No documented primary/secondary/tertiary/icon + full state matrix.
- **Icons:** emoji everywhere (against modern icon guidance; also inconsistent cross-platform + poor a11y).
- **Consistency:** low. Landing and app share almost no CSS.
- **Bug seen in screenshot:** a floating "Open the Dashboard · FREE DEMO" widget overlaps the pricing cards on desktop.

## 3. UX
- **Nav:** landing top-nav ok; app uses filter dropdowns (Module/Category/Age). Older age/material/picks rows are force-hidden (`display:none!important`) — leftover dead UI.
- **Search/filter:** functional client-side filter; but with 5k items and auto-generated near-dupes, results feel padded.
- **Cards:** lively (pop-in, energy colours) but heavy animation; min-height 180 with emoji hero.
- **Detail view / favourites / planner / collections / print:** all present and client-side; planner + collections + done-tracking live in localStorage.
- **Onboarding:** multi-step modal + email gate + demo timer (gamified paywall).
- **Mobile:** dense but works; your ≤720px "compact dropdown, no hamburger, solid bg" rule is partially met but not to spec.

## 4. Content inventory (measured)
- Hand-authored objects by module: **AdaptED 1,523 · RegulatED 1,479 · CampED 1,059 · TeenagED 517** (≈4,578).
- Hero breakdown displayed: AdaptED 1,420 · CampED 1,031 · TeenagED 503 · RegulatED 1,439 = **4,393** (doesn't equal the "5,330" headline → the remainder is generator + UnboxED + ParentED).
- Categories in `CATS`: AdaptED ~45, CampED 28, TeenagED ~10, RegulatED ~35+ (brief says "128 categories" — needs reconciliation with actual `CATS`).
- **Gaps:** grade-level (K/1/2…), curriculum/standards, worksheets & printables, social stories, visual schedules library, seasonal/holiday packs, group-size + indoor/outdoor are tags but not first-class browse axes, special-needs/regulation is one module not a cross-cutting filter, no "sub plan / 5-min filler" packs as products.
- Extra content: Play-to-Learn PDFs (3), ASL board (35+ signs), 4 AAC boards, RegulatED 4 interactive tools.

## 5. Technical
- **Structure:** `app.html` = 3.65 MB / 18,555 lines — ALL CSS+JS+data inline in one file. `index.html` 226 KB. No build step (per your rule, good), but no data separation (bad for perf/maintainability).
- **Performance:** entire 5k dataset + generators + heavy CSS animations parse on every load; `vault-demo.mp4` 3.6 MB. Cache headers set to no-store (forces re-download every visit). Slow on school laptops/phones.
- **Accessibility (WCAG AA): fails in several places.** Dark-only; many low-contrast greys (`rgba(255,255,255,0.25)`); emoji-as-icon with no labels; `alt=` count in app.html = 0; only 1 `prefers-reduced-motion` rule despite dozens of animations; focus states minimal (`:focus` ~9 rules, no consistent `:focus-visible`).
- **License / purchase flow — INSECURE (critical).** Unlock is fully client-side: `hmacVerify()` uses a hardcoded secret **`SECRET='LC2025ADAPTED'`** in the shipped file; plus a hardcoded owner key `LAZYCREATOR-OWNER-0F0D0006`, static `DEMO-*` keys, and a `?unlock=LAZYCREATOR-OWNER-0F0D0006` URL bypass. Anyone reading source can mint valid keys → the paywall is trivially bypassable. Gumroad `/v2/licenses/verify` is also called client-side. It "works" for honest buyers, but is not secure.
- **Placeholder values (leftover):** index.html footer script still contains `GUMROAD_BASE='https://tatianna.gumroad.com/l'`, `SLUGS: 'PASTE_STARTER_SLUG'…`, `CONTACT_EMAIL='PASTE_YOUR_EMAIL_HERE'`. Live buy buttons instead hardcode `thelazycreator.gumroad.com/l/kbylil` ($67) and `/muyuj` ($397), so the placeholder block is dead but shippable confusion.
- **Netlify function:** `netlify/functions/reviews.js` is a bundled Netlify-Blobs reviews store; app calls `/.netlify/functions/reviews`. Works only on Netlify (fine).
- **Broken/369 links, console:** no obvious 404s in static assets; icons/manifest referenced (`/icons/…`, `/manifest.json`) — verify they exist on deploy. Console logs a build banner only.
- **Git:** single-branch (`main`) auto-deploys via Netlify; `facelift` now exists locally.

## 6. Prioritized problems
**Critical**
- C1 Client-side license secret + owner-key + `?unlock` bypass (paywall not secure).
- C2 Single 3.65 MB `app.html`; no data separation; no-store cache → poor load on school hardware.
- C3 Brand/module story contradictory (4 vs 5 vs 6); "5,330" not equal to displayed sums.
- C4 Charity give-back program (8 partners) absent though it's a stated pillar.

**High**
- H1 WCAG AA failures (contrast, alt text, focus-visible, reduced-motion).
- H2 Auto-generated near-duplicate activities dilute quality/search.
- H3 No shared design system; landing vs app inconsistent; button chaos.
- H4 Leftover placeholders (SLUGS/EMAIL) and dead tier pricing in code.
- H5 Floating demo widget overlaps pricing on desktop.

**Medium**
- M1 Emoji-as-icon everywhere. M2 Mina→Mrs. Bailie rename unfinished. M3 Few real resource *types* vs competitors. M4 No grade/standards axis. M5 localStorage-only (no team/seat sync for School).

**Low**
- L1 Dead/hidden UI rows. L2 Duplicated CSS rules. L3 Minor copy inconsistencies.

---
# PHASE 2 — PLAN (no code yet)

## A. New design system (delivered first as `styleguide.html` in Phase 3)
- **Palette (AA-checked on both themes).** Neutral ink `#0F1D2E` on paper `#FBF7F0` = 13.4:1. Dark surface `#0B1220`, text `#EAF0F7` = 15:1. One brand accent per module, each with an AA-verified on-light and on-dark variant + a tint:
  - AdaptED — Marigold `#B8560E` (on-light 4.8:1) / `#F6A63C` (on-dark 8.2:1)
  - CampED — Pine `#1B5E3A` / `#4FD08A`
  - TeenagED — Indigo `#3D3AD1` / `#9FA8FF`
  - RegulatED — Clay `#9A3412` / `#FDBA74`
  - (ParentED / 5th–6th TBD pending Discrepancy #1)
  Body text always meets ≥4.5:1; large text ≥3:1; UI borders ≥3:1.
- **Typography:** display face swapped to something less "on-distribution" (candidates: *Bricolage Grotesque* or *Hanken Grotesk* for display, *Source Serif / Newsreader* for editorial), body kept highly legible. Type scale to your spec (H1 `text-4xl→6xl`, H2 `text-base→lg`, body `text-sm→base`).
- **Per-module background treatment:** each module a distinct, subtle, GPU-cheap static motif (AdaptED = soft paper grid; CampED = topographic contour lines; TeenagED = dotted circuitry; RegulatED = calm concentric waves), all respecting `prefers-reduced-motion`.
- **Button system:** tokens for primary / secondary / tertiary / icon-only, each with hover / focus-visible / active / disabled / loading. Pill + squared variants. Min 44px touch target. Visible 2px focus ring `#3D3AD1`/light equivalent.
- **Cards, badges, icons, motion:** unified `.card`, tier/new/"NEW" badges, **Lucide icon set replacing emoji** for UI chrome (emoji may remain as decorative activity glyphs only), a small motion spec (150–250ms, transform/opacity only).

## B. Landing page redesign (section order)
1. Sticky nav (desktop full; ≤720px solid compact dropdown, links wrapping, no hamburger).
2. Hero — one honest headline, one honest number (real curated count), a real demo card, primary CTA "Get the Vault — $67", secondary "Try the demo".
3. Mrs. Bailie in action — live scripted plan (keep her).
4. The modules — 1 panel per module using its background treatment.
5. Tatianna's authority — educator bio, 20+ yrs / YMCA (no invented stats).
6. Charity give-back — 8 partners (pending Discrepancy #2).
7. Pricing — Vault $67 / School $397 exactly as today (unchanged unless you approve).
8. Reviews (Netlify function) + FAQ + final CTA + footer (remove placeholders).

## C. App redesign
- Shell with sticky header, module switcher, and per-module background.
- Home/dashboard: continue, weekly picks, collections, Mrs. Bailie entry.
- Browse: left rail (desktop) / bottom-sheet (mobile) filters — Module, Category, Age **and new Grade**, Setting, Group, Energy, Duration, Resource-type, Special-needs, Season.
- Cards → detail drawer (materials, steps, adaptations, print, save, add-to-planner).
- Favourites, Planner, Print/Export, all preserved and restyled; dead hidden rows removed.
- Mobile nav to your ≤720px spec.

## D. Resource expansion ("many, many more")
Proposed additions (all marked `new:true` for Tatianna's review), targeting the gaps:
- **New authored activities to replace generator filler & fill gaps: +1,200** — AdaptED +400 (grade-banded K-5, STEM, outdoor), CampED +250 (rainy-day, waterfront, all-camp), TeenagED +250 (life-skills, digital citizenship, money), RegulatED +300 (co-regulation, sensory diets, transitions).
- **New resource TYPES (printables, original, no copyrighted content):**
  - Printable activity cards: 300 (auto-render from existing data → PDF/print CSS).
  - Lesson-plan templates: 40. Visual schedules: 60. Social stories: 80. Worksheets: 250. Parent handouts: 60. Regulation tools: 30 (extend RegulatED). Sign-language packs: 6. Seasonal/holiday packs: 12 curated bundles.
- **Batch plan:** author in JSON shards (`/data/adapted.json`, etc.), 100–200/batch, each item `new:true` + `source:'authored'`; you approve a sample batch before scale. 10 sample activities in exact format are in `facelift-sample-activities.js`.

## E. Technical plan (stay static on Netlify)
- Split data out of `app.html` into `/data/*.json` per module, lazy-load per active module (fetch on demand). Keeps it static, no build step.
- Print/export via print-CSS + client PDF (existing pattern).
- Move license secret OFF the client: verify via a Netlify function (like `reviews.js`) that holds the HMAC secret in an env var, so the shipped HTML no longer contains the secret or owner key. **Existing keys keep working** (same algorithm, server-side). This is the only justified new server piece and I'll only add it if you approve.
- Cache: version assets + allow normal caching (drop no-store) for fast repeat loads.
- A11y pass: contrast tokens, `alt`, `:focus-visible`, `prefers-reduced-motion`, keyboard order.

## F. Phased roadmap
- **P0 Foundations (small):** `styleguide.html` design system + tokens. No app behavior change.
- **P1 Landing redesign (medium).** Ship new index.html; keep Gumroad links, Mrs. Bailie, pricing; add charity section; remove placeholders.
- **P2 App shell + data extraction (large).** Split data to JSON, restyle shell/nav/filters/cards/detail; preserve license/planner/favourites/print. Optional secure-license function (needs approval).
- **P3 A11y + performance hardening (medium).**
- **P4 Resource expansion (large, batched).** Authored activities + new resource types, all `new:true`, in approved batches with move-mapping (all 5,330 existing items preserved).

STOP — awaiting your approval and answers to the 3 discrepancies.
