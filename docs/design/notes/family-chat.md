# Family (kid) — design notes

Prototype: [`docs/design/family-chat-redesign.html`](../family-chat-redesign.html)
Surface: HeyLoLo **kid** area — the child's window to message and **call** the grown-ups a parent
approved (Mom, Dad, Grandma…), plus moderated family group chats.
Theme: **light-only** (this app has no dark mode). One phone, three switchable views via a dev toggle.
Maps to features **F27** (call parents from the chat) and **F31** (chats with parents + group chats),
and to the roadmap issues **ElumiAI/elumi-kids#67** (friend audio/video calls, WebRTC + LiveKit) and
**#74 / #75** (friend-request / contact-approval infra).

## The views

Switchable live via the dev toggle above the frame (`Family list` / `Group chat` / `In call`) — the
owner can preview all three without editing anything. The frame also has real in-flow taps so it reads
as a journey: tap the **Family** group row → chat → **answer** the ringing banner → active call →
**End** returns to chat.

### A · Family list (home for the Family tab)

- **Header** "Family" + a single functional **+** (round-btn) that means *ask a grown-up to add
  someone* — the child can request, only a parent approves.
- **Approval note** (the safety spine, top of scroll): a soft white card with a shield-check glyph —
  *"Only grown-ups a parent **approved** show up here. No strangers, ever."* Sets the frame before any
  contact is shown.
- **People** — warm contact rows: `avatar (initial + presence dot) · name · presence · CALL set`.
  - Presence: **online** = filled `--fountain` dot + "Online now" in `--paradiso`; **offline** =
    `--silver` dot + "Last seen 2h ago / yesterday" in `--manatee`. (No new green token invented —
    online reads on-brand in teal.)
  - **Per-contact CALL affordances** (this is F27, made prominent): two round 42px buttons on every
    row — **audio** (phone handset, `--aqua-spring` tint) and **video** (camcorder, `--black-sqz`
    tint). Big kid-friendly targets, always visible, no menu-diving to call a parent.
  - Four members shown: Mom (online), Dad (2h), Grandma (online), Grandpa (yesterday).
- **Family groups** — group rows: cluster avatar · name · last-message **preview with sender prefix**
  (`Mom: Home in 10 minutes!`) · time · unread **badge** (coral `--burnt`, only when > 0). Two groups:
  **Family** (badge 3) and **Grandparents**.
- **Tab bar**: Home · Chats · **Family** (active) · Settings — see open question below.

### B · Group conversation (the **Family** group)

Chose the **group** (not a 1:1) as the conversation example because it uniquely exercises the group
requirement — **sender name + avatar on every incoming bubble** — while the list already shows 1:1
people. Reuses the chat-surface bubble language verbatim.

- **Header**: functional back chevron · cluster avatar · **Family** title + roster subtitle
  (`Mom, Dad, Grandma · you`) · **two header call buttons** (audio + video) — F27 at the top of the
  thread, exactly where a kid reaches to "call everyone".
- **Incoming-call banner (F27, in-context)**: a white ribbon pinned under the header —
  `M avatar · "Mom is calling…" · pulsing live dot + "Family voice call" · Decline (coral) / Answer
  (fountain)`. Makes the ringing state legible without leaving the chat; **Answer** drives to view C.
- **Thread**: `--blue-chill` outgoing bubbles (me, right, bottom-right clipped), white incoming
  bubbles (bottom-left clipped, soft shadow) — identical to `chat-conversation-redesign.html`. Incoming
  bubbles carry a **28px sender avatar + colored sender name** (Mom coral, Dad lagoon, Grandma teal).
- **Voice-first content**: both a Dad→child and a child→Dad **voice-note bubble** (play triangle +
  waveform bars + duration) — voice notes are first-class, since many 4–12s can't type fluently.
- A **call system card** (`Family call · 6 min`) records that a call happened, tying calling into the
  timeline.
- **Composer** — deliberately voice-first: `+ photo · text input "Message your family" · prominent
  mic disc`. The **mic** is the big 46px gradient disc (the mic-disc token), not a small afterthought;
  text is available but the voice note is the hero.

### C · In-call (F27 active state)

Full-screen calm call surface (tab bar hidden): a **"Safe family call"** pill, a large 130px avatar
with an expanding halo ring, **Mom**, **"Connected · 02:14"** with a pulsing live dot, and a control
row — **Mute · End (coral) · Video**. This is the concrete F27 payoff: a child can be *in* a call with
a parent, one tap from either the list, the header, or the ringing banner.

## Safety / approval model (the whole point)

This is a child's window to their family, **not a social network** — every choice enforces that:

- **Parent-approved contacts only.** Nobody appears in the list who a parent hasn't approved. The
  approval note states it in kid words; the **+** is a *request*, never a self-serve add. This is the
  frontend face of **#74 / #75** (friend-request / approval infra) applied to family, not friends.
- **No open contact with strangers.** There is no search, no "add by username", no discovery — the
  only entry point to a new contact is a parent-mediated request.
- **Group chats are moderated / closed.** Family groups have a fixed, parent-defined roster (shown in
  the subtitle) — a child can't add members. Same closed-membership stance as the person list.
- **Calling stays inside the approved graph.** Every call affordance (row buttons, header buttons,
  ringing banner) only ever targets an approved family member — so F27 can never dial a stranger.
- **Warm, not clinical.** Coral is used *only* for warmth/attention semantics (unread badge, decline,
  end-call) — never as alarm. The tone is "the people who love you", not "manage your contacts".

## How calling maps to #67 (WebRTC + LiveKit)

- The **audio / video buttons** (list rows + chat header) are the *initiate-call* entry points #67
  needs — each would `createRoom` + place a call to the approved member (or the whole group for the
  header buttons).
- The **incoming-call banner** is the *ringing / invite-received* state — a LiveKit room invite
  surfaced in-app; **Answer** = join room, **Decline** = reject invite.
- The **in-call view** is the *connected room* — mute = local audio track toggle, video = publish a
  camera track (audio-first, video opt-in), end = leave/close room. The `02:14` timer is room
  duration.
- The **call system card** in the thread is the *call-ended* record — one row per completed room, so
  calls live alongside messages in the same history.
- #67 lists friend calls; **family** is the safer first slice of the same infra — same rooms, same
  tracks, but the callable graph is the parent-approved family list, so it can ship before open friend
  calling.

## Open questions

- **Where does "Family" live in the tab bar?** The prototype adds a 4th tab (Home · Chats · **Family**
  · Settings). Options: (a) dedicated **Family** tab as drawn; (b) fold family threads into the
  existing **Chats** tab (LoLo + family in one list, distinguished by a badge) to avoid a 4-tab bar;
  (c) a home-screen entry tile instead of a tab. Leaning (a) for discoverability + emotional weight
  ("my people" deserves a home), but (b) keeps the bar at three. Needs a product call.
- **Does the child initiate calls, or only accept?** Drawn as child-initiable (row/header buttons). A
  stricter posture: child can *message* freely but only *accept* calls a parent placed, or a
  quiet-hours / school-hours gate on outbound calls. Ties to the Companion mode settings (P4).
- **Voice notes vs live calls for the youngest kids.** For 4–6s, an async voice note may be the safer
  default than a live call; consider gating live calling by age.
- **Group-call ergonomics.** The header call buttons on a group start a *family* call — confirm the
  roster/participant model (everyone rings vs. a shared room others join) with #67.
- **Presence privacy.** Showing "last seen" for parents is friendly, but confirm parents are OK being
  shown as offline to the child (some may prefer presence hidden).

## Tokens used (verbatim from `chat-conversation-redesign.html`)

- **Teal spine:** `--blue-lagoon #037B83`, `--blue-chill #0E8B93` (outgoing bubble + accents),
  `--paradiso #3C9094`, `--fountain #5EBCC1` (**the chat-file value** — matches the chat surface, per
  the known `--fountain` drift; not "fixed" here), `--gothic #6B9EA8`, `--smalt #5A8994`,
  `--ziggurat #C2DDDE`, `--onahau #CAF8FF`, `--aqua-spring #E8F4F8`, `--black-sqz #E9F6F6`.
- **Warm / attention:** `--burnt #E36843` used *only* for the unread badge, decline, and end-call.
- **Ink / neutral:** `--scorpion #595959` (bubble body), `--abbey #494A50`, `--manatee #8F9098`
  (offline / placeholder), `--silver #B2B2B2` (offline dot).
- **Mic disc gradient:** `--disc-1 #CDF0F2` / `--disc-2 #DEF4F6` / `--disc-3 #F5FBFB` — the composer
  mic + call-avatar accents.
- **Frame + chrome:** the exact **320×660** phone (radius 46, `#1d2b2e`, same shadow), the 36-radius
  screen with the `#d4ecef→#ecf5f1` gradient, the 9:41 DM-Sans status bar with the same three
  signal/wifi/battery SVGs, the round-btn header buttons, and the **bubble** rules (max-width, 20px
  radius, 7px clipped corner, shadows) copied so it drops into the gallery beside the chat surface.
- **Tab bar:** the light `rgba(255,255,255,.86)` blur bar from `home-redesign.html` (active `#178187`
  on `#E0F1EE`, muted `#9bb5b8`), extended to Home · Chats · Family · Settings.
- **Fonts:** Nunito (display + body), **DM Sans** for all numerals/labels (`9:41`, timestamps,
  durations, `now`/`1h`).

## Self-check (house rules)

Light-only. No emoji-as-icons (the two literal emoji live **inside** message text — a kid typed them —
never as UI glyphs). No decorative chevrons/arrows — the only chevron is the functional **back**
control; the mode/nav uses stroke glyphs. No gradient text, no eyebrows (section labels "People" /
"Family groups" are functional list headers, normal case). Every glyph is a simple consistent stroke
SVG (phone, video, mic, send/play, shield, shield-check, people-cluster, plus). Kid-friendly touch
targets (42–64px call controls). Scroll fades via mask-image / a `.fade-top` (no hard cut-off under the
header or behind the tab bar). `prefers-reduced-motion` kills the halo/pulse and shortens transitions.
