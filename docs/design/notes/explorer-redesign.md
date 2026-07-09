# Explorer mode — redesign notes

Prototype: `docs/design/explorer-redesign.html` (drops into the gallery — same phone frame + tokens as `home-redesign.html` / `voice-mode-redesign.html`).

Explorer is the camera mode: the kid points the phone at an object and LoLo talks about what it sees. This redesign answers the owner's two exact asks:

1. **Zoom** on the camera view (pinch + slider + buttons).
2. **Make LoLo's reaction unmistakable** — a distinct visual "moment" the instant LoLo locks onto an object, paired with a haptic cue (represented visually here as a ripple pulse + a "buzz" motion).

It is deliberately built as a **sibling of voice mode**: the same LoLo disc + voiceprint-halo, same tide-pool palette, same frosted-white chips. When LoLo speaks about an object, it looks and behaves like LoLo speaking in voice mode.

---

## Design tokens (lifted verbatim, not invented)

- Palette: BlueLagoon `#037B83`, BlueChill `#0E8B93`, Paradiso `#3C9094`, FountainBlue `#5EBCC1`, Anakiwa `#A2F3FF`, teal-deep `#16737A`, ink `#114e52` / `#28484c`, muted `#5b9296`. Explorer accent = coral `#E36843` on `#FFE6DE` (the Explorer tile color from home).
- Disc gradient: `radial-gradient(circle at 38% 32%, #F5FBFB, #DEF4F6 46%, #CDF0F2 100%)` — used for the LoLo disc.
- Fonts: Nunito (400–900) for everything, DM Sans (500/700) for the status-bar clock + zoom readout numerals.
- Phone frame: `344×730`, `border-radius:54px`, `#0c2f33` bezel, `padding:11px`; inner screen `border-radius:44px`. Status bar SVGs + home indicator copied from the two reference files.

---

## The camera viewport (no real camera)

The camera feed is simulated with a soft, **light, warm photographic placeholder**: a bright cream→peach room gradient, a soft window-glow blur, a wooden tabletop band, and a hero **red apple** SVG (the object LoLo detects) with a contact shadow. Kept intentionally bright so the screen stays LIGHT-ONLY — no dark camera chrome.

Overlaid, camera-viewfinder framing = four white translucent corner brackets (`.brackets`).

---

## Zoom interaction (ask #1)

Range **1.0× → 3.0×**, readout chip shows e.g. `2.0×`. Three input paths, all wired:

- **Vertical slider** on the right edge (`.ztrack` + draggable `.zthumb` + `.zfill`). Custom pointer-drag (not a rotated `<input range>`) so it styles cleanly: top = 3×, bottom = 1×.
- **`+` / `−` buttons** step ±0.2.
- **Pinch / scroll** over the viewport: two-finger `touchmove` scales relative to the pinch-start distance; `wheel` (trackpad pinch / mouse wheel) also zooms. A **"Pinch to zoom"** hint chip auto-appears on boot and after Flip, then fades.

Mechanic: the scene is `transform: scale(zoom)` with `transform-origin: 50% 60%` — the same point the apple sits on — so the object stays anchored under the reticle at every zoom level (the reticle/ripple are fixed at 50%/60% and never drift off the object). "Flip" resets zoom to 1× (and re-flashes the pinch hint).

Demonstrating 1×→2×: five `+` taps (or a slider drag) visibly doubles the apple; the readout tracks it.

---

## Detection state machine (ask #2)

Four visible states, each selectable from the external control row, and cycle-able via **▸ Play the flow** (scanning → found → talking). `go(state)` is the single switchboard; it resets all transient layers first, then arms the target state.

### (a) SCANNING — "LoLo is looking"
Calm, not busy. A soft teal **scan line sweeps** top↔bottom (`@keyframes sweep`), a faint teal **wash** sits over the frame, and a dashed **roaming box** drifts around (`@keyframes roam`) as if hunting. Badge: **"Looking around…"** (with the live pip). LoLo bar reads *"Let me see what's here"* with animated dots. Halo mode `ambient` (low, slow).

### (b) FOUND IT — the key moment (must be unmistakable)
Four cues fire **simultaneously** so the reaction is impossible to miss:
1. **Focus reticle snaps** onto the object — teal AF-style corner brackets animate from 1.4× + faded to a locked 1.0× with a slight overshoot (`@keyframes snap`, spring easing). This is the "camera locked on" read.
2. **Teal ripple pulse** radiates out from the object — three concentric rings expand + fade from the reticle center (`.ripplewrap.fire`, staggered 0 / 130 / 260 ms). This is the visual twin of the haptic tap.
3. **Detection card buzzes** — the LoLo bar does a quick horizontal **shimmy** (`@keyframes buzz`, ±3px, decaying) — this is the literal visual pairing point for a real device vibration (see haptic mapping below). Content swaps to **"I see a red apple!"** and the object thumbnail **pops** in (spring scale-in).
4. **LoLo's voiceprint-halo blooms** (halo mode `found`, high energy burst) + a soft whole-screen teal **flash** (`.flash.fire`).

Badge switches to **"Found it!"** (pip off — it's a resolved state, not a live search).

### (c) TALKING — LoLo explains
Halo mode `speak` (the voice-mode speech-sim energy). The badge hides (like voice mode's speaking state). The caption **streams** character-by-character into the LoLo bar with a blinking cursor (`streamCaption`): *"It's a red apple! Apples grow on tall trees. This one is shiny and red, which means it's ripe and ready for a big crunch."* High-contrast, calm, kid-friendly.

### (d) NOTHING FOUND — gentle empty state
No blame, no error-red. The scene **dims** (object fades to 14%, zoom resets to 1×) so it truly reads as "pointed at nothing," and a legible frosted card shows a search glyph + **"Point me at something!"** + *"Move me toward a toy, a plant, or your snack — I'll take a good look."* Badge: **"Nothing yet"**.

---

## How "the reaction is clear now" works (the whole point)

Before, LoLo reacting to an object was ambiguous. Now the FOUND beat stacks **five** channels at once — spatial (reticle locks *on the object*), motion (ripple out of the object), tactile (card buzz + real vibration), audio-visual (halo bloom), and text (named object + thumbnail). Any one of them signals "LoLo sees it"; together they make the moment a genuine, celebratory *event* rather than a quiet caption change. A pre-reader gets it from the reticle + ripple + buzz alone.

---

## Haptics → detection mapping (how the app pairs a real vibration)

The prototype represents haptics **visually** because HTML can't buzz — but the mapping is explicit so the RN app can wire the real thing:

| Detection event | Visual cue (prototype) | Real haptic (app) |
|---|---|---|
| **Object locked (FOUND)** | reticle snap + teal ripple + **card buzz shimmy** + screen flash | one crisp **medium impact** (e.g. `Haptics.impactAsync(Medium)`) fired the instant the vision model returns a confident detection |
| LoLo starts talking | halo bloom | optional light **selection** tick as speech begins |
| Nothing found | (no buzz — calm) | **no** haptic (never punish an empty frame) |

The card's `buzz` shimmy is the on-screen anchor: fire the device vibration on the exact same frame the `.buzz` class is added, so the felt and seen cues land together. The ripple ring timing (~900 ms) matches a single tap-and-settle, not a long rumble.

---

## Controls / interaction surface

- **Point** (center hero, teal target glyph): runs the full demo — scanning → found → talking. In-app this is "capture / look now."
- **Ask LoLo** (left, mic glyph): "ask LoLo about what it sees" — jumps straight to LoLo talking about the current object.
- **Flip** (right): flips the camera; here it resets zoom + re-hints pinch.

---

## Constraints honored

- **LIGHT-ONLY** — bright warm scene, frosted-white chips, no dark camera UI.
- **No emoji-as-icons, no decorative chevrons/arrows, no gradient-text** — all glyphs are inline SVG (mic, target, zoom ±, search, flip, LoLo face, apple).
- **`prefers-reduced-motion`** — respected: sweep/roam/ripple/buzz/halo animations collapse to ~0ms and single iterations; the caption renders instantly instead of typing; the reticle appears locked without the snap; Play/Point timing compresses. States still read; nothing flashes or jitters.
- Matches the gallery phone frame + tokens, so it can be listed alongside the other redesign screens.

---

## Verified in-browser (Chrome, served over http)

Scanning, Found it, LoLo talks, Nothing found, and the +/slider zoom (1.0×→2.0×) were each exercised and screenshotted; **"Play the flow"** correctly chains scanning → found → talking; **no console errors** on load or interaction.

## Open questions / future
- Real Explorer uses a live vision model — the "confident detection" threshold that triggers the FOUND haptic needs a real confidence cutoff (avoid buzzing on every frame / flicker). Debounce so the reticle doesn't chatter between near-misses.
- Multi-object frames: this prototype locks one object. If the model returns several, decide between "lock the most confident" vs. a tappable list.
- Consider a subtle sustained low haptic while SCANNING is *not* recommended (fatiguing) — keep haptics reserved for the FOUND event only.
