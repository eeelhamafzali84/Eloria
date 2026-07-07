# Phases.md — ELORIA Implementation Roadmap

> Phased delivery plan for taking ELORIA from the current front‑of‑house prototype (inline‑styled Design Components + `localStorage`) to a production **Nuxt.js + MySQL + authenticated** e‑commerce application. Read alongside `Agent.md` — this document defines **sequence and scope**; `Agent.md` defines the **design + engineering rules** that every phase must obey.

**Golden rule for every phase:** the visual language, RTL/Persian correctness, IRANSans typography, token system, and motion rules in `Agent.md` (§1–§12) must never change. Phases change the plumbing, not the design.

---

## Phase 0 — Foundations & Project Setup
**Goal:** a running Nuxt app skeleton with the design system ported.

- Scaffold **Nuxt.js (Vue 3, SSR)** project; enable TypeScript, ESLint, Prettier.
- Configure **RTL/Persian** at the app root (`dir="rtl" lang="fa"`), `@nuxtjs/i18n`.
- Port design tokens (§2/§4 of `Agent.md`) into global `:root` custom properties.
- Set up **IRANSans `@font-face`** (+ Vazirmatn fallback) and base resets.
- Add shared utils/composables: `faDigits()`, `money()`.
- Configure environment management (`.env`), CI lint/build.

**Deliverables:** empty but styled app shell, tokens, fonts, RTL, tooling.
**Exit criteria:** `npm run dev` renders a themed blank page with correct fonts + RTL.

---

## Phase 1 — Database & Infrastructure
**Goal:** MySQL is the system of record.

- Provision **MySQL**; choose ORM (**Prisma** recommended).
- Model core schema (`Agent.md` §13.3): `users`, `products`, `product_variants`, `categories`, `carts`/`cart_items`, `wishlists`/`wishlist_items`, `orders`/`order_items`, `reviews`, `blog_posts`.
- Store **money as integer minor units** (Toman).
- Write versioned **migrations**; write **seed** scripts sourced from the current `eloria-data.js` catalogue + images.
- Set up DB connection pooling and secrets management.

**Deliverables:** migrations, seed data, ORM client.
**Exit criteria:** seeded DB queryable; catalogue matches prototype data shape.

---

## Phase 2 — Design System as Vue Components
**Goal:** rebuild the `el-` components as reusable Vue SFCs, pixel‑matching the prototype.

- Build: `ProductCard.vue`, `CategoryTile.vue`, `MagicTile.vue`, `Button.vue`, `Field.vue` (floating label), `Badge.vue`, `Rating.vue`, `SectionIntro.vue`, `Marquee.vue`, `ReviewCard.vue`, `BlogCard.vue`.
- Build overlays/drawers: `CartDrawer.vue`, `SearchOverlay.vue`.
- Preserve every state/interaction rule (§5/§6): hover lifts, quick‑view, `el-reveal` scroll animation, signature easing.
- Set up **Pinia** stores scaffolding: `useCartStore`, `useWishlistStore`, `useAuthStore`, `useUiStore`.

**Deliverables:** component library + Storybook (optional) matching the design.
**Exit criteria:** components visually identical to the prototype; a11y labels present.

---

## Phase 3 — Catalogue & Storefront Pages (SSR)
**Goal:** data‑driven, SEO‑friendly browsing.

- Build API routes: `GET /api/products` (filter/sort/paginate), `GET /api/products/:id`, categories.
- Build pages with `useFetch`/`useAsyncData`:
  - `pages/index.vue` (Landing — all 18 sections, hero scrub as `<ClientOnly>`).
  - `pages/shop.vue` (Category grid + filters + sort).
  - `pages/product/[id].vue` (Product detail).
- Implement live **search overlay** against the products API.
- SEO: meta tags, structured data (Product schema), prerender static sections.

**Deliverables:** browsable storefront backed by MySQL.
**Exit criteria:** landing/category/product/search all render from the DB via SSR.

---

## Phase 4 — Authentication & Accounts
**Goal:** users can register, log in, and manage their account.

- Auth API (`Agent.md` §13.4/§13.5): register, login, logout, `me`, password reset.
- **Password hashing** (bcrypt/argon2); httpOnly Secure SameSite session/JWT.
- Pages: `login.vue`, `register.vue`, `account.vue` (protected), reset flow — all using `.el-field`, purple CTAs, inline validation, RTL.
- Nuxt **`auth` middleware** guarding protected routes; return‑URL redirect.
- **Guest → user merge** of `localStorage` cart/wishlist on login.
- Security: CSRF on mutations, rate‑limit auth, server‑side validation, HTTPS.

**Deliverables:** working auth + protected account area.
**Exit criteria:** a user can sign up, log in, stay logged in, and see their account.

---

## Phase 5 — Cart, Wishlist & Checkout
**Goal:** complete, persistent commerce flow.

- Server cart/wishlist APIs; Pinia stores sync to server for logged‑in users, `localStorage` for guests.
- Wishlist page + heart toggles wired to state.
- **4‑step checkout** (`Agent.md` §5.9): bag → shipping → payment → confirmation, with the stepper and order summary.
- Orders API: `POST /api/orders`, order history, tracking code.
- Payment: integrate a gateway (or a mock in staging); never trust client totals — recompute server‑side.

**Deliverables:** end‑to‑end purchase producing a persisted order.
**Exit criteria:** guest and logged‑in checkout both create valid orders.

---

## Phase 6 — Content, Reviews & Engagement
**Goal:** trust + lifestyle content.

- Reviews API (`GET` per product, `POST` auth‑required); render review cards + verified badges.
- Blog: `blog_posts` table, list + detail pages using `BlogCard.vue`.
- Consultation form, newsletter signup, Instagram gallery, store locator — wired to backend (leads/subscribers tables) with validation + success states.

**Deliverables:** reviews, blog, lead‑gen forms live.
**Exit criteria:** authenticated users can post reviews; forms persist submissions.

---

## Phase 7 — Accessibility, Performance & Hardening
**Goal:** production quality.

- **A11y:** add `:focus-visible` rings to all interactive elements; `prefers-reduced-motion` fallbacks (disable hero scrub/marquee/reveals); audit `--muted` contrast; ordered headings; `aria-live` on cart/search.
- **Performance:** responsive images + `srcset`, WebP/AVIF, lazy loading, video poster frames, code‑split, cache headers, DB indexes.
- **Security/QA:** OWASP pass, error monitoring, logging, E2E tests (Playwright), load test.

**Deliverables:** audited, tested, monitored app.
**Exit criteria:** a11y + performance budgets met; tests green.

---

## Phase 8 — Launch & Post‑Launch
**Goal:** ship and iterate.

- Staging → production deploy, DB backups, rollback plan, CDN for assets.
- Admin surface for catalogue/orders (optional, gated by `role`).
- Analytics + conversion funnels; A/B hooks (e.g. the A/B direction system).
- Post‑launch backlog: shade‑variant selectors, out‑of‑stock/discount card variants, mega‑menu, loyalty.

**Deliverables:** live production storefront.
**Exit criteria:** ELORIA is publicly live, monitored, and iterating.

---

## Dependency summary
`0 → 1 → 2` can partly parallelise (2 depends on tokens from 0). `3` needs `1+2`. `4` needs `1`. `5` needs `3+4`. `6` needs `3+4`. `7` spans all. `8` last.

*End of Phases.md*
