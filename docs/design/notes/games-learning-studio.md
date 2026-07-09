# Games hub + Learning Studio — design notes

Prototypes:
- [`docs/design/games-hub-redesign.html`](../games-hub-redesign.html) — the **Games** hub
- [`docs/design/learning-studio-redesign.html`](../learning-studio-redesign.html) — the **Learning Studio** (Learn + Create)

Surface: two new **content-launcher hubs** for HeyLolo / elumi-kids — light-only, warm-teal "tide-pool"
brand. A kid taps a tile to launch a game or an activity. Both drop straight into the hosted gallery
alongside `home-redesign.html` and `voice-chat-redesign.html`.

## Where the tokens came from (lifted verbatim, not guessed)

Read from the two shipped prototypes and reused 1:1:

- **Palette** (`palette.ts` / chat-baseline): BlueLagoon `#037B83`, BlueChill `#0E8B93`, Paradiso `#3C9094`,
  FountainBlue `#5EBCC1`, Anakiwa `#A2F3FF`, Onahau `#CAF8FF`, SmaltBlue `#5A8994`, teal-deep `#16737A`,
  ink `#114e52` / `#28484c`, muted `#5b9296` / `#6c8b8e`.
- **Disc gradient** (mascot avatar + vs-LoLo badge): radial `#F5FBFB → #DEF4F6 → #CDF0F2` with a
  `rgba(255,255,255,.6)` border and `rgba(3,123,131,.18)` shadow.
- **Warm/secondary accent**: coral `#E0763F` (the same "Explorer" orange the home uses).
- **Phone frame**: `344×730`, radius `54px`, body `#0c2f33`, pad `11px`, dual drop shadow
  `0 36px 80px rgba(11,60,64,.30), 0 6px 16px rgba(11,60,64,.16)`.
- **Screen wash**: `linear-gradient(180deg,#D6EFF5,#E3F5F3 42%,#EFF9F8 72%,#FBFDFD)`.
- **Status bar / home indicator / tab bar**: copied byte-for-byte (DM Sans `9:41`, signal/wifi/battery
  SVGs; tab bar `rgba(255,255,255,.86)` + `blur(14px)`, radius `28px`, active pill `#E0F1EE`, active ink
  `#178187`, inactive `#9bb5b8`; home indicator `128×5 rgba(20,60,64,.18)`).
- **Cards**: white, radius `22–26px`, shadow `0 8px 22px rgba(20,90,95,.09)` (tiles) /
  `0 12px 28px rgba(20,90,95,.10)` (continue card).
- **Fade edges** (house rule 11): scroll surface masked with `linear-gradient(180deg,#000 88%,transparent)`
  so tiles dissolve above the tab bar instead of clipping; `96px` bottom pad clears the tab bar.
- **Fonts**: Nunito (display/labels) + DM Sans (numerals) via Google Fonts.
- **Reduced motion**: `@media (prefers-reduced-motion: reduce)` collapses all transitions/animations.

## Screen A — Games hub

Layout order: status bar → hub header (back circle + LoLo avatar + "Games") → **Continue playing** card
(last game, here Memory cards, with a filled play button to resume) → "All games" row → **2-col grid of 9
tiles**.

**Tile taxonomy** (icon chip + label + one-word sub-label; `vs LoLo` badge = play against the AI):

| Tile | Glyph | Accent chip | vs LoLo? |
|---|---|---|---|
| Chess | pawn | lagoon | ✅ |
| Connect Four | 4×disc board | sky | ✅ |
| Memory cards | two cards + star | teal | ✅ |
| Jigsaw puzzle | puzzle piece | amber | — |
| Connect the dots | numbered dotted path | rose | — |
| Labyrinth | square-spiral maze | coral | — |
| Which habitat? | pine tree | sea-green | — |
| Manners quiz | heart + sparkle | teal | — |
| Q&A | speech bubble + `?` | lagoon | — |

The `vs LoLo` badge is a small pill (top-right) with a mini mascot face so a **non-reader** still knows
those three are "against LoLo".

## Screen B — Learning Studio

Layout order: status bar → hub header ("Studio") → **Learn** section → **Create** section. Two clearly
signposted "rooms" sharing the games tile language, differentiated by:
- **Section header** with a chip glyph (graduation cap = Learn, paintbrush+sparkle = Create) and colored
  title (teal `#0E7077` vs coral `#C05C31`).
- **Chip temperature**: Learn skews cool (teal / sky / sea / lagoon); Create skews warm (coral / amber /
  rose), with a faint cream card (`#FFFDFA`) and a warm-tinted tag chip.

**LEARN taxonomy** (label · tag):

| Tile | Glyph | Tag | Chip |
|---|---|---|---|
| Math | +/−/×/÷ signs | Math | teal |
| Physics | atom | Science | sky |
| Geometry | triangle/square/circle | Shapes | sea |
| Vocabulary | Aa letter blocks | Words | amber |
| Trace letters | pencil on dotted line | Trace | rose |
| Learn the clock | clock face | Time | lagoon |
| Supermarket | shopping cart | Money | teal |
| Food & nutrition | apple | Food | sea |

**CREATE taxonomy** (label · tag):

| Tile | Glyph | Tag | Chip |
|---|---|---|---|
| Cooking with LoLo | chef hat | Cook | coral |
| Grow a plant | sprout in pot | Grow | sea |
| Storybook maker | open book | Story | amber |
| Coloring | paint palette | Draw | rose |
| Short videos | video frame + play | Video | coral |

## Interactivity (demo-able)

- Every tile presses to `scale .96` on tap and eases back (spring cubic-bezier), with a lightened shadow.
- Tapping a game tile fires a small pill **toast** ("Launching Chess…" / "Opening Math…") that
  auto-dismisses after 1.5s — enough to show the launch intent without a fake screen.
- All motion is gated by `prefers-reduced-motion`.

## Self-check (forbidden-ai-tells)

No emoji-as-icon (every glyph is inline stroke/solid SVG). No chevron/arrow used as decoration — the only
arrow is the functional **back** control (matching `voice-chat-redesign`), and the only triangle is the
functional **play**/media glyph. No gradient text, no neon `0 0` glow, no atmospheric mesh/grain (screen
bg is the real flat aqua wash; the only radial gradient is the mascot disc's real signature token). Fonts
are Nunito + DM Sans. Touch targets ≥ 64px (tiles are ~135×116). Light-only, no dark variant.

## Open design questions

1. **Navigation home.** Where do these hubs live in the real nav? The prototypes keep the shared 3-tab bar
   (Home / Chats / Settings, Home active) and a back control, implying they're pushed from Home tiles. If
   Games/Studio deserve first-class tabs, the tab bar grows to 4–5 and the active state moves — needs a
   product call before porting.
2. **vs-LoLo scope.** Only Chess / Connect Four / Memory are marked "vs LoLo". Should Labyrinth / Which
   habitat / Manners also get a co-op or "with LoLo" variant, and if so how do we distinguish *against*
   vs *with* (the badge currently reads as adversarial)?
3. **Age gating.** 4–12 is a wide band. Chess and Physics skew older; Trace letters and Coloring skew
   younger. Do we filter/reorder tiles by the child's age profile, or show everything and let LoLo scale
   difficulty inside each activity?
4. **Continue-playing depth.** Games shows one "last game". Should Studio get a symmetric "Pick up where you
   left off" (half-finished storybook, plant on day 3)? Likely yes for stickiness — omitted here to keep
   the first pass calm.
5. **Locked / premium tiles.** No locked state drawn. If some activities are gated behind entitlement
   (see subscriptions work), we need a calm lock treatment that doesn't feel punitive to a kid.
6. **Content count / scroll.** 9 games + 13 studio tiles already scroll. If the catalog grows, do we
   paginate, add category filter chips (like the parent dashboard), or group into collapsible rows?

## Status

Design prototype only — production code untouched. `docs/design/index.html` **not** edited (gallery index
not wired). Not ported. Awaiting operator review before any worktree port.
