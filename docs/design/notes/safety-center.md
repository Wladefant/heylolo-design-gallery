# Safety Center — design notes

Prototype: [`docs/design/safety-center-redesign.html`](../safety-center-redesign.html)
Surface: HeyLoLo **parent** area — the "Safety Center", the screen that makes child-safety
visible and actionable so a parent trusts the product.
Theme: **light-only** (this app has no dark mode). One phone, two switchable banner states.
Maps to feature **F30** (owner wants proof the safety-alert system is real) and to backend
roadmap issues **ElumiAI/elumi-kids#66** (server-side LLM safety pipeline) and
**ElumiAI/elumi-kids#72** (FCM push to parent on a safety flag).

## The states

Switchable live via the dev toggle under the frame (`Active alert` / `All clear`) — the owner can
preview both without editing anything. `data-state` on `.screen` flips which banner shows.

1. **Active alert** (`data-state="alert"`, default). A **measured coral** heads-up card — warm,
   not a red panic:
   - White rounded shield-alert glyph + headline **"LoLo noticed something"** + timing
     **"12 min ago · in Chat mode"** (DM Sans) + a **High** severity pill.
   - A white inset panel with the **redacted-safe summary** of what was said
     ("Paul asked *where kids can buy cigarettes*, and pushed a little when LoLo said no") — a
     calm paraphrase, never the raw disturbing text verbatim.
   - **What LoLo did in the moment**, separated by a hairline: a green shield-check + "In the
     moment, LoLo **paused and gently redirected** … steered the chat back to something lighter."
     This is the reassurance beat — the parent sees the child was already protected.
   - Three actions: **Transcript** (ghost) · **Call Paul** (ghost) · **Reviewed** (primary coral).

2. **All clear** (`data-state="clear"`). A white card, green shield-check badge,
   **"All good this week — no safety flags for Paul. LoLo is listening in the background and will
   let you know the moment anything comes up."** Reassuring, not empty/cold. Green here (not coral)
   because coral is reserved strictly for concern.

Both states share the rest of the scroll: history, severity model, topics, explainer.

## Severity + notify model (the legend IS the push setting)

Rendered as the **"How LoLo reaches you"** card — the severity legend doubles as the delivery
setting, so one control teaches the model and configures the push (issue **#72**):

| Severity | Dot | What it is | Delivery | Control |
|---|---|---|---|---|
| **High** | solid coral `#E0763F` | self-harm, danger, strangers, substances | **Instant push** | locked ON (non-disableable) |
| **Medium** | soft coral `#E9A472` | sadness, conflict, worries | **Daily digest** | toggle (default ON) |
| **Low** | teal-grey `#9DC3C0` | mild curiosity | **Logged only**, no notify | toggle (default ON) |

The severity ramp reads left-to-right as loud→calm entirely inside the coral family for the
serious tiers, then neutral teal-grey for Low — so color always carries the safety meaning, never
decoration.

## Alert settings — safe by default

**"Always notify about"** card lists the trigger topics, each a coral-tinted inline glyph:

- **Self-harm & distress** — locked ON (lock chip "Always on")
- **Violence & weapons** — locked ON
- **Meeting strangers** — locked ON
- **Danger & location** — locked ON
- **Adult substances** (alcohol/smoking/vaping) — toggle, default ON (the one a parent may adjust)

The four most serious categories render as **locked** toggles (muted teal `#A9CFD1`, knob pinned
right, `cursor:default`, a lock glyph + "Always on") — they cannot be turned off. This is the
safe-by-default stance: a parent can add nuance (adult substances) but can never accidentally
silence self-harm/violence/strangers/danger.

## History / timeline

**"Earlier flags"** card — divider-separated rows, each: a severity **dot**, the topic
(`Felt left out by a friend`), a DM-Sans meta line (`Sun, Jun 29 · Medium · sad mood`), and a
status pill — **Reviewed** (green) or **Open** (coral). Gives the parent a scannable audit trail so
the safety system feels continuous and real, not a one-off popup.

## Trust rationale (why it's built this way)

This is the screen that makes a parent trust the product, so every choice leans
**reassuring, non-alarmist, trustworthy**:

- **Measured coral, never red.** Coral `#E0763F` is warm, not alarm-red. The banner says "noticed
  something" / "would like your eyes on it" energy, not "EMERGENCY".
- **Show the save, not just the scare.** Every alert leads with *what LoLo already did* ("paused and
  gently redirected") — the parent learns the child was protected in real time, before they even
  saw the notification.
- **Redacted-safe summaries.** The parent gets a calm paraphrase, not the raw disturbing string —
  enough to act, not enough to alarm or re-expose.
- **Safe-by-default, can't-footgun.** The gravest categories are locked on. Trust comes from knowing
  the floor can't be lowered by accident.
- **An explainer that names the mechanism.** "How LoLo keeps Paul safe" states plainly that every
  message is checked as it happens by a safety model, escalation scales with severity, and nothing
  leaves the family — turning an invisible backend into a visible promise.

## Maps to backend issues

- **#66 (server-side LLM safety pipeline).** The explainer card ("checked, right as it happens, by a
  safety model") and the severity classification (High/Medium/Low + topic tags like
  `meeting strangers`, `sad mood`) are the parent-facing surface of #66's output. Each history row =
  one pipeline verdict; the topic tag = the pipeline's category; the severity = its risk score.
- **#72 (FCM push on safety flag).** The "How LoLo reaches you" card is the settings contract for
  #72: High → instant FCM push (locked on), Medium → batched daily digest, Low → no push (history
  only). The active-alert banner is what the parent lands on when they tap that push.

## Tokens used (verbatim from `parent-dashboard-2026.html`)

- **Coral / safety:** `--coral-bg #FFF0EA`, `--coral-ink #E0763F`, plus derived `--coral-deep
  #C25E2C`, `--coral-brown #b58468` (the same values the dashboard's "safety check" row uses).
  Severity soft-coral `#E9A472` / `#FBE0CC`, low teal-grey `#9DC3C0`.
- **Teal spine:** `--lagoon #037B83`, `--chill #0E8B93`, `--paradiso #3C9094`, `--fountain #5EBCC1`,
  `--teal-deep #15727A`, `--green #3FA079` (positive/all-clear + "Reviewed"), inks `--ink #114e52` /
  `--ink2 #28484c`, `--muted #5b9296` / `--muted2 #6c8b8e`.
- **Frame + chrome:** the exact 344×730 phone (radius 54, `#0c2f33`, same shadow), the 44-radius
  screen with the `#D6EFF5→#FBFDFD` gradient, the 9:41 DM-Sans status bar with the same three
  signal/wifi/battery SVGs, and the dark tab bar `#27393C` (active `#5FD6D6`, muted `#9fb4b5`,
  Home · HeyLoLo · Chat · Settings) — copied so it drops into the gallery seamlessly.
- **Fonts:** Nunito (display + body), **DM Sans** for all numerals/labels (`9:41`, `12 min ago`,
  dates, delivery captions).

## Self-check (house rules)

No emoji-as-icons; no decorative chevrons/arrows (the child-switcher keeps only the functional
people-swap glyph, same as the dashboard); no gradient text; no eyebrows. Every glyph is a simple
consistent stroke SVG (shield, shield-check, shield-alert, bell-less lock, phone, document, check,
heart, stop-octagon, people, location pin, prohibition circle). **Coral appears only in safety
semantics** — the alert banner, severity, topic-icon tints, and action buttons; nothing else in the
screen is coral. Light-only. `prefers-reduced-motion` shortens all transitions. Scroll fades top +
bottom via the mask-image (no hard cut-off under the header or behind the tab bar).
