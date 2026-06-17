# Uncle Inc. — Build Plan

## 1. PRODUCT

Uncle Inc. is a public marketing landing page (Next.js 14 App Router + Tailwind) that introduces the Uncle Inc. platform — an AI-assisted MVP development environment for non-technical early-stage founders — and converts visitors into waitlist signups. The page communicates the core value ("from idea to validated MVP in 72 hours"), walks through 6 capability cards, surfaces a Free vs Pro pricing teaser (Free = validation, Pro = $29/mo for production builds), answers 5 FAQs, and persists email captures to a Supabase `waitlist` table. It is **not** the product app — auth here is a magic-link waitlist only; no login, no dashboard.

**Pain addressed (from research):** solo founders and small teams can't tell whether their idea is worth building until they've shipped something real. Existing tools (Notion, Figma, Bubble, general-purpose AI codegen) either stop at planning, require coding, or don't close the loop from idea → working artifact → user signal. Uncle collapses that into one guided flow.

## 2. WHO IT'S FOR

**ICP:** non-technical or semi-technical early-stage founders (solo or 2–3 people) validating a new product idea, typically pre-seed, who can describe their idea in writing but can't or won't hand-build an MVP. Time-poor, budget-sensitive, skeptical of no-code lock-in and of "AI" hype. They want proof a tool will *actually produce a runnable thing*, not another Notion doc.

**How that shapes the product:**
- Copy is concrete and operator-toned, not breathless. "72 hours," "one prompt," "shareable preview URL" — measurable verbs, not adjectives.
- No invented logos/testimonials/customer counts. The only social proof is a real waitlist counter (`X founders on the waitlist`) pulled from the DB, or omitted if 0.
- The single CTA across the page is the waitlist email capture — repeated in nav, hero, mid-page, pricing section, and final band. Friction is intentionally one field.
- Pricing uses real numbers ($0 / $29) the founder has stated; no "from $X" vagueness, no fake discount strikethroughs.
- FAQ answers the four objections the ICP actually has (does it really build something, do I need to code, what about my data, can I leave).

## 3. LOOK & FEEL

**Vibe / positioning:** "Crisp Operator." Confident enterprise UI, precise 12-col grid, generous whitespace, deep-navy base with indigo/cobalt primary and a restrained teal accent. Forge motif: subtle hammer/anvil mark in the wordmark and a thin horizontal rule with a notched "spark" break between sections.

**Color tokens (Tailwind config + CSS vars):**
- `bg.base` `#0a0a0f` (near-black navy — page background, dark sections)
- `bg.panel` `#11131a` (cards on dark)
- `surface.light` `#f8fafc` (light section background)
- `surface.lightAlt` `#ffffff` (cards on light)
- `text.onDark` `#e5e7eb`, `text.onDarkMuted` `#94a3b8` (slate-400)
- `text.onLight` `#0f172a`, `text.onLightMuted` `#475569`
- `accent.indigo` `#4f46e5` (primary CTAs on light, focus rings)
- `accent.cobalt` `#1e40af` (gradient pair, links on dark)
- `accent.teal` `#14b8a6` (sparingly: success state, "live" dot, spark on forge rule)
- `border.onDark` `rgba(255,255,255,0.08)`, `border.onLight` `#e2e8f0`
- Gradient utility: `from-indigo-500 via-indigo-600 to-cobalt-700`

**Typography:**
- Headings: **Inter**, `font-semibold` (700 for h1), tight tracking `tracking-tight`, h1 `text-5xl md:text-6xl`, h2 `text-3xl md:text-4xl`.
- Body: **IBM Plex Sans**, `font-normal`, `leading-relaxed`, base `text-base md:text-lg`.
- Eyebrow / kicker: Inter uppercase `text-xs tracking-[0.18em] text-teal-400` on dark / `text-indigo-600` on light.
- Numbers (pricing, 72h): Inter with `tabular-nums`.
- Fonts loaded via `next/font/google` with `display: 'swap'` and CSS variables `--font-inter` / `--font-plex-sans`.

**Spacing / layout:**
- Container max `max-w-6xl` (1152px), section vertical padding `py-20 md:py-28`.
- 12-col grid with `gap-6 md:gap-8` for cards.
- Vertical rhythm: each section opens with eyebrow → h2 → supporting paragraph (max-w-2xl mx-auto text-center) → content.

**Key components:**
- `<NavBar>` — sticky, transparent on hero then `backdrop-blur bg-[#0a0a0f]/80` after scroll. Wordmark left, anchor links + "Join waitlist" button right.
- `<Hero>` — two-column on `lg+`: left = headline, sub, dual CTAs, trust microcopy; right = a stylized "product preview" mock (HTML/CSS, no fake screenshot): a small browser-chrome frame showing a fake Uncle build log with a pulsing teal "live" dot and a status line ("Compiling prompt… → Preview ready · 3m 41s").
- `<FeatureCard>` — icon block (24px line icon, 1.5px stroke, indigo on dark / indigo on light), h3, body, optional 1-line "How it works" footer in muted text.
- `<PricingCard>` — two cards side-by-side, the Pro card elevated with `border-indigo-500/40`, teal "Recommended" pill, indigo gradient button.
- `<FAQItem>` — native `<details>`/`<summary>` for zero-JS accordion, plus-button rotates 45° on open, teal accent on the plus.
- `<WaitlistForm>` — controlled input + submit, server action posts to Supabase, inline states (idle / submitting / success / already-joined / error).
- `<Footer>` — three columns: brand+tagline / product links / legal links, plus a small "© 2026 Uncle, Inc." line.
- `<ForgeRule>` — `<hr>`-like divider with a centered teal diamond/spark (SVG) breaking the line — the forge motif.

**Iconography:** Lucide via `lucide-react` — line icons only, 1.5px stroke, size 22. No emoji as UI icons (the example pattern's ⚡/🚀 is replaced).

**Imagery:** No stock photos. The hero "preview" is a hand-built CSS mock of the product, which is honest — it's clearly a representation of the product surface, not a fake screenshot of a finished app.

**Interaction / motion:**
- Smooth scroll for in-page anchors (`scroll-behavior: smooth`, `scroll-margin-top: 80px` on targets).
- Hero headline: subtle `animate-fade-up` (translateY 8px → 0, opacity 0 → 1, 500ms, staggered 80ms across children). Defined in `tailwind.config.ts` `keyframes`.
- Hover on feature cards: `border-white/15` → `border-white/25`, icon background `bg-indigo-500/10` → `bg-indigo-500/20`, `transition-all duration-200`.
- CTA buttons: `hover:bg-indigo-500 active:bg-indigo-700`, focus-visible ring `ring-2 ring-indigo-400 ring-offset-2 ring-offset-[#0a0a0f]`.
- Waitlist success: inline check icon + "You're on the list. We'll email you when access opens."
- Nav becomes solid `bg-[#0a0a0f]` with `border-b border-white/5` once `window.scrollY > 24` (small client component, no framer-motion dependency).
- Respects `prefers-reduced-motion`: disables fade-up and smooth-scroll.

---

### Screen-by-screen

**`<NavBar>`** — `sticky top-0 z-50`. Left: Uncle mark — SVG hammer-over-anvil in indigo with teal spark, then `Uncle Inc.` in Inter 600. Right (md+): Features · How it works · Pricing · FAQ. Far right: ghost link "Sign in" (non-functional placeholder route, hidden until app exists) and indigo solid "Join waitlist" pill → scrolls to `#waitlist`. Mobile: hamburger opens a full-width sheet with the same links + the CTA.

**`<Hero>`** — `min-h-[88vh] flex items-center`. Left col (lg:col-span-7): eyebrow `FORGE YOUR MVP` in teal; h1 `From idea to validated MVP in 72 hours` (last two words in `bg-gradient-to-r from-indigo-400 to-cobalt-400 bg-clip-text text-transparent`); paragraph (IBM Plex) `Uncle turns a written description of your idea into a working, shareable prototype — with real user testing and launch analytics — so you know if it's worth building before you spend a month coding it.`; dual CTAs — primary `Join the waitlist` (indigo solid, links to `#waitlist`), secondary `See how it works` (ghost with arrow, links to `#how`); microcopy `No credit card · Invite-only beta · Cancel anytime`. Right col (lg:col-span-5): the product preview mock.

**`<SocialProofStrip>`** — a thin row directly under the hero, dark. NOT fake logos. Instead: a single live line — `🟢 247 founders on the waitlist` (count fetched from Supabase `count(*)` on the server; if 0, the strip is hidden entirely rather than showing "0 founders"). To the right of that, three textual "what you skip" chips — `No code required` · `No vendor lock-in` · `Export to GitHub anytime`.

**`<Features>`** — `id="features"`. Section eyebrow `EVERYTHING YOU NEED`, h2 `Six tools, one forge`, supporting line `From the first prompt to your first paying user, Uncle handles the plumbing so you can focus on the idea.`. 3×2 grid of `<FeatureCard>`:
1. **Rapid AI Prototyping** — Describe your idea; Uncle generates a clickable web prototype with realistic copy and flows.
2. **Built-in User Testing** — Share a link, watch session replays and heatmaps, collect structured feedback.
3. **Launch Analytics** — Signups, activation, and retention dashboards wired in from day one.
4. **No-Code Required** — Visual editor for tweaks; never touch a config file unless you want to.
5. **Iterative Validation** — A/B test headlines, pricing, and onboarding flows without redeploying.
6. **One-Click Deploy** — Push to a custom domain on Uncle's infra, or export the full Next.js repo to your GitHub.

Each card has a Lucide icon (`Sparkles`, `Users`, `BarChart3`, `Wand2`, `GitBranch`, `Rocket`).

**`<HowItWorks>`** — `id="how"`. Three numbered steps on a horizontal timeline (desktop) / vertical stack (mobile): `1. Describe` → `2. Prototype` → `3. Validate`. Each step has an icon, a one-line title, and 1–2 sentences. A thin indigo line connects the step circles; the active step on hover gets a teal ring. Below the steps, a "Typical timeline: 72h to live link" callout in a panel with a `tabular-nums` `72h`.

**`<PricingTeaser>`** — `id="pricing"`. Eyebrow `PRICING`, h2 `Start free. Upgrade when you're ready.`, supporting line `No surprise overages. No per-seat fees while you're validating.`. Two cards:
- **Free — $0 forever** — "Validation mode": 1 active project, shareable preview link, basic analytics (last 7 days), community support. CTA: `Join the waitlist` (indigo outline).
- **Pro — $29 / month** — "Production mode": everything in Free, plus custom domain, full analytics history, A/B testing, export to GitHub, email support. Recommended pill in teal. CTA: `Join the waitlist` (indigo solid).
Fine print: `Prices in USD. Annual billing coming soon.`

**`<FAQ>`** — `id="faq"`. Eyebrow `FAQ`, h2 `Common questions`. Five `<FAQItem>`s:
1. *Do I need to know how to code?* — No. Uncle is built for non-technical founders. If you can write a paragraph about your idea, you can use Uncle. Power users can drop into the code or export the repo.
2. *What does "validated MVP" actually mean?* — A working web prototype real people can use, with analytics so you can see if they come back — not a Figma mock.
3. *Can I export my project?* — Yes. Every Uncle project can be exported as a standard Next.js + Tailwind repo pushed to your GitHub at any time. No lock-in.
4. *How is my data handled?* — Your prompts, prototypes, and analytics are stored encrypted at rest. We never train models on your private project data. Full policy at /privacy.
5. *When can I get access?* — We're onboarding waitlist members in small cohorts. Joining the waitlist puts you in line; we'll email when your seat is ready.

**`<WaitlistCTA>`** — `id="waitlist"`. Dark band, indigo→cobalt gradient background, centered. H2 `Be on the first 1,000 builds.`, sub `Join the waitlist to get early access and founder pricing for life.`, then `<WaitlistForm>`, then microcopy `We'll never share your email. Unsubscribe in one click.`

**`<FinalCTA>`** — smaller band before footer, light surface, indigo solid button `Join the waitlist →` and one-line `Ready in 72 hours. Cancel anytime.`

**`<Footer>`** — dark. Three columns:
- Brand: mark + `Uncle Inc.` + `Build the thing. Know if it's worth it.`
- Product: Features · How it works · Pricing · FAQ
- Company: About · Privacy · Terms · Contact (`hello@uncle.inc` as `mailto:`)
Bottom row: `© 2026 Uncle, Inc.` left, `Made for founders shipping in public.` right.

---

## 4. USER FLOWS

**Flow A — Primary: visitor → waitlist signup**
1. Land on `/` → see hero.
2. Scroll (or click nav) to features → read 6 cards.
3. (Optional) click `How it works` → see 3-step timeline.
4. (Optional) scroll to pricing → see Free / Pro.
5. (Optional) open FAQ items.
6. Click `Join the waitlist` anywhere on the page → smooth-scroll to `#waitlist`.
7. Enter email in `<WaitlistForm>` → client-side validation (HTML5 `type="email"` + `required` + regex).
8. Submit → POST to server action `joinWaitlist(formData)`.
9. Server action: validate email shape; upsert into Supabase `waitlist` table on `email` (unique constraint). On conflict, return `{ status: 'already_joined' }`. On success, return `{ status: 'ok' }`.
10. Form transitions: idle → submitting (button label `Joining…`, spinner) → success (`You're on the list. Check your inbox for a confirmation.` + check icon) OR `already_joined` (`You're already on the list — we'll be in touch.`) OR `error` (`Something went wrong. Try again.`).
11. Hero strip count re-renders on next page load with the new total.

**Flow B — Secondary: nav anchor click**
1. Click `Features` in nav → smooth-scroll to `#features` with `scroll-margin-top: 80px`.
2. Nav bar stays sticky and translucent.

**Flow C — Secondary: mobile menu**
1. Tap hamburger → sheet slides down (CSS transition on `max-h`).
2. Tap any link → sheet closes, page scrolls.
3. Tap `Join the waitlist` → sheet closes, scrolls to `#waitlist`, focuses the email input (`useRef` + `.focus()`).

**Flow D — Error / empty states**
- Supabase not configured: waitlist form posts to a fallback `/api/waitlist` route that returns a friendly error; form shows `We're having trouble signing you up right now. Email hello@uncle.inc.` No crash.
- Waitlist count query fails: `<SocialProofStrip>` returns `null`; page renders normally.
- JS disabled: `<form action="/api/waitlist" method="POST">` still works as a plain HTML form; success page is `/waitlist/thanks` (server-rendered).

## 5. PAGES / ROUTES

| Route | Purpose | Layout / UI |
|---|---|---|
| `/` | Single landing page — hero, social proof, features, how-it-works, pricing, FAQ, waitlist CTA, final CTA, footer. | All sections above, composed in `app/page.tsx`. |
| `/waitlist/thanks` | Plain HTML fallback success page (works without JS). | Centered panel: heading `You're on the list.`, body copy, link `← Back to Uncle Inc.` |
| `/privacy` | Privacy policy stub. | Single column, prose, `max-w-3xl`. Honest placeholder with real sections (data we collect, how we use it, your rights, contact). |
| `/terms` | Terms of service stub. | Same `max-w-3xl` prose layout. Honest placeholders. |
| `/api/waitlist` | POST endpoint (fallback for no-JS form submissions). | JSON: `{ ok: true }` on insert/conflict, `{ ok: false, error }` otherwise. |
| `/api/waitlist/count` | GET endpoint returning `{ count: number }` for the social proof strip. | Cached 60s. |

(No `/sign-in`, `/dashboard`, `/app` — this is the marketing page only. The nav "Sign in" link is hidden until the app exists.)

## 6. CORE FEATURES

1. **Hero with gradient headline + dual CTAs**
   - Renders headline with the last two words (`validated MVP`) using a `bg-clip-text` gradient (`from-indigo-400 via-indigo-500 to-cobalt-400`).
   - Primary CTA `<a href="#waitlist">`, secondary `<a href="#how">`. Both `focus-visible` rings on dark.

2. **Animated product preview mock (Hero right column)**
   - Hand-built HTML/CSS: a `rounded-xl border border-white/10 bg-panel` with a fake macOS-style traffic-light row, a tab labeled `uncle.build`, a pulsing teal dot (`animate-pulse`), and 3 fake log lines that fade in with staggered delays (`animation-delay: 0s / 0.4s / 0.8s`).
   - Below the mock, a small status row: `● Live · preview.uncle.inc/your-idea` in IBM Plex mono fallback to `font-mono`.
   - Pure CSS animation, no images, no fake screenshot of an unreleased app.

3. **Live waitlist count strip**
   - Server component reads `count` from Supabase `waitlist`. Renders only if `count > 0`.
   - Uses `tabular-nums` so the number doesn't jiggle as it grows.

4. **6-card feature grid**
   - Responsive 1 → 2 → 3 columns. Each card is `rounded-2xl border border-white/10 bg-panel/60 p-6 hover:border-white/20 transition`. Icon in a `h-10 w-10 rounded-lg bg-indigo-500/10 text-indigo-400 flex items-center justify-center` wrapper.

5. **3-step "How it works" timeline**
   - Horizontal on `md+`: connecting line is an absolutely-positioned `h-px bg-white/10`; step circles are `h-10 w-10 rounded-full border border-white/15 bg-[#0a0a0f] text-indigo-400 flex items-center justify-center font-semibold`. Stacks vertically on mobile.

6. **Pricing teaser (2 cards)**
   - Pro card has `ring-1 ring-indigo-500/40` and a teal `Recommended` pill absolutely positioned at `-top-3 left-6`.
   - Buttons are `<a href="#waitlist">` — the page has only one conversion path.

7. **FAQ accordion (5 items)**
   - Native `<details>`/`<summary>`. Summary has `cursor-pointer`, plus icon rotates 45° via `[&[open]_.plus-icon]:rotate-45`.
   - Body uses IBM Plex with `text-onDarkMuted` and `leading-relaxed`.

8. **Waitlist email form**
   - Client component with `useFormStatus` semantics via a small custom `useTransition` wrapper (no react-hook-form dependency).
   - Inputs: `email` (required, `type="email"`), honeypot `website` field (hidden, must be empty — bots fill it).
   - Submits to server action `joinWaitlist`. Inline states with `aria-live="polite"` on the status message.
   - Honeypot rejection returns `{ status: 'ok' }` to the bot without writing.

9. **Sticky nav with scroll-aware background**
   - Client component uses `IntersectionObserver` on a sentinel `<div>` placed at `top: 80px`. When sentinel scrolls out of view, nav gets `bg-[#0a0a0f]/85 backdrop-blur border-b border-white/5`.

10. **Mobile menu sheet**
    - Client component. Toggle via `useState`. Sheet uses `aria-hidden` + `aria-expanded` on the trigger. Closes on Escape (`keydown` listener) and on link click. Locks `body` scroll via `overflow-hidden` class on `<html>` while open.

11. **Reduced-motion support**
    - Global CSS: `@media (prefers-reduced-motion: reduce) { *, *::before, *::after { animation-duration: 0.001ms !important; transition-duration: 0.001ms !important; scroll-behavior: auto !important; } }`.

12. **SEO + metadata**
    - `app/layout.tsx` exports `metadata`: `title.default: "Uncle Inc. — From idea to validated MVP in 72 hours"`, `description`, `openGraph`, `twitter`. `metadataBase` set to `https://uncle.inc`.
    - `app/sitemap.ts` returns `/`, `/privacy`, `/terms`.
    - `app/robots.ts` allows all, points sitemap.
    - JSON-LD `Organization` script in `<head>` with real `name`, `url`, `logo` (the wordmark SVG), `sameAs: []` (empty — no fake socials).

## 7. DATA MODEL

Single Supabase table. No other tables, no auth users on this page.

**`waitlist`**
- `id` — `uuid` PK, default `gen_random_uuid()`
- `email` — `text`, NOT NULL, UNIQUE, `lower(email)` enforced via a `lower(email)` unique index
- `source` — `text`, nullable (e.g., `"hero"`, `"pricing"`, `"final"`) — captured via hidden form field for future segmentation
- `referrer` — `text`, nullable (from `document.referrer` on client, sent with form)
- `user_agent` — `text`, nullable (truncated to 500 chars server-side)
- `created_at` — `timestamptz`, NOT NULL, default `now()`

**RLS:** enabled. Single policy: `insert` allowed for the `anon` role (so the public form can write), `select` denied for `anon`. The service role key (server-only) is used to read the count.

**Indexes:**
- `idx_waitlist_email_lower` UNIQUE on `lower(email)`
- `idx_waitlist_created_at` on `created_at desc` (for future cohort queries)

**Environment variables required:**
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY` (insert only)
- `SUPABASE_SERVICE_ROLE_KEY` (server-only, for count + insert with upsert)

If env vars are missing, the page renders without the count strip and the form returns a graceful error — it never crashes.

## 8. AUTH

**No auth on this page.** The waitlist form is a single email capture — no login, no password, no OAuth, no magic-link sign-in. The signup uses a Supabase service-role upsert behind a server action. This deliberately avoids any dead "Sign in with Google" button.

## 9. FILES

(see FILES array at end)

## 10. ACCEPTANCE

- [ ] `npm run dev` starts the app on `:3000` with no console errors.
- [ ] `/` renders all sections in order: Nav, Hero, SocialProof (or hidden if 0), Features, HowItWorks, Pricing, FAQ, WaitlistCTA, FinalCTA, Footer.
- [ ] Brand palette matches the spec exactly: `#0a0a0f`, `#11131a`, `#4f46e5`, `#1e40af`, `#64748b`, `#14b8a6`.
- [ ] Inter is used for headings, IBM Plex Sans for body, both loaded via `next/font` with no FOUT.
- [ ] No emoji used as UI icons anywhere; all icons are Lucide line icons at 1.5px stroke.
- [ ] No fake testimonials, no customer logos, no invented user counts, no ratings, no press quotes, no revenue figures.
- [ ] Hero gradient renders on the words "validated MVP" only.
- [ ] Nav becomes solid + blurred after the user scrolls past 24px.
- [ ] Mobile hamburger opens a sheet with all 4 anchor links + the waitlist CTA, closes on Escape and on link click, locks body scroll.
- [ ] All in-page anchor links smooth-scroll and respect `scroll-margin-top: 80px`.
- [ ] `prefers-reduced-motion` disables all animations and smooth scroll.
- [ ] Waitlist form: empty submit blocked by HTML5 validation; valid email submits to server action; success / already_joined / error states each render the correct message with `aria-live="polite"`.
- [ ] Honeypot field `website` is hidden visually (`sr-only` + `tabindex="-1"` + `autoComplete="off"`) and any non-empty submission is silently dropped without writing to the DB.
- [ ] Submitting a duplicate email returns the `already_joined` message without crashing or creating a second row (verified by DB unique index).
- [ ] `/waitlist/thanks` renders without JS as a plain HTML page reachable via a no-JS form POST to `/api/waitlist`.
- [ ] With Supabase env vars unset, the page still renders, the social-proof strip is hidden, and the waitlist form shows the fallback error message — no thrown errors, no blank screen.
- [ ] With Supabase env vars set, a submitted email appears in the `waitlist` table with `created_at = now()`.
- [ ] Lighthouse: Performance ≥ 90, Accessibility ≥ 95, Best Practices ≥ 95, SEO ≥ 95 on `/`.
- [ ] All interactive elements have visible `:focus-visible` rings (indigo on dark, indigo on light).
- [ ] `<html lang="en">`, semantic landmarks (`<header>`, `<main>`, `<section>` with `aria-labelledby`, `<footer>`), every `<img>` has `alt`, every form input has a `<label>`.
- [ ] `/privacy` and `/terms` render with real prose (not lorem ipsum) covering: data collected, usage, retention, your rights, contact — and a copyright line.
- [ ] `metadata` exports a complete title, description, OG image (use a generated OG route at `/api/og` that renders the hero headline on the brand background), and Twitter card.
- [ ] `app/sitemap.ts` and `app/robots.ts` produce valid output at `/sitemap.xml` and `/robots.txt`.
- [ ] JSON-LD `Organization` in `<head>` has only real `name`, `url`, `logo`, and empty `sameAs`.
- [ ] `npm run build` completes with zero TypeScript errors and zero ESLint errors.
- [ ] Production build runs via `npm start` and behaves identically to dev for all user flows above.

FILES: ["app/layout.tsx", "app/page.tsx", "app/globals.css", "app/sitemap.ts", "app/robots.ts", "app/privacy/page.tsx", "app/terms/page.tsx", "app/waitlist/thanks/page.tsx", "app/api/waitlist/route.ts", "app/api/waitlist/count/route.ts", "app/api/og/route.tsx", "app/actions/waitlist.ts", "lib/supabase/server.ts", "lib/supabase/client.ts", "lib/utils.ts", "components/NavBar.tsx", "components/MobileMenu.tsx", "components/Hero.tsx", "components/ProductPreviewMock.tsx", "components/SocialProofStrip.tsx", "components/Features.tsx", "components/FeatureCard.tsx", "components/HowItWorks.tsx", "components/PricingTeaser.tsx", "components/PricingCard.tsx", "components/FAQ.tsx", "components/FAQItem.tsx", "components/WaitlistCTA.tsx", "components/WaitlistForm.tsx", "components/FinalCTA.tsx", "components/Footer.tsx", "components/ForgeRule.tsx", "components/Wordmark.tsx", "components/icons.tsx", "supabase/migrations/0001_waitlist.sql", "tailwind.config.ts", "postcss.config.mjs", "next.config.mjs", "tsconfig.json", ".env.example", "package.json"