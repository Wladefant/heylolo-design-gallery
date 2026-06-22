# Voice Picker redesign — "Choose LoLo's voice"

Standalone interactive prototype: `docs/design/voice-picker-redesign.html`
Design-only. No production code touched. Light-only (no dark mode) — three live STATES shown side by side instead.

## What it is

The child-facing "Choose LoLo's voice" settings screen. Voices stay **grouped by their model**
(Chirp 3 HD / ElevenLabs), each row is **tap-to-preview** (sample on tap), and the pick is confirmed
with a calm teal check. The pale-teal mic disc is reused as a "now-previewing" hero at the top, so the
sample literally plays *on LoLo*.

Baseline mapped from `docs/_session/voice-picker-settings/PLAN.md`: two provider groups, each voice has a
`label` + `descriptor`, the liked default is the **Gemini-TTS / Chirp "Zephyr"** warm voice (`658b78f`,
memory `reference_liked_tts_voice.md`). Grouping + tap-to-preview interaction kept verbatim.

## Mobbin references (iOS)

- **Replika — voice list with personality descriptors** — [mobbin.com/screens/5fcfda7b-810a-49c3-8418-11787689a214](https://mobbin.com/screens/5fcfda7b-810a-49c3-8418-11787689a214) — grouped list, each row = play button + descriptor name + Select/Selected, the selected row gets an outlined highlight. **TOP REF.**
- **Replika — personality-card variant** — [mobbin.com/screens/01dd0757-5e04-4005-aa09-679224b9b915](https://mobbin.com/screens/01dd0757-5e04-4005-aa09-679224b9b915) — "Caring / Confident / Calm" accent-labelled rows; the descriptor-as-personality idea.
- **Beside — voice library, two-word descriptors** — [mobbin.com/screens/6de42833-62c5-4c99-9e84-ba558fd8b5b6](https://mobbin.com/screens/6de42833-62c5-4c99-9e84-ba558fd8b5b6) — clean name + short descriptor ("Energized, Supportive") + radio on the right. The calm, scannable row.
- **Medium — grouped sections + selected check** — [mobbin.com/screens/59421b77-ced4-47e7-840d-68467fa2b811](https://mobbin.com/screens/59421b77-ced4-47e7-840d-68467fa2b811) — uppercase section headers (FEATURED / ENGLISH) with a single checkmark on the chosen item. Maps to our two-group structure.
- **WhatsApp / Meta AI — hero disc + swappable identity** — [mobbin.com/screens/82b2d2c1-b4e8-4731-bdd3-d64b9273c3c2](https://mobbin.com/screens/82b2d2c1-b4e8-4731-bdd3-d64b9273c3c2) — a voice "orb" at the top with the current voice name below; confirms our disc-as-hero pattern (we keep teal, not their purple gradient).
- **CapWords — play-circle leading icon + Done pill** — [mobbin.com/screens/d8d264af-654a-4488-ace1-b50d2c325782](https://mobbin.com/screens/d8d264af-654a-4488-ace1-b50d2c325782) — play affordance lives *in* the row as a circular chip.

## The 2–3 redesign ideas

1. **Disc-as-hero "now previewing" (from WhatsApp/Meta AI orb + Replika).**
   The signature pale-teal mic disc sits at the top and *is* the preview surface: idle = a calm dot
   cluster; on tap it wakes (scale/glow per `listening-avatar.tsx`) with an equalizer and shows
   "Playing sample · <name>". The child hears the voice *as LoLo*, not from a generic speaker icon.
   This keeps the disc as the brand hero the design skill calls for.

2. **Model groups with tap-to-preview rows (from Replika + Medium + Beside).**
   Two uppercase group heads (Chirp 3 HD / ElevenLabs) with a small teal pip. Each voice is a soft
   rounded card: a pale-teal play **chip** that flips to a pause + mini-waveform while previewing,
   the name, a one-line personality descriptor, and a teal selected-check on the right. Selection
   (border + check) and preview (waveform tag) are **separate** states so a child can sample several
   before committing — exactly the Replika "Select vs Selected" split, restyled warm/teal.

3. **"Kids love this" liked-voice marker (from Replika's Selected emphasis, our Zephyr fact).**
   Zephyr (the warm Gemini-TTS voice the operator actually liked) carries a small coral
   "kids love this" pill on the Chirp 3 HD group head — a single warm accent, not a recolor of the
   whole row. Gives the child a gentle default without a hard "recommended" banner.

## Tokens used (real — `src/shared/config/palette.ts`, `fonts.ts`)

- **Teal spine:** BlueLagoon `#037B83` (strong ink/headings, save button base, play glyph), BlueChill
  `#0E8B93` (selected check + border, eq bars), FountainBlue `#5EBCC1` (group pip, previewing accent),
  Gothic `#6B9EA8` / SmaltBlue `#5A8994` (secondary text), Ziggurat `#C2DDDE` (card borders).
- **Light surfaces:** AquaSpring `#E8F4F8`, BlackSqueeze `#E9F6F6` (idle save bar), LightGrayishCyan
  `#F5FAFB` (screen base). No dark theme anywhere.
- **The mic disc — reused verbatim:** gradient `#CDF0F2 → #DEF4F6 → #F5FBFB`, `6px` white-ish border
  `rgba(255,255,255,.62)`, `#037B83` soft shadow, the wake-bloom on preview.
- **Warm accent — sparingly:** BurntSienna `#E36843` only on the single "kids love this" pill.
- **Fonts:** Nunito (display + body), DM Sans (status bar numerals/signal). Google-Fonts equivalents.
- Custom ink `--ink #0C3F44` (darkened BlueLagoon) + `--ink-dim #4E7C82` for AA-legible body/secondary
  text on the pale-teal surface — secondary text is the "dim" ink, never the faintest, per the
  legibility rule.

## Kept vs changed

**Kept (from the real baseline / brand):**
- Two provider groups + tap-to-preview (PLAN.md F3/F4); Zephyr as the liked default.
- The pale-teal disc as the hero element; light-only teal tide-pool palette.
- Selection vs preview as distinct row states (matches the planned `voice-card.tsx` `isSelected` +
  optional preview button).

**Changed / added (proposed):**
- Promoted the disc from a separate listening screen into the picker as the live preview surface.
- Personality **descriptors** surfaced inline on every row (the catalog already carries `descriptor`;
  the planned card only had label + descriptor — we make the descriptor a first-class personality line).
- A single coral "kids love this" liked-voice marker on the Chirp group (new affordance for a gentle
  default; cheap to wire — it's just a flag on the catalog entry).
- A muted/active **save button** that names the chosen voice ("Save & use Sulafat") and stays inert
  ("This is the one") until the child picks something different from the stored voice.

## Self-check (forbidden AI tells)

No arrows/chevrons (back glyph is a plain caret-stroke, not a UI chevron-as-decoration). No colored
left-edge rails. No purple/teal-green gradient clichés, no gradient text. No emoji — inline stroke SVG
only. No radial-mesh + grid + grain background (clean pale-teal surface). No neon `box-shadow:0 0` glow
(soft neutral teal elevation only). Generic fonts avoided (Nunito + DM Sans). Color carries meaning on
the one owning element (the check, the one liked pill), not washed across the container.
