# Agent.md — ELORIA Premium E‑Commerce Design System

> This document is the single source of truth for the ELORIA project. It is written for an AI engineer who will maintain and extend this codebase for years. Read it fully before modifying anything. When in doubt, preserve the existing design language rather than inventing.

> Project-local rule: `Design/design1.html` must always be used as the canonical visual and structural resource before changing or extending the site. Do not design outside that reference or the ELORIA rules in this document.

---

## 1. Project Overview

### What this product is
ELORIA is a **premium, Persian‑language (RTL), imported‑cosmetics e‑commerce experience**. It sells makeup, skincare, hair care, beauty tools, fragrance, and accessories — curated "imported luxury" positioning comparable to Sephora, Dior Beauty, Charlotte Tilbury, Rare Beauty, Rhode, and Chanel Beauty.

The product is a **multi‑page storefront** made of self‑contained Design Components (`.dc.html`), sharing one visual language and one persisted commerce state:

- `Eloria - Landing.dc.html` — the cinematic home page (18 conceptual sections).
- `Eloria - Category.dc.html` — shop / listing grid with filters + sort.
- `Eloria - Product.dc.html` — product detail page.
- `Eloria - Checkout.dc.html` — 4‑step cart → shipping → payment → confirmation flow.
- `Eloria - Wishlist.dc.html` — saved favourites.
- `Eloria - Account.dc.html` — account / profile.
- `eloria-data.js` — the shared product catalogue, currency/number helpers, and cart/wishlist persistence.
- `Eloria Landing - Standalone.html` — a bundled, offline, single‑file export of the landing page (generated artifact — never hand‑edit).

### Design philosophy
- **Editorial luxury, not template retail.** Generous whitespace, restrained colour, large confident typography, cinematic imagery. Every element must earn its place; when a section feels empty, solve it with composition, not filler.
- **Motion as narrative.** The hero literally tells a story — a bare complexion becomes a full couture look as the user scrolls, driven by a scroll‑scrubbed video. Motion is premium, eased, and never harsh or cartoonish ("Apple‑quality").
- **One coherent system across pages.** Shared tokens, shared components, shared state. A user should never feel they left the brand.

### UX goals
- Make discovery effortless and shopping fast (search, categories, filters, quick add).
- Build trust (verified reviews, authenticity messaging, secure‑checkout cues, real product photography).
- Optimise for conversion without feeling pushy — primary CTAs are unmistakable; secondary actions recede.

### Brand personality
Elegant · Luxury · Modern · Minimal · Trustworthy · Premium · **Feminine without being decorative.**
Explicitly avoided: flowers, cute illustrations, cheap beauty clichés, pink overload, generic cosmetic‑template aesthetics.

---

## 2. Visual Language

### Colour palette
| Role | Token | Value | Usage |
|---|---|---|---|
| Brand primary | `--primary` | `#7850cd` | CTAs, links, accents, badges, active states, focus |
| Primary light | `--primary-2` | `#9a7ce6` | Gradient partner, hover glows |
| Primary soft | `--primary-soft` | `#f1ecfa` | Tinted fills, selected chips |
| Page background | page literal | `#f2ecfb` | **Uniform light‑lilac** applied to every section + hero |
| Card / surface base | `--bg` | `#ffffff` | Cards, drawers, inputs, elevated surfaces |
| Neutral surface | `--surface` | `#f6f5f8` | Image placeholders, muted fills |
| Ink (text) | `--ink` | `#151319` | Primary text |
| Muted text | `--muted` | `#726d7a` | Secondary text, captions, labels |
| Hairline | `--line` | `#eae7ef` | Borders, dividers |
| Footer gradient | literal | `#7850cd → #5b3aa8 → #4a2d8f` | Footer + consultation panel |
| Rating star | literal | `#e0a13a` | Star glyphs |
| Success | literal | `#2f8f5b` | "Verified", free‑shipping, success ticks |
| Error | literal | `#d6406c` | Form validation errors |

**Deep‑plum ink accents** used on the pastel hero: `#2b2038` / `#2b2044` (logo + nav text over light backgrounds).

> **Rule:** `#f2ecfb` is the canonical page background across the entire product. Cards stay white for contrast. Do not introduce a second page background.

### Direction themes (A / B)
The landing page supports two art directions swapped at runtime via CSS custom properties in `applyDir()`:
- **A · Minimal:** `--bg #ffffff`, `--surface #f6f5f8`, `--ink #151319`, `--radius 16px`.
- **B · Editorial:** `--bg #f6f3ef`, `--surface #efe9f4`, `--ink #1a1622`, `--radius 6px`.
Both currently use IRANSans; the switcher is a code‑level capability, not always surfaced in the UI.

### Typography
- **Primary typeface: `IRANSans`** (self‑hosted `@font-face`, weights 300/400/500/700/800 via jsDelivr CDN).
- **Fallback: `Vazirmatn`** (Google Fonts) → then `sans-serif`. Fallback exists so Persian never breaks if the CDN fails.
- Both `--font-display` and `--font-body` resolve to `'IRANSans','Vazirmatn',sans-serif`.
- `--dw` (display weight) = `700`; `--dt` (display tracking) = `0` — **Persian script must not use positive letter‑spacing.**

### Font scale (observed, `clamp()`‑fluid)
| Element | Size |
|---|---|
| Hero H1 | `clamp(32px, 4.8vw, 66px)` |
| Section H2 | `clamp(30px, 4.4vw, 58px)` |
| Sub‑headline H3 | `clamp(24px, 2.8vw, 36px)` |
| Card title | `16–16.5px` |
| Body | `15–17px`, line‑height `1.9–2.0` for Persian legibility |
| Eyebrow label | `13px`, weight `600`, colour `--primary` |
| Caption / meta | `11–12.5px`, colour `--muted` |
| Badge | `9–11px`, weight `600–700` |

### Border radius
`--radius: 16px` (A) / `6px` (B). Derived radii use `calc(var(--radius) + N)`:
- Cards: `var(--radius)`
- Large panels / feature cards: `calc(var(--radius) + 4px)` … `+8px`
- Hero video frame / consultation panel: `calc(var(--radius) + 12px)`
- Pills / chips / round buttons: `30–40px` or `50%`
- Badges: `8px` / `20px`

### Shadows & elevation
A restrained, tinted‑shadow system (plum‑tinted, never neutral grey):
- Card hover: `0 24px 56px -32px rgba(60,30,90,.45)`
- Feature imagery: `0 40px 80px -40px rgba(20,10,40,.35)` / `0 44px 90px -42px rgba(60,30,90,.5)`
- Primary CTA: `0 14px 34px -14px rgba(120,80,205,.75)`
- Soft CTA lift: `0 12px 30px -12px rgba(120,80,205,.7)`
- Drawers: `±30px 0 80px -30px rgba(20,10,40,.4)`
Elevation is expressed **almost entirely through shadow spread + blur**, not borders. Cards rest flat (`1px solid --line`) and lift on hover.

### Gradients
- **Brand gradient:** `linear-gradient(120deg,#7850cd,#9a7ce6)` (buttons, avatars, tick badges).
- **Footer / consultation:** `linear-gradient(165deg,#7850cd,#5b3aa8,#4a2d8f)`.
- **Hero atmosphere:** layered `radial-gradient` bloom in lilac/blush tones for a soft pastel key light.
- **Category / collection tiles:** each has a bespoke dual‑tone `radial-gradient(120% 90% at 30% 20%, …)` behind the photo, tuned per category (rose, sage, caramel, lilac, amber, mauve).

### Glassmorphism
Used sparingly and intentionally: `background: rgba(255,255,255,.7–.94); backdrop-filter: blur(6–16px)`. Applied to the scrolled nav, quick‑view button, category tag chips, wishlist buttons, and the search overlay. **No neumorphism anywhere.**

### Image style
Real editorial product/beauty photography (SHEGLAM catalogue), soft lighting, luxury feel. Placeholders use the striped `repeating-linear-gradient` treatment until real assets arrive. Images fill via `object-fit: cover` in fixed aspect‑ratio frames.

### Icon style
**Untitled‑UI line icons**, uniform spec: `viewBox="0 0 24 24"`, `fill="none"`, `stroke="currentColor"`, `stroke-width="2"`, `stroke-linecap="round"`, `stroke-linejoin="round"`. Rendered at 15–24px. Icons inherit colour via `currentColor`. **Never mix icon families or stroke widths.**

### Illustration style
No character/spot illustrations. "Graphics" are abstract: gradient tiles, a stylised CSS map (grid + glowing pin) in the store section, and soft radial blooms. Emoji are not used.

---

## 3. Layout System

- **Direction:** `dir="rtl" lang="fa"` on every root. All layout logic is RTL‑first (drawers slide from the **left**, badges sit top‑left, nav underline animates right→left).
- **Grid:** CSS Grid for card collections — `repeat(auto-fill, minmax(240–280px, 1fr))` for products, `repeat(auto-fit, minmax(300px, 1fr))` for feature pairs. Flexbox for rows, nav, toolbars, chips.
- **Max widths / containers:** `1360px` (product grids, footer, wide sections), `1320px` (category page), `1280px` (before/after, stores), `1200px` (reviews, blog, instagram), `1100px` (consultation), `760px` (newsletter), `640px` (centred section intros).
- **Horizontal padding:** `clamp(20px, 4vw, 56px)` — the universal gutter.
- **Section vertical rhythm:** `clamp(60–80px, 7–10vw, 110–150px)` top/bottom. Larger for hero‑adjacent and emotional sections, tighter for dense grids.
- **Section intro block:** centred, `max-width:640px`, `margin:0 auto clamp(40px,5vw,64px)` — eyebrow (13px `--primary` 600) → H2 → muted paragraph.
- **Vertical rhythm within cards:** `16px` padding baseline, `10–14px` gaps.
- **Responsive:** fully fluid via `clamp()` + `auto-fill/auto-fit` grids; no hard breakpoints are hard‑coded — the layout reflows continuously. Mobile‑first hit targets ≥ 38–44px.
- **Alignment:** section intros centred; content grids start‑aligned; carousels centre when they fit, fall back to scroll when they overflow.

---

## 4. Design Tokens

### Colours
`--bg #ffffff` · `--surface #f6f5f8` · `--ink #151319` · `--muted #726d7a` · `--line #eae7ef` · `--primary #7850cd` · `--primary-2 #9a7ce6` · `--primary-soft #f1ecfa` · page `#f2ecfb` · footer stops `#7850cd/#5b3aa8/#4a2d8f` · star `#e0a13a` · success `#2f8f5b` · error `#d6406c`.

### Typography tokens
`--font-display` / `--font-body` = `'IRANSans','Vazirmatn',sans-serif` · `--dw` (700) · `--dt` (0). Weights in use: 300, 400, 500, 600, 700, 800.

### Spacing scale (px)
`2, 3, 4, 5, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 30, 34, 40, 44, 48, 56, 64, 72`. Fluid section spacing via `clamp()`. **Do not introduce off‑scale values.**

### Radius
`--radius` (16 / 6) · derived `calc(--radius + 4/8/12)` · pills `30–40px` · circle `50%` · badge `8px`.

### Shadow tokens
See §2. Canonical set: card‑hover, feature‑image, primary‑CTA, soft‑CTA, drawer.

### Animation duration
`.2s` (micro / chips) · `.25s` (buttons) · `.3s` (hover scale, colour) · `.35s` (overlay fade) · `.4s` (quick‑view reveal, cart overlay) · `.5s` (nav state, drawer slide) · `.6s` (magic‑tile flex‑grow) · `.7s` (image zoom) · `.8s` (thumb scale) · `1s` (scroll reveal) · marquee `32s linear infinite`.

### Animation easing
- Signature easing: **`cubic-bezier(.2,.7,.2,1)`** (reveals, image zoom, nav underline, magic tiles).
- Drawers: `cubic-bezier(.5,.05,.2,1)`.
- Generic: `ease`.

### Opacity
Text tiers: `.5/.55` (de‑emphasised marks), `.6` (footer meta), `.7–.75` (secondary on dark), `.82` (footer body), `.9–.96` (near‑solid on gradient). Hover glows `.1–.35`.

### Blur
`backdrop-filter: blur(6px)` (chips, buttons), `blur(14–16px)` (nav, search overlay). Decorative bloom blur `24–48px`.

### Border
`1px solid var(--line)` default; `1.5px solid var(--primary)` for selected filters/options; `2px` icon strokes.

### Z‑index
`60` nav · `80` (legacy direction switcher, removed) · `90` cart overlay · `91` cart drawer · `95` search overlay. Keep new overlays within/around these bands; never exceed `95` without reason.

### Naming conventions
- CSS classes prefixed **`el-`**: `el-card`, `el-thumb`, `el-thumb-inner`, `el-quick`, `el-hoverbar`, `el-navlink`, `el-reveal`, `el-scroll`, `el-magic`, `el-field`.
- Keyframes prefixed **`el-`**: `el-marquee`, `el-float`, `el-cue`.
- Section ids are semantic English: `categories`, `bestsellers`, `new`, `brands`, `reviews`, `ba`, `collections`, `instagram`, `blog`, `consult`, `stores`, `newsletter`.
- `data-screen-label` on every top‑level section/screen for comment/inspection context.
- Persian is the display language; identifiers/ids/handlers stay English.

---

## 5. Components

> All components are authored inline (inline styles, `el-` helper classes for pseudo‑states) inside single `.dc.html` Design Components. There is **no external CSS file** and no CSS framework. Logic lives in a `class Component extends DCLogic` block; template holes are `{{ dottedPath }}`; control flow is `<sc-for>` / `<sc-if>`.

### 5.1 Navigation bar
- **Purpose:** persistent brand + primary nav + utility icons (search, wishlist, account, cart).
- **Variants:** transparent‑over‑hero (landing) vs. solid sticky (inner pages).
- **States:** at‑top (transparent, deep‑plum text) → scrolled (`rgba(255,255,255,.86)` + `blur(16px)` + hairline border, tighter padding); transitions over `.5s`.
- **Behaviour:** on landing, `updateScroll()` toggles background/blur/colour when `scrollY > 72vh`. Cart & wishlist show Persian‑digit count badges (top‑left, RTL).
- **Accessibility:** every icon button has an `aria-label` (Persian). Targets ≥ 28px, padded to comfortable size.
- **Future:** mobile hamburger + slide‑in menu; mega‑menu for categories.

### 5.2 Product card (`.el-card`)
- **Purpose:** the atomic commerce unit (best‑sellers, new arrivals, category grid, search results).
- **Anatomy:** image frame (aspect `1/1` or `4/5`) → brand eyebrow → 2‑line clamped title → rating row → price + Add button.
- **States:** rest (flat, `1px --line`) → hover (shadow lift, image `scale`, quick‑view slides up, hover‑bar CTA appears). Wishlist heart toggles fill (`--primary`) ↔ outline.
- **Rules:** titles use `-webkit-line-clamp:2` + `min-height` so price/CTA rows align across a row; cards in a flex track use `align-items:stretch` and `flex-direction:column` with `margin-top:auto` on the price row.
- **Accessibility:** heart & add buttons carry `aria-label`; whole card image links to the product page.
- **Future:** shade‑swatch quick‑select, "out of stock" and discount variants.

### 5.3 Category tile
- **Purpose:** visual entry into a department.
- **Anatomy:** bespoke gradient + photo (`el-thumb-inner`), soft light bloom, dark gradient scrim, glass tag chip (top), name + count (bottom).
- **Interaction:** `el-thumb-inner` scales `1.09` on hover over `.8s`.

### 5.4 "Magic products" tile (`.el-magic`)
- **Purpose:** expressive category row (صورت / لب / ابرو / چشم).
- **Behaviour:** `flex:1 1 180px`; on hover `flex-grow:2.6` over `.6s` `cubic-bezier(.2,.7,.2,1)` — the hovered tile widens while siblings recede.

### 5.5 Buttons
- **Primary CTA:** `background: var(--primary)`, white text, radius `12–40px`, weight 600, plum shadow, `translateY(-2px)` on hover. **All primary CTAs across the product are brand‑purple** (Add to cart, Add, Shop‑the‑look, Subscribe, Checkout, Request consultation, View all).
- **Dark CTA:** `background: var(--ink)` (used on some "Add" buttons) → hover `background: var(--primary)`.
- **Secondary / ghost:** `background: var(--bg)`, `1px --line`, ink text (e.g. "Continue shopping", "Browse routine").
- **Icon button:** transparent, `currentColor`, `place-items:center`, circular hover to `--primary` fill.
- **States:** hover (lift/colour), focus (should show visible ring — see Accessibility gap), disabled (not yet defined — add reduced‑opacity when needed).

### 5.6 Cart drawer
- **Purpose:** slide‑in bag from the **left** (RTL). Overlay `rgba(12,8,18,.5)` + `blur(3px)`.
- **Anatomy:** header w/ Persian count → scrollable line items (image, brand, name, qty stepper, remove, line total) → sticky footer (subtotal, free shipping, checkout link).
- **States:** empty (illustdifferent message + "continue shopping") vs. populated. Transform `translateX` over `.5s cubic-bezier(.5,.05,.2,1)`.
- **Persistence:** reads/writes `eloria_cart` via `eloria-data.js`.

### 5.7 Search overlay
- **Purpose:** full‑screen live search (`z-index:95`), lilac glass background.
- **Behaviour:** typing filters `PRODUCTS` by name+brand live; empty state shows popular‑search chips; no‑results state shows a message; results link to product pages. Opens focused; closes via ✕.

### 5.8 Forms & inputs
- **Floating‑label field (`.el-field`):** label animates up/shrinks on focus or filled (`:not(:placeholder-shown)`); border → `--primary` on focus; radius `12–14px`; RTL right‑aligned text.
- **Validation:** inline, error colour `#d6406c`, success message in `--primary`; email regex `^[^\s@]+@[^\s@]+\.[^\s@]+$`.
- **Select/textarea:** same surface, radius, focus treatment.

### 5.9 Stepper (checkout)
- **Purpose:** 4‑step progress (سبد → ارسال → پرداخت → تأیید) with numbered circles, connecting bars, active/done colouring in `--primary`.

### 5.10 Review card, blog card, testimonial, marquee, before/after slider
- **Review card:** stars, quote, avatar (gradient initial), verified tick.
- **Blog card:** image/gradient header, tag chip, title, read‑time + "ادامه ←".
- **Brand marquee:** infinite `el-marquee` strip of LTR wordmarks (`dir="ltr"` spans; real logos require uploaded assets — **do not fabricate brand logos**).
- **Before/After:** draggable `range` input clipping an "after" layer via `clip-path: inset()`.

---

## 6. Interaction System

- **Hover:** shadow lift + slight `translateY(-2px)` on CTAs; image `scale(1.06–1.09)`; quick‑view & hover‑bar reveal (opacity + translateY); nav underline grows right→left; magic‑tile flex expansion; icon buttons fill `--primary`.
- **Focus:** inputs drop their outline and colour the border `--primary`. **Gap:** custom focus‑visible rings for buttons/links are not yet implemented — add them (see Accessibility).
- **Active:** qty steppers, filter chips, sort toggles switch to `--primary` fill immediately.
- **Transitions:** colour/background `.3s ease`; hover reveals `.4s`; nav/drawer `.5s`; image `.7–.8s`; reveals `1s`; all premium eases, never linear except the marquee.
- **Scroll animations:**
  - `el-reveal` — elements start `opacity:0; translateY(30px)` and animate to rest via a scroll‑position check (`_revealCheck`) that adds `.in` + inline styles when within `88–90%` of viewport. A `2.5s` safety timer force‑reveals everything.
  - **Hero video scrub** — a `requestAnimationFrame` loop (`vloop`) reads hero scroll progress each frame and sets `video.currentTime` directly (one seek per frame, skipped while `video.seeking`). This decouples seeking from scroll events for a lag‑free feel. Hero is `600vh` tall/pinned so makeup completes exactly at 100% scroll. Video is warmed up (muted play→pause) so frames buffer and become seekable.
  - Nav transparency toggle past `72vh`.
- **Micro‑interactions:** count badges, scroll cue arrow (`el-cue`), marquee, floating‑label motion.
- **Loading:** striped placeholder fills; `loading="lazy"` on imagery; scroll‑reveal doubles as progressive entrance.
- **Cursor:** `pointer` on all interactive elements; `ew-resize` on the before/after slider.

---

## 7. Page Structure

**Landing section order (top → bottom):**
1. Nav (fixed) → 2. Hero (scroll‑scrubbed video + headline + logo, `600vh`) → 3. Featured categories → 4. Magic products (expanding tiles) → 5. Promo poster → 6. Best sellers → 7. New arrivals (carousel + "view all" + prev/next) → 8. Brand marquee → 9. Customer reviews → 10. Before/After → 11. Collections (skincare/haircare) → 12. Instagram gallery → 13. Beauty‑tips blog → 14. Consultation form → 15. Store location → 16. Newsletter → 17. Footer → (search overlay + cart drawer mounted at end).

**Hierarchy rules:** every content section opens with the centred eyebrow→H2→paragraph intro, then its grid/carousel. CTAs anchor the emotional sections (before/after, consultation, newsletter). Footer is the plum‑gradient anchor with logo, link columns, client care, and legal row.

---

## 8. UX Principles

- **Primary journey:** land → be drawn in by the hero transformation → browse categories/best‑sellers → search or filter → product page → add to cart → checkout (4 steps) → confirmation. Wishlist and account support return visits.
- **Content hierarchy:** imagery first, then product identity (brand → name → price → proof), then action. Eyebrow labels orient; H2s anchor; muted paragraphs support.
- **Attention flow:** whitespace funnels the eye; a single purple CTA per card/section is the focal point; secondary actions are ghost‑styled.
- **Conversion strategy:** low‑friction add‑to‑cart everywhere, persistent cart across pages, free‑shipping and secure‑payment cues, verified reviews, quick view, consultation lead‑gen.
- **Visual storytelling:** the hero makeup reveal is the brand thesis in motion; before/after reinforces transformation; collections and blog build lifestyle context.
- **Emotional design:** soft lilac calm, tinted shadows, eased motion → aspirational, trustworthy, feminine‑not‑girly.

---

## 9. Frontend Architecture

- **Format:** each page is a **Design Component** `Name.dc.html` — a full HTML document wrapping `<x-dc>` with a `<helmet>` (fonts, `@font-face`, keyframes, `el-` pseudo‑state classes), an inline‑styled template, and a `class Component extends DCLogic` logic block. Runtime is `support.js` (never edit).
- **Templating:** holes are dotted lookups only (`{{ p.name }}`), computed in `renderVals()`. Control flow via `<sc-for list as>` and `<sc-if value>` with `hint-*` placeholders. Handlers are whole‑value attrs (`onClick="{{ fn }}"`).
- **Styling strategy:** **inline styles only**, driven by CSS custom properties on the root for theming. Pseudo‑states use `style-hover` / `style-focus` (compiled) or `el-` classes in `<helmet>`. No stylesheets, no utility classes, no Tailwind.
- **State management:** React‑class state inside each DC (`this.state`, `setState`, `renderVals`). Cross‑page state (cart, wishlist) is persisted in `localStorage` under `eloria_cart` / `eloria_wishlist` and shared through the `eloria-data.js` ES module (`readCart/writeCart/readWish/writeWish`, `PRODUCTS`, `PRODUCT_MAP`, `faDigits`, `money`).
- **Animation libraries:** none — all motion is CSS transitions/keyframes + a hand‑written `requestAnimationFrame` loop for the hero scrub. Do not add GSAP/Framer.
- **Performance:** `loading="lazy"` imagery, `object-fit` framing, `overflow-x:clip` (not `hidden`, to preserve `position:sticky`), rAF‑throttled scroll work, `font-display:swap`, video warm‑up + single‑seek‑per‑frame throttling.
- **Reusable utilities:** `faDigits` (Latin→Persian digits), `money` (Toman formatting with Persian digits + `٬` separator), `_res()` (maps a filename to the inlined blob URL from `window.__resources` for offline bundles).
- **Folder structure:** flat project root — page DCs, shared `eloria-data.js`, image assets, `eloria-logo.svg`, `hero-makeup.mp4`, generated `Eloria Landing - Standalone.html`, and `uploads/`.

### Offline bundling
Dynamic images (resolved through template holes) are registered with `<meta name="ext-resource-dependency" content="file" data-resource-id="file">` in the `<helmet>` and passed through `_res()` so the standalone bundler can inline them. The bundle also needs a `<template id="__bundler_thumbnail">` splash. The literal `{{ c.img }}` "asset not found" warnings during bundling are expected (they are template holes, not files).

---

## 10. Accessibility

- **Contrast:** ink `#151319` on `#f2ecfb`/white is strong; `--primary #7850cd` white text passes for large/bold. **Audit** `--muted #726d7a` on lilac for small text, and white‑on‑gradient at low opacity.
- **Semantics:** `nav`, `section`, `footer`, `form`, headings present; every section has `data-screen-label`. Improve by ensuring one logical `h1` per page and ordered headings.
- **Keyboard:** links/buttons are native and focusable. **Gap:** add visible `:focus-visible` rings (e.g. `2px` `--primary` outline/offset) to all buttons, icon buttons, chips, and the before/after slider.
- **ARIA:** icon buttons have Persian `aria-label`s — maintain this for every new icon control. Add `aria-live` to cart count and search results; `aria-expanded` to the future mobile menu.
- **RTL:** `dir="rtl" lang="fa"` is mandatory; Latin wordmarks/phone/email use `dir="ltr"` locally.
- **Reduced motion:** **Not yet implemented.** Add `@media (prefers-reduced-motion: reduce)` to disable the hero scrub auto‑motion, marquee, and reveal transforms, falling back to immediate visibility.

---

## 11. Performance

- **Images:** serve responsive sizes/`srcset`; keep `loading="lazy"`; prefer WebP/AVIF (already partly used); compress hero/blog PNGs.
- **Code splitting:** each page is already an independent DC; keep shared logic in `eloria-data.js`.
- **Animation:** continue rAF‑throttling scroll; animate only `transform`/`opacity`; avoid layout‑thrashing properties.
- **Lazy loading:** defer below‑the‑fold imagery and the hero video's heavy buffering appropriately; consider poster frames.
- **Rendering:** inline styles paint immediately (streaming‑friendly); keep it that way. Avoid class‑based "base styles" that delay first paint.
- **A11y improvements** double as quality: focus rings, reduced motion, contrast fixes.

---

## 12. Future Guidelines

### DOs
- Reuse `--` tokens and `el-` classes.
- Keep every page on the `#f2ecfb` background with white cards.
- Use the Untitled‑UI icon spec for all new icons.
- Keep Persian RTL correctness (drawers/badges on the left, `dir="ltr"` for Latin runs).
- Route new commerce state through `eloria-data.js` + `localStorage`.
- Format money with `money()` and numbers with `faDigits()`.
- Give every icon control an `aria-label`; every new section a `data-screen-label`.
- Register dynamic images with `ext-resource-dependency` meta + `_res()` for offline bundling.

### DON'Ts
- Don't introduce new colours outside the tokens.
- Don't change the spacing scale or add off‑scale values.
- Don't add CSS frameworks, external stylesheets, or animation libraries.
- Don't use `overflow-x:hidden` on scroll roots (breaks sticky) — use `overflow-x:clip`.
- Don't fabricate real brand logos — require uploaded assets.
- Don't add emoji, flowers, cute illustrations, or pink‑overload.
- Don't make motion harsh/linear (except the marquee) or exceed the established durations.

### Component creation rules
Reuse before creating. New components must: use tokens, follow the `el-` naming, be inline‑styled, expose data via `renderVals()`, include `hint-*` on control‑flow, carry `aria-label`s, and be RTL‑correct.

### Spacing rules
Section padding `clamp(60–80, 7–10vw, 110–150)px`; universal gutter `clamp(20px,4vw,56px)`; card padding `16px`; intro block `max-width:640px`, centred.

### Colour rules
`--primary` for all primary CTAs, links, active states, focus. Ghost for secondary. Success `#2f8f5b`, error `#d6406c`, star `#e0a13a` are the only status colours.

### Animation rules
Signature ease `cubic-bezier(.2,.7,.2,1)`; drawers `cubic-bezier(.5,.05,.2,1)`. Reuse the duration ladder in §4. Animate `transform`/`opacity` only.

### Typography rules
IRANSans → Vazirmatn → sans‑serif; `--dt` must stay `0` (no positive tracking on Persian); H2 `clamp(30px,4.4vw,58px)`; body line‑height `1.9–2.0`.

### Responsive rules
Fluid‑first with `clamp()` + `auto-fit/auto-fill`; no fixed breakpoints; hit targets ≥ 44px; carousels centre when they fit and scroll when they overflow.

### Naming conventions
`el-` for classes/keyframes; English semantic ids; Persian for display copy only; `data-screen-label` on screens/sections.

---

## 13. Technical Stack & Backend Architecture

> This section defines the **target production stack**. The current repository is a high‑fidelity front‑of‑house prototype built as inline‑styled Design Components with `localStorage` state; the guidance below is the intended migration path to a real, database‑backed application. When building production features, follow this architecture.

### 13.1 Stack summary
| Layer | Technology |
|---|---|
| Frontend framework | **Nuxt.js (Vue 3, SSR/SSG)** |
| Backend / API | Nuxt server routes (Nitro) — or a dedicated Node/NestJS service |
| Database | **MySQL** |
| ORM / query layer | Prisma (recommended) or Drizzle |
| Auth | Session/JWT‑based **user login** (see §13.4) |
| Assets | Existing images/video served via CDN / `public/` |

### 13.2 Frontend — Nuxt.js
- The storefront is (re)built in **Nuxt.js** using Vue 3 single‑file components and file‑based routing under `pages/`.
- **Pages map directly to the current DCs:** `pages/index.vue` (Landing), `pages/shop.vue` (Category), `pages/product/[id].vue` (Product), `pages/checkout.vue`, `pages/wishlist.vue`, `pages/account.vue`.
- **Rendering:** use SSR for SEO‑critical, data‑driven pages (product, category, landing) and prerender static marketing sections where possible. Keep the hero video scrub and scroll reveals as client‑only components (`<ClientOnly>`).
- **Design fidelity is preserved:** port the exact tokens from §2/§4 into a global stylesheet or Nuxt app config (`:root` custom properties), and rebuild the `el-` components as Vue components (`ProductCard.vue`, `CartDrawer.vue`, `SearchOverlay.vue`, `Field.vue`, etc.). The visual language, RTL rules, IRANSans typography, and motion system **must not change** — only the implementation moves from DC templates to Vue SFCs.
- **State:** replace ad‑hoc component state + `localStorage` with **Pinia** stores (`useCartStore`, `useWishlistStore`, `useAuthStore`). The cart/wishlist still persist locally for guests, but sync to the server for logged‑in users.
- **Data fetching:** use `useFetch` / `useAsyncData` against the API routes in §13.5. Keep `faDigits()` and `money()` as shared composables/utils.
- **i18n / RTL:** keep `dir="rtl" lang="fa"` at the app root (Nuxt `app.vue` / `@nuxtjs/i18n`), with `dir="ltr"` locally for Latin/numeric runs.

### 13.3 Database — MySQL
- **MySQL** is the system of record for products, users, carts, orders, reviews, and content.
- Access MySQL through an ORM (**Prisma** recommended) — never raw string‑concatenated SQL. Use parameterised queries/migrations, versioned in the repo.
- **Core schema (minimum viable):**
  - `users` — `id`, `email` (unique), `password_hash`, `first_name`, `last_name`, `phone`, `role` (`customer`/`admin`), `created_at`.
  - `products` — `id`, `brand`, `name`, `slug`, `description`, `price` (store integer minor units), `category`, `rating`, `reviews_count`, `image`, `badge`, `stock`, `is_active`. Mirrors the shape in `eloria-data.js` (`PRODUCTS`).
  - `product_variants` — `id`, `product_id`, `shade`/`swatch`, `stock` (for colour variants).
  - `categories` — `id`, `name_fa`, `slug`, `image`, `product_count`.
  - `carts` / `cart_items` — persisted server‑side cart per user; `cart_items` has `cart_id`, `product_id`, `qty`, unit price snapshot.
  - `wishlists` / `wishlist_items` — per‑user saved products.
  - `orders` / `order_items` — `orders`: `id`, `user_id`, `status`, `subtotal`, `shipping_cost`, `total`, `shipping_address`, `shipping_method`, `tracking_code`, `created_at`; `order_items`: line snapshots.
  - `reviews` — `id`, `product_id`, `user_id`, `rating`, `body`, `is_verified`, `created_at`.
  - `blog_posts` — `id`, `tag`, `title`, `body`, `read_minutes`, `image`.
- **Money:** store as integer minor units (e.g. Toman) in the DB; format for display with `money()`. Never store formatted strings.
- **Migrations & seeds:** every schema change ships as a migration; seed the catalogue from the current `eloria-data.js` data.

### 13.4 User authentication (login)
- Users can **register and log in** (email + password). This powers the account page, saved wishlist/cart, order history, and reviews.
- **Password storage:** hash with **bcrypt/argon2** — never store plaintext. Enforce a sane password policy.
- **Sessions:** issue an httpOnly, Secure, SameSite cookie session (or short‑lived JWT + refresh token). Store server sessions or a `sessions` table if not using stateless JWT.
- **Auth flows / pages:**
  - `pages/login.vue`, `pages/register.vue`, `pages/account.vue` (protected), password‑reset flow.
  - Reuse the existing **floating‑label field (`.el-field`)**, primary purple CTA, and inline validation styling — the auth UI must match the design system exactly (lilac background, white card, IRANSans, RTL).
- **Route protection:** a Nuxt **middleware** (`auth.ts`) guards `account`, `checkout` (for saved details), `wishlist` sync, and any admin routes; redirects guests to `login` with a return URL.
- **Guest → user merge:** on login, merge the guest `localStorage` cart/wishlist into the user's server‑side records.
- **Security:** CSRF protection on mutations, rate‑limit login/register, validate + sanitise all input server‑side, HTTPS only, and follow OWASP basics. Do not expose password hashes or internal ids unnecessarily.

### 13.5 API layer
- Expose REST (or a typed layer) via Nuxt server routes under `server/api/`:
  - `auth`: `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/logout`, `GET /api/auth/me`, password‑reset endpoints.
  - `products`: `GET /api/products` (filter/sort/paginate), `GET /api/products/:id`.
  - `cart`: `GET/POST/PATCH/DELETE /api/cart` (server cart for logged‑in users).
  - `wishlist`: `GET/POST/DELETE /api/wishlist`.
  - `orders`: `POST /api/orders` (checkout), `GET /api/orders` (history), `GET /api/orders/:id`.
  - `reviews`: `GET /api/products/:id/reviews`, `POST /api/reviews` (auth required).
- All handlers validate input, enforce auth where required, and return typed JSON. Keep the Persian number/currency formatting on the **frontend**, not in the API (API returns raw integers).

### 13.6 Migration notes (prototype → production)
- Replace `eloria-data.js` `PRODUCTS`/`PRODUCT_MAP` with API calls backed by the `products` table (keep the same object shape to minimise churn).
- Replace `readCart/writeCart/readWish/writeWish` (`localStorage`) with Pinia + server sync; keep `localStorage` only as the **guest** fallback.
- Keep `faDigits()` and `money()` verbatim as shared utils/composables.
- Preserve every visual/interaction rule in §1–§12 — this section changes the **plumbing**, not the design.

---

## 14. AI Instructions (always follow)

1. **Preserve the design language** above all. When unsure, match existing patterns rather than inventing.
2. **Never introduce a new colour** unless it is first added to the design tokens in this file and justified.
3. **Never change the spacing, radius, shadow, or type scale.** Use existing tokens.
4. **Reuse existing components** (`el-card`, buttons, fields, drawer, overlay) before creating new ones; prefer composition over duplication.
5. **Keep motion subtle and premium** — reuse the signature easing and duration ladder; never linear (except the marquee), never harsh.
6. **Respect RTL/Persian** — `dir="rtl" lang="fa"`, left‑side drawers/badges, `dir="ltr"` for Latin/numeric runs, `--dt:0`.
7. **Inline styles only.** No stylesheets, no CSS frameworks, no animation libraries. Theme via root custom properties.
8. **Route commerce state** through `eloria-data.js` + `localStorage`; format via `money()`/`faDigits()`.
9. **Maintain accessibility**: `aria-label` on icon controls, `data-screen-label` on sections, and prefer adding the missing focus‑visible + reduced‑motion support when you touch related code.
10. **For small requests, change only what's asked.** Do not redesign or "improve" unrelated areas. Duplicate a DC (`… v2.dc.html`) for major explorations.
11. **Keep pages consistent** — the same nav, footer, cart drawer, and search overlay behaviour across every screen.
12. **Never hand‑edit generated artifacts** (`Eloria Landing - Standalone.html`, `support.js`). Re‑generate from source.
13. **Do not fabricate third‑party brand logos or photography** — request real assets from the user.
14. **Production stack is Nuxt.js + MySQL + user authentication** (see §13). When building real (non‑prototype) features, use this stack: Nuxt/Vue SFCs, Pinia state, MySQL via an ORM, and hashed‑password session/JWT login. Preserve the design language exactly — the stack changes the plumbing, not the visuals.

*End of Agent.md*
