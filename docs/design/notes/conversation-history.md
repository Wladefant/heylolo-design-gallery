# Conversation History (P2) + Full Transcript (P3) — redesign notes

Prototype: `docs/design/conversation-history-redesign.html`
Surface: parent-facing review screens. **Light-only**, warm teal "tide-pool". Two key states shown side by side in an iPhone frame: **State 1 — Conversation History** (filter chips + rich review cards) and **State 2 — Full Transcript** (read-only bubbles + HeyLolo's Suggestions). Interactive: filter chips narrow the list; tapping any history card re-contexts the transcript panel.

Design prototype only — no production code touched.

## Real baseline (what the redesign is grounded in)

- Figma file `MxRZQxXrgV9hMDtTTVEvKW`, P2 history node `68:3415`, P3 transcript node `40001409:7587`.
- Cached PNGs read: `docs/_session/parent-dashboard-rebuild/screens/s2-chat-history.png`, `s3-chat-detail.png`.
- P2 in Figma: header "Conversation History" + back; chips All / Ongoing / Flagged / Learning / E[motions]; rich cards (title + status icon, time + colored mood dot, summary paragraph, topic tag pills, "Suggested follow-up questions" with chat-bubble icons, "View full transcript" link). One card is **Live** ("Recess incident · Live").
- P3 in Figma: "Full Transcript", child (filled teal) ↔ Lolo (light) bubbles, "End of Conversation" divider, then **"HeyLoLo's Suggestions"** parenting cards (Celebrate Bravery / Reinforce Social Skills / Build Self-Confidence / Keep The Conversation Going).

## Mobbin references (platform: ios)

- [Character AI — chat history with rich review cards](https://mobbin.com/screens/d7051e6f-b9be-4613-a1f7-1ff23d506a83) — rich cards: timestamp + small status chip ("current"), title, multi-line excerpt, thumbnail. Closest analog to the P2 review card.
- [Airbnb — Messages with filter chips (All / Traveling / Support)](https://mobbin.com/screens/ad0cb3b3-535d-475e-95ca-91fb014366ce) — canonical pill filter-chip row with a selected (dark) state above a list.
- [Beside — Inbox with chips + Daily Recap summary rows](https://mobbin.com/screens/40749600-658e-43d0-8ac3-a0121af5d297) — warm, soft circular-icon avatars + chips All/Unread/…; summary-line treatment.
- [Google Gemini — Chats grouped by date (Today / July)](https://mobbin.com/screens/0271d145-bfa7-4c60-9a3a-02db3f5cfe7c) — read-only transcript/history grouped by a centered date header, plain rows, no chrome.
- [Slack — read-only channel transcript with date separators](https://mobbin.com/screens/be9c191e-ed23-45c2-8c9c-9dde56191c1c) — grouped bubbles + day rules for reviewing a transcript.
- [Telegram — chat list with pinned search field](https://mobbin.com/screens/2525dcb3-8d9c-4dd6-86b2-85d015301407) — pinned search above the scrolling list.

## The 2–3 ideas (and the ref each came from)

1. **Pinned filter-chip rail + search over a calm scroll** (from Airbnb's chip row + Telegram's pinned search). The chips and search stay fixed at the top; only the card list scrolls. Selected chip = solid `BlueLagoon` pill (high contrast), the rest are soft white pills. Chips that map to a mood (Ongoing/Flagged/Learning) carry a tiny solid color dot so the filter vocabulary matches the card mood dots.

2. **Rich review card as the unit of review, not a chevron row** (from Character AI's review card). Each card stacks: title + status glyph → time + colored mood dot → summary → topic tags → "Suggested follow-up questions" (Lolo's parenting prompts, each with a soft chat-bubble icon) → a footer "View full transcript" with a small soft **"Open" pill** instead of a chevron. The whole card is tappable. This keeps the dense parenting value of the Figma card while removing the banned chevron affordance.

3. **Read-only transcript that earns trust through grouping + a warm "Suggestions" coda** (from Gemini/Slack date-grouped transcripts + the Figma "HeyLoLo's Suggestions"). A conversation-header card, a centered day rule, alternating child (filled teal) / Lolo (light, with a small cloud avatar) bubbles, an "End of conversation" hairline rule, then HeyLolo's parenting suggestions as soft cards with a tiny color "pin" dot (teal default, `Pizazz` for the warmest nudge). The transcript panel re-contexts (title + meta) when a card is tapped in State 1, making the two-panel relationship explicit.

## Tokens used (from `src/shared/config/palette.ts` + `fonts.ts`)

- Teal spine: BlueLagoon `#037B83` (titles, selected chip, child bubble, strong ink), BlueChill `#0E8B93` (tags, accents), Paradiso `#3C9094` (follow-up icons), FountainBlue `#5EBCC1` (Social mood + suggestion pin), Gothic `#6B9EA8` (timestamps), SmaltBlue `#5A8994` (secondary text — used as readable secondary, never the faintest grey), Ziggurat `#C2DDDE` (hairlines / separators), Anakiwa/Onahau (cloud-disc gradient stops).
- Light surfaces: AquaSpring `#E8F4F8` (tag pills, "Open" pill), BlackSqueeze `#E9F6F6` (follow-up icon wells), LightGrayishCyan `#F5FAFB` (screen bg).
- Warm accents (sparingly): BurntSienna `#E36843` (Live badge + Flagged dot, the alert-cloud face), Pizazz `#FF9500` (Curious mood dot, warmest suggestion pin).
- The signature **cloud mic-disc gradient** `#CDF0F2 → #DEF4F6 → #F5FBFB` reused for every Lolo avatar (card avatar, bubble avatar, suggestions badge).
- Fonts: Nunito (display + body), DM Sans (timestamps / meta / uppercase labels).

## Kept vs changed

**Kept from the real Figma baseline**
- Header + back-button-in-a-white-circle pattern; "Conversation History" / "Full Transcript" titles.
- Filter set All / Ongoing / Flagged / Learning / Emotions.
- The full card anatomy (title, status glyph, time, mood dot, summary, topic tags, suggested follow-up questions).
- The Live "Recess incident" card and its real-time CTA.
- Transcript: child↔Lolo bubble polarity, "End of Conversation" rule, "HeyLolo's Suggestions" parenting cards.

**Changed / improved**
- **Removed the chevrons** on "View full transcript" and the dashboard-style "›" rows (hard ban). The whole card is the tap target; the footer shows a soft "Open" pill instead.
- Added a **pinned search field** (from Mobbin) the Figma P2 lacked — review screens get long.
- Mood dots are **solid** small dots (no pulsing) — the only animated element is the genuinely-live "Recess incident" pulse ring in the brand `BurntSienna`, which is the sanctioned exception (motion reserved for a live, changing value).
- Made the chip→card→transcript relationship **interactive**: tapping a card re-contexts the transcript header/meta and gently scrolls/pulses the second phone so the two states read as one flow.
- Suggestion cards use a tiny color "pin" dot for hierarchy instead of an icon tile beside every heading (avoids the icon-tile-per-heading tell).

## Hard-rule compliance (elumi-kids-design + forbidden-AI-tells)

- Light-only, two states side by side — no dark variant.
- No colored left-edge accent rails; no arrows/chevrons anywhere (verified by grep); no gradient text; no Inter/Roboto/system fonts (only Nunito + DM Sans); no emoji (inline stroke SVG only — verified by grep).
- Page background is a soft teal tide-pool wash, not a flat white and not a dark/atmospheric mesh; card elevation uses soft neutral teal-tinted shadows, not neon accent glows.
- `index.html` not touched; no browser opened; no long-running server.
