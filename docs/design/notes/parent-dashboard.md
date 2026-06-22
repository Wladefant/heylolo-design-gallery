# Parent Dashboard (P1) — redesign notes

Prototype: [`docs/design/parent-dashboard-redesign.html`](../parent-dashboard-redesign.html)
Surface: HeyLolo parent area, **P1 Dashboard** (Figma `MxRZQxXrgV9hMDtTTVEvKW`, node `40001360:8384`).
Theme: **light-only** (this app has no dark mode). Three states shown side by side.
Scope note: **Monthly Review** (`40001273:6729`) and **Perspective Alignment** (`40001273:7046`) are
explicitly OUT of scope per `parent-dashboard-rebuild/PLAN.md` — they are not in the redesign.

## Mobbin references (platform: ios)

| Ref | App | Why it mattered |
|---|---|---|
| [Life360 — Family Driving Report](https://mobbin.com/screens/c7cfb334-13b5-48d3-b044-a22e8793e740) | Life360 | child-avatar picker row → a clean **stat triplet** (254 mi / 78 mph / 40 drives) → a **per-day bar/line series**. The canonical "headline weekly report for a chosen child" shape. |
| [Greenlight — Family home](https://mobbin.com/screens/7e97e03a-4c8e-47f3-8aab-2798ba5514ab) | Greenlight | closest brand fit: **teal-on-light**, soft mint cards, generous whitespace, child pill/avatar at top, calm and rounded — proof the tide-pool palette reads as a credible, grown-up parent surface. |
| [5 Minute Journal — Insights](https://mobbin.com/screens/bb8e19de-aaab-4c56-886e-46e6ee5dae4c) | 5 Minute Journal | **flat stat tiles**, big numerals with a small caption beneath, paired side by side, no boxes-in-boxes. Model for the "today" stats without card clutter. |
| [Greenlight — child account row](https://mobbin.com/screens/e7fee410-1ac8-4a1d-b228-013871bffda4) | Greenlight | per-child summary row + "show all" as a plain text link (no chevron decoration). Informed the "Recent chats" / "Daily summary" plain-link footers. |

Secondary look-alikes scanned but not used: GoHenry / Revolut Junior (too purple, allowance-led),
Tolan (dark cosmic — wrong theme), Forest/TIDE (donut+bar charts, but bland greys).

## The 2–3 ideas (and where each came from)

1. **Flat "weekly mood" hero, not a nested card stack.** *(Life360 report + Greenlight)* — the
   real screen wraps the line chart, the mood label and the "No Concerns" pill in nested rounded
   panels inside a panel. Redesign flattens it to ONE white card: cloud mood-face + "Paul's week /
   Happy and Calm" headline, a clean teal area-line chart with Mon–Sun labels and **today's point
   emphasized** (filled dot + soft halo), then the "No concerns detected" row separated by a hairline
   rule (not its own tinted box). Hierarchy comes from type + the single divider, not four borders.

2. **Paired flat stat tiles for Companion + Usage, with a live sparkline.** *(5 Minute Journal
   Insights)* — two equal tiles. *Current companion* shows the mode cloud-face + name ("Normal" /
   "Homework") and a plain "Switch mode" link; *Today's usage* shows the big DM-Sans numeral
   `1h 32m` over a 6-bar sparkline (today's bar in primary teal, prior days in soft Ziggurat) and a
   "Daily summary" link. State 2 demonstrates the companion switched to **Homework** (tile picks up a
   teal selection border, the only place color carries meaning) and usage trending up to `2h 14m`.

3. **Child switcher as a real bottom-sheet, opened from the pill — interactive.** *(Greenlight
   child rows)* — tapping the child pill (state 1) slides up a sheet: grab handle, "Switch child",
   three children (avatar initial on a per-child teal/coral gradient + name + `Age N` in DM Sans +
   radio). Selected row = soft teal fill + Fountain-Blue border + check; picking another swaps the
   selection and dismisses. A dashed "Add another child" row sits at the bottom. State 3 shows it
   statically; the leftmost device opens it live.

Supporting moves: "Recent prompts" is a divider-separated **list** (chat-bubble icon + text + a
pill "Ask" affordance that brightens on hover) instead of three stacked tinted boxes; insight gets
brand topic **chips** (Space / Curiosity / Science) instead of prose-only.

## Tokens used (real — `src/shared/config/palette.ts`, `fonts.ts`)

- **Teal spine:** BlueLagoon `#037B83` (primary text/strong), BlueChill `#0E8B93` (links, chart
  line, today-bar), Paradiso `#3C9094` (chips), FountainBlue `#5EBCC1` (selection border / accent
  pip / sparkline gradient), Gothic `#6B9EA8` (muted captions), SmaltBlue `#5A8994` (eyebrow labels),
  Ziggurat `#C2DDDE` (sparkline rest bars, grab handle), Anakiwa `#A2F3FF` (prompt hover fill).
- **Light surfaces:** AquaSpring `#E8F4F8`, BlackSqueeze `#E9F6F6`, LightGrayishCyan `#F5FAFB` (chips,
  icon tints, page gradient, dimmed-scrim base).
- **Warm accents (sparingly):** Pizazz `#FF9500` (insight lamp icon + one avatar gradient),
  BurntSienna `#E36843` (avatar gradient), Emerald `#34C759` (the "No concerns" positive check).
- **Brand mic-disc gradient** (verbatim from `listening-avatar.tsx`): `#CDF0F2 → #DEF4F6 → #F5FBFB`
  radial with the `rgba(255,255,255,.7)` border + soft `#037B83` shadow — reused for every cloud
  mood/companion face so the parent area stays visually tied to the voice UI.
- **Custom inks** (derived, not invented colors): `--ink #0C3A3E` (deep teal-ink, never pure black),
  `--ink-dim #45727A` (AA-readable secondary). Eyebrow labels use SmaltBlue/Gothic, never a
  low-contrast same-family grey (per the legibility rule).
- **Fonts:** Nunito (display + body) via Google Fonts; **DM Sans** for ALL numerals/labels
  (`9:41`, `1h 32m`, `Age 8`, eyebrow micro-labels, nav labels) — `font-feature-settings:'tnum'`.

## Kept vs changed

**Kept (faithful to baseline + brand):**
- All real content + IA: greeting + child pill, weekly mood ("Happy and Calm"), No Concerns,
  Current Companion, Today's Usage (`1h 32m`), Today's Insight, Recent Parent Prompts.
- Parent bottom nav order **Home · HeyLolo · Chat · Settings** (from `figma-assets.md`).
- The cloud-face mood/companion avatars and the signature pale-teal disc gradient.
- The warm, soft, rounded tide-pool feel — generous radii, soft teal-tinted shadows.

**Changed (the redesign):**
- **De-nested the cards.** Baseline stacks tinted boxes inside boxes; redesign uses one flat white
  card per concern with a hairline divider for the secondary row.
- **Killed the chevrons.** Baseline puts a `›` on every actionable row; per the hard ban there are
  **zero arrows/chevrons** — "Switch mode", "Daily summary", "Recent chats" are plain teal text
  links; the child pill keeps only a small functional down-caret (it literally opens a menu).
- **Color now carries meaning on one element**, not the container — the only colored border is the
  *selected* companion tile / *selected* child row; nothing else washes color across a whole panel.
- **Real interaction:** the child-switcher is a working bottom-sheet (open from pill → pick → dismiss),
  and a second state shows the companion-switched / usage-up variant, instead of one static mock.
- Added topic **chips** to the insight and a usage **sparkline**, both grounded in real tokens.
- Dropped Monthly Review + Perspective Alignment entirely (out of scope).

## Self-check (forbidden-ai-tells)
No arrow/chevron glyphs; no `box-shadow:0 0` neon; no `border-left` accent rail; no gradient text;
no Inter/Roboto/system fonts. The only `radial-gradient`s are (a) the sanctioned brand mic-disc
faces and (b) one soft top-left page bloom (brand "light from top-left" idiom) — not the banned
mesh+grid+grain atmosphere. Numerals are DM Sans (not mono-everything).
