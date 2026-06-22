# Home (voice-first) redesign — design notes

Prototype: [`docs/design/home-voice-first-redesign.html`](../home-voice-first-redesign.html)
Surface: HeyLolo / elumi-kids **Home (voice-first) landing screen** — light-only, warm-teal "tide-pool" brand.
Real baseline: Figma Home **S1 `40001918:10195`** / **S2 `40001919:10360`** + cached screenshots
`docs/_session/home-voice-first-rebuild/screens/{s1-home-voice-first,s3-home-3button}.png`.

## Mobbin references (platform: ios)

| App | Why it informed the design | Link |
|---|---|---|
| Duolingo ABC | The exact "mic pip inside a soft pale-blue concentric-ring halo" — validates HeyLolo's existing disc-in-rings hero. | [screen](https://mobbin.com/screens/f6fb21c6-fd7d-42c3-9661-b555549584f1) |
| Meta AI | A single central voice **orb** that is the whole interaction; resting vs. active "talking" state on one element. | [screen](https://mobbin.com/screens/fca8709b-7a3a-4ce9-995d-3bd6677c5dd4) |
| Grok (voice) | Horizontal **mode row** with leading line-icons + "Start talking" label under it — kid-friendly category affordance. | [screen](https://mobbin.com/screens/129d9da3-0e18-4245-9076-84b4ab03e0da) |
| Wysa | Soft, **tinted rounded 2×2 tiles** that feel calm and friendly (not boxed-in cards) — the tile feel we want. | [screen](https://mobbin.com/screens/8311f38a-e0c7-4e1a-9fbe-c01018777e00) |
| Duolingo | Big friendly **"Tap to Speak"** capsule — labeling the speak action plainly for kids. | [screen](https://mobbin.com/screens/ed122717-5018-4352-9b1d-673adcf0ecbe) |
| NAVER (voice) | Central voice ring + tidy "say-this" suggestion rows — informed the calm-suggestion direction (used lightly). | [screen](https://mobbin.com/screens/cef82af1-9cb6-4e73-aa40-461a48e44af2) |

## The 2–3 ideas (each tied to a ref)

1. **Disc-as-anchor, calm then alive** — the pale-teal mic disc keeps the SAME position in every state;
   only its energy changes. Rest: white rings breathe on opacity, mascot drifts. Tap: spring to
   `scale 1.07`, VU bars pulse inside the disc, ripples bloom outward, "Listening" badge floats above,
   and the mode grid recedes to `opacity .5` to focus attention. *(from **Duolingo ABC** pip-in-halo +
   **Meta AI** single-orb wake.)* Honors the a11y rule: motion is mostly opacity; the one scale step is
   small and would drop under reduce-motion.

2. **Soft tinted tiles, not heavy cards** — four mode tiles on white, each with a whisper-tinted icon
   blob (teal for explore/numbers, a warm-coral hint for stories), low-contrast borders, generous radius.
   Calm and rounded rather than boxed/bento. *(from **Wysa** soft 2×2 + **Grok** leading-icon mode row.)*

3. **One dock, two depths** — resting home shows just the disc + "or type." The richer "Talk or explore"
   state fans the dock to **+ · disc · Explorer(binoculars)** so attach-a-photo, talk, and live-voice
   (Gemini) sit side by side without ever burying the disc. The "or type" affordance and a plain
   "Tap to speak"-style label keep the entry obvious. *(dock structure from real Figma S3 `40001982:11446/12605/12610`;
   speak-label clarity from **Duolingo**.)*

## Tokens used (verified — `src/shared/config/palette.ts`, `listening-avatar.tsx`)

- **Disc gradient** (verbatim): `#CDF0F2 → #DEF4F6 → #F5FBFB`, radial light-from-top-left; 6px
  `rgba(255,255,255,.55)` border; shadow `#037B83` at low opacity (`0 12 24 -2 rgba(3,123,131,.16)`),
  blooming to `.4` on wake. White breathe-rings `rgba(255,255,255,.7)`.
- **Teal spine**: BlueLagoon `#037B83` (strong ink, disc shadow, primary glyph), BlueChill `#0E8B93`,
  Paradiso `#3C9094`, FountainBlue `#5EBCC1` (mascot cap), Gothic `#6B9EA8`, SmaltBlue `#5A8994`
  (secondary text / "or type"), Ziggurat / Anakiwa `#A2F3FF` / Onahau `#CAF8FF` (pale aqua).
- **Light surfaces**: AquaSpring `#E8F4F8`, BlackSqueeze `#E9F6F6`, LightGrayishCyan `#F5FAFB` — used for
  the screen wash and tile icon blobs (flat washes, NOT atmospheric mesh).
- **Warm accents (sparingly)**: BurntSienna `#E36843` (stories icon, listening pip), Pizazz `#FF9500`
  (propeller cap bobble). Manatee `#8F9098` for placeholder grey.
- **Fonts**: Nunito (greeting, labels, headings) + DM Sans (status bar, body) via Google Fonts.

## Kept vs. changed (vs. real baseline)

**Kept (true to baseline + brand):**
- Layout order: header (back + history) → floating cloud mascot → "Hi Paul! I'm HeyLolo" greeting →
  2×2 mode grid → disc + "or type" dock at bottom.
- The disc as hero, its exact gradient/border/shadow tokens, and the white concentric rings.
- The S3 3-button dock (`+ · disc · Explorer`), behaviors unchanged (disc = record/`handleStartVoice`,
  binoculars = live voice/`handleEnterVoiceMode`, plus = attach).
- Light-only, warm-teal, soft/rounded/calm. No dark variant.

**Changed (the redesign proposals):**
- Made the **listening state explicit and reactive** — VU bars + ripple bloom + "Listening" badge +
  grid recede — rather than a static disc. (Baseline screenshots only show the resting disc.)
- Added **whisper-tinted icon blobs + a one-line sub-label** to each tile for warmth and scannability,
  and gave the grid a fourth "Explore the world" tile reading as the Explorer entry alongside the dock.
- Greeting copy shifts per state ("I'm listening…" / "Tap to talk, or wander with Explorer").
- Tightened the dock so the disc stays centred and the mini buttons read as frosted teal discs.

## Self-check (forbidden-ai-tells)

No arrow/chevron glyphs, no left-edge accent rails, no purple→blue/teal-green gradient cliché, no
gradient text, no `box-shadow: 0 0` neon glow, no atmospheric radial-mesh/grid/grain background (screen
bg is a flat aqua wash; the one `radial-gradient` is the disc's real signature token). Fonts are
Nunito + DM Sans (no Inter/Roboto/system). No emoji — all icons are inline stroke SVG.

## Status

Design prototype only — production code untouched. `docs/design/index.html` not edited. Not ported.
Awaiting operator review before any worktree port (pure reskin: keep all audio/logic).
