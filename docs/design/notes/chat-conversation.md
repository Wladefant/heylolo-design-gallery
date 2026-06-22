# Chat Conversation (text mode) — redesign notes

Prototype: [`docs/design/chat-conversation-redesign.html`](../chat-conversation-redesign.html)
Surface: HeyLolo's typed-chat conversation screen — the `voiceFirst` PromptInput variant
(`src/widgets/conversation`, `src/widgets/prompt-input`). Light-only, tide-pool brand.

## Mobbin references (platform: ios)

Distilled the dominant pattern from these screens before designing:

- [Tolan — companion chat over a soft gradient sky](https://mobbin.com/screens/eaeaf55c-3199-43ac-85bb-997d441d6bf7) — **the closest match to HeyLolo's DNA.** Friendly character floating beside white assistant bubbles on a calm gradient background, tinted right-aligned user bubbles, circular send. Warm, not loud.
- [Tolan — character + bubbles, continuation](https://mobbin.com/screens/00d782d4-2659-41d7-85dd-72ccfb7806d8) — confirms the floating companion + white-card pattern holds across the thread.
- [ChatGPT — "explain this to me if I were a child" with kid-friendly answer](https://mobbin.com/screens/e8921d53-d04c-447f-a285-f3ae196ff278) — clean assistant copy, no avatar gutter, rounded composer with a circular send.
- [ChatGPT — assistant "typing" = a single animated dot](https://mobbin.com/screens/ca57f230-d5d9-4be3-a762-02f0ea1e6c3a) — the minimal, chrome-light thinking indicator (no full "typing…" pill text).
- [Meta AI — soft-tinted assistant bubbles](https://mobbin.com/screens/a0459f93-6147-46a4-a130-fe10380f1752) — distinct on-brand tint for assistant vs user; composer is one calm rounded field.
- [MacroFactor — minimal AI chat, rounded composer + circular send](https://mobbin.com/screens/f29d13d7-0db3-4d31-8e37-ba2cdba29969) — reference for the single-row composer pill with a clean send disc (no arrow glyph).

## The 2–3 ideas (and the ref each came from)

1. **Floating cloud-LoLo companion under the thread, not a per-bubble avatar gutter.**
   (from **Tolan**) The character sits bottom-left between the last message and the
   composer; it dims/"concentrates" while LoLo is thinking and brightens when the reply
   lands. Keeps the white assistant cards full-width and uncluttered, and gives the kid a
   face to talk to — matches the real `<VoiceModeAvatar>` floating pattern in
   `conversation.tsx`.

2. **Calm, chrome-light thinking indicator — three dots that wave, in a barely-there light
   chip.** (from **ChatGPT typing-dot** + the real `thinking-dots.tsx`) No "typing…" text,
   no heavy pill. Three `SmaltBlue` dots pulse opacity + a 2px lift, staggered 150ms, on a
   `rgba(255,255,255,.62)` chip — exactly Figma S4 (`40001826:8657`) but honoring the real
   component's restraint.

3. **Distinct on-brand bubble tints + a minimal white composer pill that swaps voice→send.**
   (from **Meta AI** tint pairing + **MacroFactor** composer) User = teal `BlueChill` fill,
   white text, right-aligned with a small bottom-right radius tail; assistant = white card,
   dark `Scorpion` text, soft directional shadow, bold markdown (e.g. "spinnerets"). The
   composer is a single white pill: `+` · "Ask me anything" · then `[mic] [voice-disc]` when
   empty, collapsing to a single paper-plane **send** the moment text is present (matches the
   real `keepMicWhenTyping` / home-typing collapse). The send glyph is a rounded paper-plane,
   **not** a bare arrow.

## Tokens used (all from `src/shared/config/palette.ts` + `fonts.ts`)

- **Background gradient** (S3–S6): soft mint→teal vertical wash `#d4ecef → #e3f1f0 → #ecf5f1`
  — flat linear, no radial mesh. Page chrome uses an even calmer `#eef7f8 → #e6f2f4`.
- **User bubble:** `BlueChill #0E8B93` fill, `White` text.
- **Assistant bubble:** `White` card, `Scorpion #595959` body, `Abbey #494A50` bold; shadow
  soft + directional (`0 6px 18px -10px rgba(3,123,131,.28)`), never a `0 0` glow.
- **Thinking dots:** `SmaltBlue #5A8994` on a `rgba(255,255,255,.62)` chip.
- **Composer send glyph:** `SilverChalice #B2B2B2` when empty → `BlueChill #0E8B93` when ready.
- **Voice-mode disc:** the real listening-disc radial gradient `#CDF0F2 → #DEF4F6 → #F5FBFB`
  with a `rgba(255,255,255,.7)` border (`listening-avatar.tsx`).
- **Placeholder:** `Manatee #8F9098`. **Warm accent:** `BurntSienna #E36843` only on the
  LoLo hat pom + cheek blush (sparingly).
- **Fonts:** Nunito (body/bubbles), DM Sans (status bar, captions/numerals).

## Kept vs changed

**Kept (real baseline, unchanged contract):**
- voiceFirst composer layout: `+` left, text field, `[mic][voice-disc]` right; typing collapses to send.
- Bubble roles + colors (`BlueChill` user / `White` assistant), 20px radius, asymmetric tail.
- Bare-ish thinking indicator (no "typing…" pill text), floating companion avatar, markdown bold in replies.
- Trimmed floating header: round back button + round history button, no title.
- "Ask me anything" placeholder copy; spider/"spinnerets" sample content lifted straight from Figma S5.

**Changed / proposed polish:**
- Background promoted to an explicit, named mint→teal linear gradient across the whole thread
  (the plan's §8 was "audit + swap"; this pins the stops).
- Thinking indicator given a soft light chip + a small vertical lift on the dot pulse so it
  reads as "alive" without chrome — a middle ground between the bare dots in code and the
  faint pill shown in the S4 Figma export.
- Companion avatar now reacts to state (dims while thinking, brightens on reply) — a small
  bit of "calm by default, alive on interaction" the static Figma frame can't show.
- Top scroll-fade pinned as a real gradient overlay (plan §S5 wanted it; here it's concrete).

## Forbidden-tells self-check (passed)

No arrow/chevron glyphs in any link/CTA/button (send = paper-plane icon). No atmospheric
radial-mesh page background (flat linear washes). No `box-shadow: 0 0` neon glows — all
shadows are soft directional elevation. No left-edge accent rails. No gradient text. No
emoji (cloud-LoLo + icons are inline stroke/shape SVG). Fonts are Nunito + DM Sans only.
The three `radial-gradient` uses are legitimate object fills: the cloud body, its tufts, and
the token-specified mic-disc — none are page backgrounds.

## Not done (out of scope for this prototype)

- Image bubbles / generated-image "drawing…" placeholder, attachment preview, voice-mode
  pill + listening surface, history sheet. This prototype is the text-mode thread + composer
  across the three text states (waiting, responded, typing follow-up) only.
