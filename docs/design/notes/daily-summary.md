# Daily Summary redesign — design notes

Prototype: `docs/design/daily-summary-redesign.html` (open directly; self-contained, vanilla JS).
Scope: parent-facing **Daily Summary** screen (DS1 `40000453:7298` full scroll + DS2 `40001360:8478`
entry button). Light-only, real teal "tide-pool" theme. Design prototype only — no production code touched.

## Mobbin references (ios)

Examined 15 screens across the three brief queries. The ones the ideas actually came from:

- [Beli — "September, at a glance"](https://mobbin.com/screens/ac5b8c7f-dee7-478c-aad7-8c89728720b1) — single flat recap panel with three stacked big-number + label stats. Source of **Idea 1**.
- [Opal — "Let's recap" highlights](https://mobbin.com/screens/403d27ac-0a46-4cdd-9e8e-a7521bbc8aee) — divider-separated big-number rows, each with a small status chip on the right. Source of **Idea 2**.
- [timespent — "Recap / Timed Activities"](https://mobbin.com/screens/4bf89dbc-99ee-46de-ba7d-17ddfea5bd79) — total-time hero + per-activity breakdown list with proportion. Reinforces **Idea 2** (proportion bars on each conversation).
- [Finch — "Latest Progress"](https://mobbin.com/screens/b717349d-ebbb-4ea4-92b1-67eee8675560) — warm, plain-language one-line-per-thing narrative list. Source of **Idea 3**.
- [Noom — "Today's Progress"](https://mobbin.com/screens/e5da144e-e9ca-4d30-88e3-4fa253f7ff09) — flat white rows + small round status icon, no card-in-card. Reinforces the insights list styling.

Other queried-but-not-used (for the record): GO Club ([1](https://mobbin.com/screens/281efc99-7572-4bcf-ab5d-42dee33335e0), [2](https://mobbin.com/screens/f52a327d-e539-458c-a71f-0a1437ef97a8)), [Tangerine](https://mobbin.com/screens/c79337f1-5ece-4920-bcec-6c8c7b24e704), [TIDE](https://mobbin.com/screens/e896e10e-3e7a-4da0-a099-eadb0c1cdba8), [Fabric](https://mobbin.com/screens/b0de8bea-341f-4846-b4d8-bc73e87331f6), [Gawq](https://mobbin.com/screens/5a4558f5-577a-438b-94c2-714dee67c809), [GoHenry](https://mobbin.com/screens/8c96f881-62da-4f5f-842d-0325a3e06555), [How We Feel](https://mobbin.com/screens/ee29ed85-2896-4126-ad7a-81ca0e66bb9a), [Google Fit](https://mobbin.com/screens/3b20d4cd-bb32-4c3a-a18b-7c831bc6b9cc).

## The 2–3 ideas

1. **Lead with the narrative, then an "at-a-glance" triad** *(from Beli)*. The baseline DS1 opens with a
   sentiment gauge and two stat boxes before you know what the day was *about*. The redesign opens the
   hero card with one warm sentence ("Paul had a curious, upbeat day.") + a one-line narrative — both of
   which the backend already produces (`daily-summaries` Gemini prose, `overallSentiment.description`) —
   then a single flat 3-up stat row (Conversations / Time together / Topics) divided by hairlines, not
   three nested boxes. One panel instead of card-in-card.

2. **Conversations as divider rows with a proportion bar** *(from Opal + timespent)*. Each conversation
   is a row inside one card — name, time + duration (`startedAt`, `durationMinutes`), a thin teal
   proportion bar sized to its share of the day, and a tap-to-expand recap (`summary`). A mixed-sentiment
   chat gets a warm (Pizazz) bar instead of teal — color carries meaning on **that one element only**,
   never the whole row. State B shows the expanded row with a sentiment chip + "Open full transcript".

3. **Insights + starters as a plain warm narrative list** *(from Finch)*. `insights[]`
   (strength / curiosity / socialEmotional) render as one-line-per-thing rows with a small rounded
   icon tile and a brand-colored eyebrow — not three big "cloud" cards. `conversationStarters[]` are
   italic quote rows the parent can read aloud. State C demotes the stats and leads with a larger
   sentiment arc + the two things that stood out, for a calm day where nothing needs attention.

Cross-cutting: **"Worth a look"** (`requiresAttention`) is a single warm Pippin panel with a leading
warning icon + tinted title and a "Read this chat" button — **no colored left-edge rail** (the brief's
hard ban). Topic chips (`topics[]`) are filterable in state B.

## States shown (3, side by side, all light)

- **A — Full summary scroll**: hero narrative + triad → sentiment arc → mood journey → topic chips →
  worth-a-look → conversations (one expanded) → insights → starters.
- **B — Topic filter + expanded conversation**: a topic chip selected narrows the day to matching chats
  and floats the attention moment up; the conversation row is expanded with a sentiment chip.
- **C — Sentiment & insights focus**: a calmer (yesterday) day leads with the narrative + a bigger arc +
  the two standout insights; stats and mood demoted; an empty "No chats" mood bucket shown.

## Tokens used (from `src/shared/config/palette.ts` + `fonts.ts`)

- **Fonts**: Nunito (display + body), DM Sans (all numerals — stats, times, gauge, message counts — with
  `tnum`). Google Fonts. No system/Inter/Roboto.
- **Teal spine**: BlueLagoon `#037B83` (headings, big numbers, strong ink), BlueChill `#0E8B93`
  (secondary teal / chip text), Paradiso `#3C9094` (units), FountainBlue `#5EBCC1` (proportion bars,
  sentiment arc fill), SmaltBlue `#5A8994` (captions), Gothic muted-teal.
- **Light surfaces**: page behind phones flat `#dfeef0`; screen backdrop = the real soft listening-surface
  gradient `#f3fbfb → LightGrayishCyan #F5FAFB → BlackSqueeze #E9F6F6`; AquaSpring `#E8F4F8` /
  BlackSqueeze `#E9F6F6` chip + icon-tile fills; white cards.
- **Disc gradient** (mic-disc, reused on child avatar + mood faces): `#CDF0F2 → #DEF4F6 → #F5FBFB` radial,
  `rgba(255,255,255,.7)` border — the signature element per the design skill.
- **Warm accents (sparingly)**: BurntSienna `#E36843` + Pippin `#FFE6DE` (worth-a-look), Pizazz `#FF9500`
  (mixed-sentiment bar + social/emotional eyebrow), Emerald `#34C759` (strength / positive).
- **Inks (legibility rule)**: strong `#0c4a4f`, readable secondary `#3f6b72` for ALL read text; the
  faintest `#9bb6bb` is used only on the decorative "No chats" placeholder face, never on a label.

## Kept vs changed (vs baseline DS1)

**Kept** (real data + brand): date `‹ ›` strip with "Today"/future-disabled; child pill in header;
semicircle sentiment gauge with the score (61); mood-journey morning/noon/night faces; topic chips;
requires-attention block; per-conversation cards with time + duration + summary + transcript link;
HeyLolo insights (strength/curiosity/social-emotional); conversation starters. All map 1:1 to the
`DailySummary` wire shape and the incremental aggregate (`child-day-aggregates` topics/spans/mood,
debounced `daily-summaries` narrative).

**Changed**:
- Hero now leads with the **Gemini narrative sentence** + a single flat **3-up stat triad** (Beli),
  replacing the gauge-first + two-separate-stat-boxes opening.
- Conversations become **divider rows in one card with proportion bars** (Opal/timespent), replacing
  three separate full cards each with its own progress bar.
- Insights become a **plain narrative list with small icon tiles + brand eyebrows** (Finch/Noom),
  replacing the three large "cloud" insight cards.
- Attention uses a **leading icon + tinted title**, explicitly **no left-edge accent rail**.
- Sentiment uses **one accent on the number only** — no whole-panel recolor by mood (forbidden tell).

## Self-check (forbidden-ai-tells)

No arrow/chevron decoration glyphs (back + date-nav are functional nav controls, SVG, matching baseline);
no `box-shadow:0 0` glows; no left-edge accent rails; no atmospheric mesh page background (flat `#dfeef0`
behind phones; the in-screen gradient is the real app listening-surface, an intentional brand element);
no Inter/Roboto/system design face; no emoji (inline stroke SVG only); no whole-component state recolor;
`ink-faint` never on a readable label. Soft neutral teal elevation shadows only.
