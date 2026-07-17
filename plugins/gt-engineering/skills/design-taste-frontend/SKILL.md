---
name: design-taste-frontend
description: Senior UI/UX Engineer. Architect digital interfaces overriding default LLM biases. Enforces metric-based rules, strict component architecture, CSS hardware acceleration, and balanced design engineering.
---

# High-Agency Frontend Skill

## 1. ACTIVE BASELINE CONFIGURATION
* DESIGN_VARIANCE: 8 (1=Perfect Symmetry, 10=Artsy Chaos)
* MOTION_INTENSITY: 6 (1=Static/No movement, 10=Cinematic/Magic Physics)
* VISUAL_DENSITY: 4 (1=Art Gallery/Airy, 10=Pilot Cockpit/Packed Data)

**AI Instruction:** The standard baseline for all generations is strictly set to these values (8, 6, 4). Do not ask the user to edit this file. Otherwise, ALWAYS listen to the user: adapt these values dynamically based on what they explicitly request in their chat prompts. Use these baseline (or user-overridden) values as your global variables to drive the specific logic in Sections 3 through 7.

## 2. DEFAULT ARCHITECTURE & CONVENTIONS
Unless the user explicitly specifies a different stack, adhere to these structural constraints to maintain consistency:

* **WEBGEN HAND-OFF [READ FIRST]:** When invoked as the hand-off target of `/webgen`, the `args` payload carries `project: <display> (slug: <slug>)`, `identity-migrated: true`, `stack: <one-line stack descriptor>`, and `brief: <verbatim brief>`. Inherit context — do NOT re-onboard or re-ask the brief. Treat the `stack` line as authoritatively overriding the framework / RSC / styling / icons defaults below. When `identity-migrated: true`, NEVER touch `package.json`, `.cta.json`, `wrangler.jsonc`, `README.md`, or the root document `<title>` — that work is already done. Use `display` (not `slug`) in visible copy, navigation, and any new route `<title>` metadata.
* **DEPENDENCY VERIFICATION [MANDATORY]:** Before importing ANY 3rd party library (e.g. `framer-motion`, `lucide-react`, `zustand`), you MUST check `package.json`. If the package is missing, you MUST output the installation command (e.g. `npm install package-name`) before providing the code. **Never** assume a library exists.
* **Framework & Interactivity:** TanStack Start (React 19) with file-based routing via TanStack Router + `@tanstack/router-plugin`. There is NO RSC, NO `'use client'`, NO `next/*` import path, and NO `app/` directory in this stack.
    * **UNIVERSAL COMPONENTS:** All components are universal — they render on both the server (SSR) and the client. Effects, animations, and DOM-bound work belong inside `useEffect` (or motion library hooks). NEVER add `'use client'` directives — they do not exist in this stack and will break the build.
    * **INTERACTIVITY ISOLATION:** When Sections 4 or 7 (Motion/Liquid Glass) are active, the specific interactive UI MUST be extracted as an isolated, memoized leaf component so its re-renders do not invalidate parent layouts.
    * **ROUTE FILES:** Add new routes as `src/routes/<segment>.tsx`, each exporting `Route = createFileRoute('/path')({ component: X })`. The router plugin auto-generates `routeTree.gen.ts` — NEVER hand-edit it.
    * **ROOT LAYOUT & HEAD METADATA:** Live in `src/routes/__root.tsx` under `head().meta` / `head().links`. Preserve the existing devtools wiring; modify only the `meta`/`links` arrays and the shell JSX.
    * **SERVER FUNCTIONS:** Use `createServerFn` from `@tanstack/react-start` for server-only work. Avoid Node-only APIs (`fs`, `child_process`, native binaries) — the deploy target is Cloudflare Workers, which does not provide them.
* **State Management:**
    * Local UI → `useState` / `useReducer`.
    * Server cache → TanStack Query, integrated via `@tanstack/react-router-ssr-query` (already installed). For direct client cache, add `@tanstack/react-query` to `package.json` first.
    * URL state (filters, tabs, pagination) → TanStack Router `search` params and route params. Prefer this over local state when the state should survive reload or be shareable.
    * Global client state → add `zustand` to `package.json` first. Reach for global state strictly to avoid deep prop-drilling, never as a default.
* **Styling Policy:** Tailwind v4 via `@tailwindcss/vite` (already installed and wired in `vite.config.ts`).
    * **NO `tailwind.config.{js,ts}`, NO `postcss.config.js`.** Theme tokens, custom utilities, and `@theme` declarations live in `src/styles.css`.
    * v3 syntax (JS config object, `@tailwind base/components/utilities`) is BANNED in this project — use v4 CSS-first configuration (`@import "tailwindcss"`).
* **Deploy Target:** Cloudflare Workers via `wrangler` (`npm run deploy`). Project metadata: `wrangler.jsonc`. The Vite plugin `@cloudflare/vite-plugin` handles the dev/SSR loop.
* **ANTI-EMOJI POLICY [CRITICAL]:** NEVER use emojis in code, markup, text content, or alt text. Replace symbols with high-quality icons (Radix, Phosphor) or clean SVG primitives. Emojis are BANNED.
* **Responsiveness & Spacing:**
  * Standardize breakpoints (`sm`, `md`, `lg`, `xl`).
  * Contain page layouts using `max-w-[1400px] mx-auto` or `max-w-7xl`.
  * **Viewport Stability [CRITICAL]:** NEVER use `h-screen` for full-height Hero sections. ALWAYS use `min-h-[100dvh]` to prevent catastrophic layout jumping on mobile browsers (iOS Safari).
  * **Grid over Flex-Math:** NEVER use complex flexbox percentage math (`w-[calc(33%-1rem)]`). ALWAYS use CSS Grid (`grid grid-cols-1 md:grid-cols-3 gap-6`) for reliable structures.
* **Icons:** Use `lucide-react` (already installed) — import individually, e.g. `import { ArrowRight } from 'lucide-react'`. Standardize `strokeWidth` globally (exclusively `1.5` or `2.0`). `@phosphor-icons/react` or `@radix-ui/react-icons` may be substituted ONLY if explicitly requested and added to `package.json` first.


## 3. DESIGN ENGINEERING DIRECTIVES (Bias Correction)
LLMs have statistical biases toward specific UI cliché patterns. Proactively construct premium interfaces using these engineered rules:

**Rule 1: Deterministic Typography**
* **Display/Headlines:** Default to `text-4xl md:text-6xl tracking-tighter leading-none`.
    * **ANTI-SLOP:** Discourage `Inter` for "Premium" or "Creative" vibes. Force unique character using `Geist`, `Outfit`, `Cabinet Grotesk`, or `Satoshi`.
    * **TECHNICAL UI RULE:** Serif fonts are strictly BANNED for Dashboard/Software UIs. For these contexts, use exclusively high-end Sans-Serif pairings (`Geist` + `Geist Mono` or `Satoshi` + `JetBrains Mono`).
* **Body/Paragraphs:** Default to `text-base text-gray-600 leading-relaxed max-w-[65ch]`.

**Rule 2: Color Calibration**
* **Constraint:** Max 1 Accent Color. Saturation < 80%.
* **THE LILA BAN:** The "AI Purple/Blue" aesthetic is strictly BANNED. No purple button glows, no neon gradients. Use absolute neutral bases (Zinc/Slate) with high-contrast, singular accents (e.g. Emerald, Electric Blue, or Deep Rose).
* **COLOR CONSISTENCY:** Stick to one palette for the entire output. Do not fluctuate between warm and cool grays within the same project.

**Rule 3: Layout Diversification**
* **ANTI-CENTER BIAS:** Centered Hero/H1 sections are strictly BANNED when `LAYOUT_VARIANCE > 4`. Force "Split Screen" (50/50), "Left Aligned content/Right Aligned asset", or "Asymmetric White-space" structures.

**Rule 4: Materiality, Shadows, and "Anti-Card Overuse"**
* **DASHBOARD HARDENING:** For `VISUAL_DENSITY > 7`, generic card containers are strictly BANNED. Use logic-grouping via `border-t`, `divide-y`, or purely negative space. Data metrics should breathe without being boxed in unless elevation (z-index) is functionally required.
* **Execution:** Use cards ONLY when elevation communicates hierarchy. When a shadow is used, tint it to the background hue.

**Rule 5: Interactive UI States**
* **Mandatory Generation:** LLMs naturally generate "static" successful states. You MUST implement full interaction cycles:
  * **Loading:** Skeletal loaders matching layout sizes (avoid generic circular spinners).
  * **Empty States:** Beautifully composed empty states indicating how to populate data.
  * **Error States:** Clear, inline error reporting (e.g., forms).
  * **Tactile Feedback:** On `:active`, use `-translate-y-[1px]` or `scale-[0.98]` to simulate a physical push indicating success/action.

**Rule 6: Data & Form Patterns**
* **Forms:** Label MUST sit above input. Helper text is optional but should exist in markup. Error text below input. Use a standard `gap-2` for input blocks.

## 4. CREATIVE PROACTIVITY (Anti-Slop Implementation)
To actively combat generic AI designs, systematically implement these high-end coding concepts as your baseline:
* **"Liquid Glass" Refraction:** When glassmorphism is needed, go beyond `backdrop-blur`. Add a 1px inner border (`border-white/10`) and a subtle inner shadow (`shadow-[inset_0_1px_0_rgba(255,255,255,0.1)]`) to simulate physical edge refraction.
* **Magnetic Micro-physics (If MOTION_INTENSITY > 5):** Implement buttons that pull slightly toward the mouse cursor. **CRITICAL:** NEVER use React `useState` for magnetic hover or continuous animations. Use EXCLUSIVELY Framer Motion's `useMotionValue` and `useTransform` outside the React render cycle to prevent performance collapse on mobile.
* **Perpetual Micro-Interactions:** When `MOTION_INTENSITY > 5`, embed continuous, infinite micro-animations (Pulse, Typewriter, Float, Shimmer, Carousel) in standard components (avatars, status dots, backgrounds). Apply premium Spring Physics (`type: "spring", stiffness: 100, damping: 20`) to all interactive elements—no linear easing.
* **Layout Transitions:** Always utilize Framer Motion's `layout` and `layoutId` props for smooth re-ordering, resizing, and shared element transitions across state changes.
* **Staggered Orchestration:** Do not mount lists or grids instantly. Use `staggerChildren` (Framer) or CSS cascade (`animation-delay: calc(var(--index) * 100ms)`) to create sequential waterfall reveals. **CRITICAL:** For `staggerChildren`, the Parent (`variants`) and Children MUST reside in the same component subtree. If data is fetched asynchronously, pass the data as props into a centralized Parent Motion wrapper.

## 5. PERFORMANCE GUARDRAILS
* **DOM Cost:** Apply grain/noise filters exclusively to fixed, pointer-event-none pseudo-elements (e.g., `fixed inset-0 z-50 pointer-events-none`) and NEVER to scrolling containers to prevent continuous GPU repaints and mobile performance degradation.
* **Hardware Acceleration:** Never animate `top`, `left`, `width`, or `height`. Animate exclusively via `transform` and `opacity`.
* **Z-Index Restraint:** NEVER spam arbitrary `z-50` or `z-10` unprompted. Use z-indexes strictly for systemic layer contexts (Sticky Navbars, Modals, Overlays).

## 6. MOBILE-FIRST ENGINEERING
LLMs generate desktop-first layouts by default. This section enforces mobile engineering as a first-class discipline — not an afterthought collapse. Rules here are non-negotiable regardless of DESIGN_VARIANCE or MOTION_INTENSITY settings.

**Rule M1: Touch Target Sizing (Non-Negotiable)**
* All tappable elements — buttons, links, toggles, icon buttons — MUST meet a **minimum 44×44px tap target** (Apple HIG / WCAG 2.5.5). Visual size and tap size are separate concerns.
* Inline links too small to tap safely MUST be wrapped with `p-2 -m-2` to expand the hit area without affecting surrounding layout geometry.
* Icon-only controls MUST carry an `aria-label` and an explicit tap zone via `p-3` minimum padding.

**Rule M2: Prevent iOS Auto-Zoom on Inputs**
* All `<input>`, `<textarea>`, and `<select>` elements MUST render at `text-base` (16px) minimum on mobile. **Any font-size below 16px on a focused input triggers iOS Safari's forced auto-zoom** — a catastrophic UX regression that is non-recoverable without a page reload.
* Apply `text-base` directly on the input element, never rely on an inherited parent scale.

**Rule M3: Responsive Typography — Mobile Scale First**
* ALWAYS define the mobile value before the desktop override. Writing raw desktop-only scale classes is BANNED:
  * Display: `text-3xl sm:text-4xl md:text-6xl` — NOT bare `text-6xl`
  * Section heads: `text-xl sm:text-2xl md:text-4xl`
  * Captions: `text-sm` — acceptable only on non-interactive, non-input elements
* Headlines should tighten tracking at desktop, not mobile: `tracking-tight md:tracking-tighter`.

**Rule M4: Safe Area Insets — iOS Notch & Home Indicator**
* Fixed or sticky elements anchored to the **bottom** of the screen MUST include `pb-[env(safe-area-inset-bottom)]`. No exceptions for navigation bars, FABs, bottom sheets, or sticky CTAs.
* Full-screen modals, drawers, and overlays MUST pad the **top** via `pt-[env(safe-area-inset-top)]` to clear the Dynamic Island / notch hardware.
* Treat a missing safe-area inset as a build-blocking visual bug — content clipped by device hardware is shipped broken.

**Rule M5: Mobile Navigation Patterns**
* **BANNED:** Rendering a horizontal desktop navbar directly at `< md:` viewports. Horizontal nav at small breakpoints forces overflow, compresses links, and destroys usability.
* On mobile, navigation MUST take one of two forms:
  1. **Hamburger Drawer** — slides in from the left or right via `framer-motion` `AnimatePresence`. The trigger icon transforms from menu to close with a spring rotation. Drawer MUST lock body scroll while open.
  2. **Bottom Tab Bar** — for app-shell layouts (max 5 tabs). `fixed bottom-0 inset-x-0` with `pb-[env(safe-area-inset-bottom)]`. Active tab: color shift + `scale-105` spring on the icon — NEVER underlines.
* Drawer and bottom-sheet overlays MUST apply `overflow-hidden` to `<html>` while open and restore scroll precisely on close.

**Rule M6: Layout Collapse — Single-Column Discipline**
* The base layout direction for ALL components is `flex-col` / `grid-cols-1`. Desktop multi-column is a **progressive enhancement** layered on top, never the starting state.
* Required grid pattern: `grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3`. Any grid starting at ≥ 2 columns without a `grid-cols-1` mobile base is BANNED.
* Horizontal padding on all layout wrappers: `px-4 sm:px-6 lg:px-8`. Zero horizontal padding on mobile is BANNED.
* Two-column flex layouts below `sm:` are BANNED unless one column is a narrow metadata strip (≤ 30% width, e.g., an avatar or a status badge). Otherwise, stack full-width.

**Rule M7: The Thumb Zone — Ergonomic Action Placement**
* The **bottom 60% of the mobile viewport** is the natural thumb comfort zone. Conversion actions belong there.
* **PRIMARY CTAs** (Purchase, Subscribe, Submit) MUST be reachable without thumb stretch — never anchored exclusively above the fold or at the top of a long form.
* Form submit buttons: ALWAYS `w-full` on mobile to maximize the tap surface.
* **Destructive actions** (Delete, Remove, Disconnect) MUST sit outside the casual thumb zone and require an explicit confirmation step before execution.

**Rule M8: Scroll & Overflow Discipline**
* Prevent accidental horizontal overflow: every root layout wrapper MUST apply `overflow-x-hidden`.
* Intentional horizontal scrollers (chip rows, tag lists, carousels): use `overflow-x-auto scroll-smooth snap-x snap-mandatory`. Add `pr-4` to the scroll container so the last item isn't flush-clipped against the edge.
* **BANNED:** Nesting `position: fixed` inside `overflow: hidden` containers — a known iOS Safari rendering bug that causes fixed elements to scroll with the page.
* Body scroll MUST be locked (`overflow-hidden` on `<html>`) while any modal, drawer, or bottom sheet is open, and restored precisely on close.

**Rule M9: Mobile Performance Budget**
* `backdrop-filter: blur()` (glassmorphism) is GPU-expensive on mobile. Scale it back: `backdrop-blur-md sm:backdrop-blur-xl`. NEVER stack full-strength blur across multiple simultaneous elements on the same mobile viewport.
* All entrance animations MUST respect `prefers-reduced-motion`. Wrap with Framer Motion's `useReducedMotion()` and skip to the final state when the preference is active.
* **BANNED:** Applying `will-change: transform` to more than 3 simultaneously animated elements on a single mobile viewport — it causes memory pressure spikes on mid-range Android devices.
* Images MUST define explicit `width` and `height` attributes, or use `aspect-ratio` in CSS, to eliminate Cumulative Layout Shift (CLS) on mobile load.

## 7. TECHNICAL REFERENCE (Dial Definitions)

### DESIGN_VARIANCE (Level 1-10)
* **1-3 (Predictable):** Flexbox `justify-center`, strict 12-column symmetrical grids, equal paddings.
* **4-7 (Offset):** Use `margin-top: -2rem` overlapping, varied image aspect ratios (e.g., 4:3 next to 16:9), left-aligned headers over center-aligned data.
* **8-10 (Asymmetric):** Masonry layouts, CSS Grid with fractional units (e.g., `grid-template-columns: 2fr 1fr 1fr`), massive empty zones (`padding-left: 20vw`).
* **MOBILE OVERRIDE:** For levels 4-10, any asymmetric layout above `md:` MUST aggressively fall back to a strict, single-column layout (`w-full`, `px-4`, `py-8`) on viewports `< 768px` to prevent horizontal scrolling and layout breakage.

### MOTION_INTENSITY (Level 1-10)
* **1-3 (Static):** No automatic animations. CSS `:hover` and `:active` states only.
* **4-7 (Fluid CSS):** Use `transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1)`. Use `animation-delay` cascades for load-ins. Focus strictly on `transform` and `opacity`. Use `will-change: transform` sparingly.
* **8-10 (Advanced Choreography):** Complex scroll-triggered reveals or parallax. Use Framer Motion hooks. NEVER use `window.addEventListener('scroll')`.

### VISUAL_DENSITY (Level 1-10)
* **1-3 (Art Gallery Mode):** Lots of white space. Huge section gaps. Everything feels very expensive and clean.
* **4-7 (Daily App Mode):** Normal spacing for standard web apps.
* **8-10 (Cockpit Mode):** Tiny paddings. No card boxes; just 1px lines to separate data. Everything is packed. **Mandatory:** Use Monospace (`font-mono`) for all numbers.

## 8. AI TELLS (Forbidden Patterns)
To guarantee a premium, non-generic output, you MUST strictly avoid these common AI design signatures unless explicitly requested:

### Visual & CSS
* **NO Neon/Outer Glows:** Do not use default `box-shadow` glows or auto-glows. Use inner borders or subtle tinted shadows.
* **NO Pure Black:** Never use `#000000`. Use Off-Black, Zinc-950, or Charcoal.
* **NO Oversaturated Accents:** Desaturate accents to blend elegantly with neutrals.
* **NO Excessive Gradient Text:** Do not use text-fill gradients for large headers.
* **NO Custom Mouse Cursors:** They are outdated and ruin performance/accessibility.

### Typography
* **NO Inter Font:** Banned. Use `Geist`, `Outfit`, `Cabinet Grotesk`, or `Satoshi`.
* **NO Oversized H1s:** The first heading should not scream. Control hierarchy with weight and color, not just massive scale.
* **Serif Constraints:** Use Serif fonts ONLY for creative/editorial designs. **NEVER** use Serif on clean Dashboards.

### Layout & Spacing
* **Align & Space Perfectly:** Ensure padding and margins are mathematically perfect. Avoid floating elements with awkward gaps.
* **NO 3-Column Card Layouts:** The generic "3 equal cards horizontally" feature row is BANNED. Use a 2-column Zig-Zag, asymmetric grid, or horizontal scrolling approach instead.

### Content & Data (The "Jane Doe" Effect)
* **NO Generic Names:** "John Doe", "Sarah Chan", or "Jack Su" are banned. Use highly creative, realistic-sounding names.
* **NO Generic Avatars:** DO NOT use standard SVG "egg" or Lucide user icons for avatars. Use creative, believable photo placeholders or specific styling.
* **NO Fake Numbers:** Avoid predictable outputs like `99.99%`, `50%`, or basic phone numbers (`1234567`). Use organic, messy data (`47.2%`, `+1 (312) 847-1928`).
* **NO Startup Slop Names:** "Acme", "Nexus", "SmartFlow". Invent premium, contextual brand names.
* **NO Filler Words:** Avoid AI copywriting clichés like "Elevate", "Seamless", "Unleash", or "Next-Gen". Use concrete verbs.

### External Resources & Components
* **NO Broken Unsplash Links:** Do not use Unsplash. Use absolute, reliable placeholders like `https://picsum.photos/seed/{random_string}/800/600` or SVG UI Avatars.
* **shadcn/ui Customization:** You may use `shadcn/ui`, but NEVER in its generic default state. You MUST customize the radii, colors, and shadows to match the high-end project aesthetic.
* **Production-Ready Cleanliness:** Code must be extremely clean, visually striking, memorable, and meticulously refined in every detail.

## 9. THE CREATIVE ARSENAL (High-End Inspiration)
Do not default to generic UI. Pull from this library of advanced concepts to ensure the output is visually striking and memorable. When appropriate, leverage **GSAP (ScrollTrigger/Parallax)** for complex scrolltelling or **ThreeJS/WebGL** for 3D/Canvas animations, rather than basic CSS motion. **CRITICAL:** Never mix GSAP/ThreeJS with Framer Motion in the same component tree. Default to Framer Motion for UI/Bento interactions. Use GSAP/ThreeJS EXCLUSIVELY for isolated full-page scrolltelling or canvas backgrounds, wrapped in strict useEffect cleanup blocks.

### The Standard Hero Paradigm
* Stop doing centered text over a dark image. Try asymmetric Hero sections: Text cleanly aligned to the left or right. The background should feature a high-quality, relevant image with a subtle stylistic fade (darkening or lightening gracefully into the background color depending on if it is Light or Dark mode).

### Navigation & Menüs
* **Mac OS Dock Magnification:** Nav-bar at the edge; icons scale fluidly on hover.
* **Magnetic Button:** Buttons that physically pull toward the cursor.
* **Gooey Menu:** Sub-items detach from the main button like a viscous liquid.
* **Dynamic Island:** A pill-shaped UI component that morphs to show status/alerts.
* **Contextual Radial Menu:** A circular menu expanding exactly at the click coordinates.
* **Floating Speed Dial:** A FAB that springs out into a curved line of secondary actions.
* **Mega Menu Reveal:** Full-screen dropdowns that stagger-fade complex content.

### Layout & Grids
* **Bento Grid:** Asymmetric, tile-based grouping (e.g., Apple Control Center).
* **Masonry Layout:** Staggered grid without fixed row heights (e.g., Pinterest).
* **Chroma Grid:** Grid borders or tiles showing subtle, continuously animating color gradients.
* **Split Screen Scroll:** Two screen halves sliding in opposite directions on scroll.
* **Curtain Reveal:** A Hero section parting in the middle like a curtain on scroll.

### Cards & Containers
* **Parallax Tilt Card:** A 3D-tilting card tracking the mouse coordinates.
* **Spotlight Border Card:** Card borders that illuminate dynamically under the cursor.
* **Glassmorphism Panel:** True frosted glass with inner refraction borders.
* **Holographic Foil Card:** Iridescent, rainbow light reflections shifting on hover.
* **Tinder Swipe Stack:** A physical stack of cards the user can swipe away.
* **Morphing Modal:** A button that seamlessly expands into its own full-screen dialog container.

### Scroll-Animations
* **Sticky Scroll Stack:** Cards that stick to the top and physically stack over each other.
* **Horizontal Scroll Hijack:** Vertical scroll translates into a smooth horizontal gallery pan.
* **Locomotive Scroll Sequence:** Video/3D sequences where framerate is tied directly to the scrollbar.
* **Zoom Parallax:** A central background image zooming in/out seamlessly as you scroll.
* **Scroll Progress Path:** SVG vector lines or routes that draw themselves as the user scrolls.
* **Liquid Swipe Transition:** Page transitions that wipe the screen like a viscous liquid.

### Galleries & Media
* **Dome Gallery:** A 3D gallery feeling like a panoramic dome.
* **Coverflow Carousel:** 3D carousel with the center focused and edges angled back.
* **Drag-to-Pan Grid:** A boundless grid you can freely drag in any compass direction.
* **Accordion Image Slider:** Narrow vertical/horizontal image strips that expand fully on hover.
* **Hover Image Trail:** The mouse leaves a trail of popping/fading images behind it.
* **Glitch Effect Image:** Brief RGB-channel shifting digital distortion on hover.

### Typography & Text
* **Kinetic Marquee:** Endless text bands that reverse direction or speed up on scroll.
* **Text Mask Reveal:** Massive typography acting as a transparent window to a video background.
* **Text Scramble Effect:** Matrix-style character decoding on load or hover.
* **Circular Text Path:** Text curved along a spinning circular path.
* **Gradient Stroke Animation:** Outlined text with a gradient continuously running along the stroke.
* **Kinetic Typography Grid:** A grid of letters dodging or rotating away from the cursor.

### Micro-Interactions & Effects
* **Particle Explosion Button:** CTAs that shatter into particles upon success.
* **Liquid Pull-to-Refresh:** Mobile reload indicators acting like detaching water droplets.
* **Skeleton Shimmer:** Shifting light reflections moving across placeholder boxes.
* **Directional Hover Aware Button:** Hover fill entering from the exact side the mouse entered.
* **Ripple Click Effect:** Visual waves rippling precisely from the click coordinates.
* **Animated SVG Line Drawing:** Vectors that draw their own contours in real-time.
* **Mesh Gradient Background:** Organic, lava-lamp-like animated color blobs.
* **Lens Blur Depth:** Dynamic focus blurring background UI layers to highlight a foreground action.

## 10. THE "MOTION-ENGINE" BENTO PARADIGM
When generating modern SaaS dashboards or feature sections, you MUST utilize the following "Bento 2.0" architecture and motion philosophy. This goes beyond static cards and enforces a "Vercel-core meets Dribbble-clean" aesthetic heavily reliant on perpetual physics.

### A. Core Design Philosophy
* **Aesthetic:** High-end, minimal, and functional.
* **Palette:** Background in `#f9fafb`. Cards are pure white (`#ffffff`) with a 1px border of `border-slate-200/50`.
* **Surfaces:** Use `rounded-[2.5rem]` for all major containers. Apply a "diffusion shadow" (a very light, wide-spreading shadow, e.g., `shadow-[0_20px_40px_-15px_rgba(0,0,0,0.05)]`) to create depth without clutter.
* **Typography:** Strict `Geist`, `Satoshi`, or `Cabinet Grotesk` font stack. Use subtle tracking (`tracking-tight`) for headers.
* **Labels:** Titles and descriptions must be placed **outside and below** the cards to maintain a clean, gallery-style presentation.
* **Pixel-Perfection:** Use generous `p-8` or `p-10` padding inside cards.

### B. The Animation Engine Specs (Perpetual Motion)
All cards must contain **"Perpetual Micro-Interactions."** Use the following Framer Motion principles:
* **Spring Physics:** No linear easing. Use `type: "spring", stiffness: 100, damping: 20` for a premium, weighty feel.
* **Layout Transitions:** Heavily utilize the `layout` and `layoutId` props to ensure smooth re-ordering, resizing, and shared element state transitions.
* **Infinite Loops:** Every card must have an "Active State" that loops infinitely (Pulse, Typewriter, Float, or Carousel) to ensure the dashboard feels "alive".
* **Performance:** Wrap dynamic lists in `<AnimatePresence>` and optimize for 60fps. **PERFORMANCE CRITICAL:** Any perpetual motion or infinite loop MUST be memoized (`React.memo`) and completely isolated in its own microscopic leaf component. Never trigger re-renders in the parent layout.

### C. The 5-Card Archetypes (Micro-Animation Specs)
Implement these specific micro-animations when constructing Bento grids (e.g., Row 1: 3 cols | Row 2: 2 cols split 70/30):
1. **The Intelligent List:** A vertical stack of items with an infinite auto-sorting loop. Items swap positions using `layoutId`, simulating an AI prioritizing tasks in real-time.
2. **The Command Input:** A search/AI bar with a multi-step Typewriter Effect. It cycles through complex prompts, including a blinking cursor and a "processing" state with a shimmering loading gradient.
3. **The Live Status:** A scheduling interface with "breathing" status indicators. Include a pop-up notification badge that emerges with an "Overshoot" spring effect, stays for 3 seconds, and vanishes.
4. **The Wide Data Stream:** A horizontal "Infinite Carousel" of data cards or metrics. Ensure the loop is seamless (using `x: ["0%", "-100%"]`) with a speed that feels effortless.
5. **The Contextual UI (Focus Mode):** A document view that animates a staggered highlight of a text block, followed by a "Float-in" of a floating action toolbar with micro-icons.

## 11. FINAL PRE-FLIGHT CHECK
Evaluate your code against this matrix before outputting. This is the **last** filter you apply to your logic.
- [ ] Is global state used appropriately to avoid deep prop-drilling rather than arbitrarily?
- [ ] Is mobile layout collapse (`w-full`, `px-4`, `max-w-7xl mx-auto`) guaranteed for high-variance designs?
- [ ] Do full-height sections safely use `min-h-[100dvh]` instead of the bugged `h-screen`?
- [ ] Do `useEffect` animations contain strict cleanup functions?
- [ ] Are empty, loading, and error states provided?
- [ ] Are cards omitted in favor of spacing where possible?
- [ ] Did you strictly isolate CPU-heavy perpetual animations in their own Client Components?
- [ ] Do all `<input>`, `<textarea>`, and `<select>` elements use `text-base` (16px) to prevent iOS auto-zoom?
- [ ] Do fixed bottom elements (navbars, FABs, CTAs) include `pb-[env(safe-area-inset-bottom)]`?
- [ ] Do all interactive elements meet the 44×44px minimum tap target?
- [ ] Is `overflow-x-hidden` applied to the root layout to prevent accidental horizontal scroll?
- [ ] Is body scroll locked while any modal, drawer, or bottom sheet is open?
- [ ] Are all entrance animations wrapped with `useReducedMotion()` and skipped when preferred?
- [ ] Are desktop-only interactions (hover effects, magnetic cursors) disabled or absent on touch viewports?
