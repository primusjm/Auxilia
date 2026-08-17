# Blue Band Breakfast Squad — gamified web app prototype

Front-end prototype of the companion web app for the WhatsApp breakfast
challenge, themed on Blue Band's existing **Breakfast Squad** IP. Mock data only:
no backend, no network calls, no auth. Built against
`BlueBand_Prototype_Handoff.md`, with tone and mechanics cross-checked against
`BlueBand_Full_Deck_v2.pptx`, `BlueBand_Integration_Spec.md` and
`Social Mission Comic Book 2024 Original.pdf`.

Viewport target is 390×844. Above 440px the app renders inside a phone frame for
review; at or below 440px the frame drops and it goes full-bleed.

## Toolkit journey

The app follows the Social Mission Toolkit journey:

**Landing → Register/Login (parent) → Dashboard → Avatar creation → Games hub →
Completion + certificate → Parent survey.**

The tracker, streaks, badges, report card, child profile and Naivas cart from the
earlier build are mapped into this structure rather than rebuilt:

- **Dashboard** (`dashboardScreen`) is the daily check-in — log breakfast, see the
  recipe, track progress, jump to games. It is the **logger, not the comic's
  weekly planner** (that stays a separate parent tool, deliberately not built
  here).
- **Calendar / report card / child HQ** are sub-screens off the dashboard and tab
  bar, unchanged in logic.
- **Games hub** is new; badges are the keys that unlock it (see below).
- **Certificate** is the completion screen, reached from the day-final
  celebration. **Parent survey** follows it (comic p. 12).

Tab bar: Today · Calendar · Games · {child} · Yum City. The full-screen flow steps
(landing, auth, avatar, certificate, survey) show no tab bar.

## Compliance — parent-fronted (FFG: do not market to children)

- **The parent owns the account.** Landing and the register/login gate speak to
  the parent; the child is a "recruit" inside the parent's account. Entry is still
  the WhatsApp token (spec §A1), so the gate is a lightweight parent-owner
  confirmation + consent, not a password signup — nothing is submitted.
- **Kids' surfaces are educational, never sales.** The Games hub and child HQ
  carry **no cart, no Naivas, no buy CTA** — verified in the regression sweep. The
  Naivas one-way cart hand-off lives only on the parent dashboard.
- **Asterisk convention.** Every kid-facing "superhero" claim carries `*` with the
  footnote in `BB.ASTERISK` ("*With a good breakfast every day, as part of a
  balanced, active lifestyle."). Applied on landing and the certificate.
- **Age 6–14, gender-neutral.** Stated on landing. The avatar heroes
  (`BB.HEROES`) are power-symbol placeholders with neutral names, balanced so
  nothing skews to one gender.

## Games hub

Three toolkit games as badge-locked **entry points only — the games are not
built** (real engines come later). Each unlocks when its tied badge is earned, so
the badge ladder is the key chain and "unlocks at Day X" reads off the badge day —
correct whether the programme is 21 or 30 days.

| Game | Kind | Unlocks with badge | Day (21 / 30) |
|---|---|---|---|
| Blue Band Run | Endless runner | Squad Recruit | 1 / 1 |
| Blue Band Solve | Food-sorting puzzle | Sidekick | 5 / 7 |
| Blue Band Brawl | **Feed-the-monster tap game** | Breakfast Hero | 10 / 14 |

**Blue Band Brawl is reframed from "fight" to "feed"** — tap good foods to the
hungry monster and turn him friendly. No violence; kid-safe by design. Each tile
shows locked/unlocked state, the unlock day, and a light social-proof line
("512 kids are playing this"). Tapping an unlocked tile opens a placeholder sheet
that says the engine drops in here.

## ⚑ Open question — programme length (set to 21, pending confirmation)

**The printed Breakfast Squad programme runs 21 days; the earlier brief said 30.**
The comic is a "three-week action plan" with a 21-row planner (pp. 5–7). This pass
sets the challenge to **21 days** per instruction, but the decision is not final.

`BB.CHALLENGE_DAYS` in `mock.js` is the single switch — set it to `21` or `30` and
every screen, badge day, phase, progress rail, game-unlock day and demo scenario
follows; no screen reads a hardcoded day count. The state is flagged in-app on the
landing screen and the calendar (the flag reads "matches the printed programme" at
21 and "length unconfirmed" at 30), so the choice cannot be signed off by accident.

Both programmes are defined in `BB.PROGRAMS`. The 21-day badge days
(1/5/10/15/21) are **provisional**: the printed programme is weekly, but the badge
enum has five rungs, which does not divide into three weeks cleanly. If Flora
confirms 21, that ladder needs a real decision — either four rungs (a spec change)
or the spacing used here.

## The IP

Universe, characters and nutrition framework come from the comic:

- **Yum City** and the **Junk Food Monster**, who took the good breakfasts and
  left everyone tired.
- **The squad** — BlueBand (good fats), Peanut Butter (protein), Brown Bread and
  Rice (carbs), Carrot / Mango / Mushroom (vitamins A / C / D).
- **The four groups**, in the comic's own framing: Carbohydrates for Energy, Good
  fats for Growth, Proteins for Strength, Vitamins for Immunity. Every meal item
  in `mock.js` is tagged with its group, and the meal card scores the breakfast
  against all four — the Breakfast Magic Box rule. `mkate` is deliberately 3-of-4
  so the gap state is demoable; the card names the missing power.
- **The pledge** is the enrollment device, as in the comic.

**Character art is not reproduced.** The caped Blue Band tub and the squad are
emoji stand-ins driven by `--bb-mascot-glyph` / `--bb-logo-glyph` in `tokens.css`
Tier 1, and `.mascot::before` / `.logo__mark::before` in `app.css`. Swap those for
real artwork. Copy is written for this app rather than lifted from the comic.

## Files

| File | What it holds |
|---|---|
| `index.html` | Entry point. Links the four files below. |
| `tokens.css` | Design tokens. **The only file to touch when brand assets land.** |
| `app.css` | Components and screens. Contains no literal colours. |
| `mock.js` | Mock data + the five demo scenarios. |
| `app.js` | State, routing, rendering, the log → streak → badge flow. |

## Swapping in the real brand

Everything brand-owned is Tier 1 at the top of `tokens.css`. Tiers 2 and 3
(semantic roles, shape, motion) reference Tier 1, and `app.css` only ever
references Tiers 2–3. So the swap is one block:

1. Replace the `--bb-blue-*`, `--bb-gold-*`, `--bb-cream-*` and `--bb-warm-*`
   ramps with Flora's real values. Keep the step names — every rule reads them.
2. Replace `--bb-font-display` / `--bb-font-body` with the licensed faces, and
   drop the Google Fonts `<link>` in `index.html`.
3. The logotype is a placeholder glyph driven by `--bb-logo-glyph` and rendered
   by `.logo__mark::before`. For a real mark, swap that rule for an `<img>` — the
   mark is a single 34px component used in two places.

Nothing else needs editing. `app.css` contains no hex values, no named colours
and no brand-tinted `rgba()`: the gold glows, medal shadows and scrims are mixed
from Tier 1 with `color-mix()`, so they follow a swap automatically — overriding
`--bb-gold-700` alone recolours every gold shadow. The only literals left in
`app.css` are white and black alphas for glass highlights and drop shadows, which
are not brand-owned.

Two caveats:

- `color-mix()` needs Chrome 111+ / Safari 16.2+ / Firefox 113+. Fine for this
  prototype and current mobile browsers; if it ships to a wider Android base,
  precompute those tokens as rgba instead.
- Contrast was checked against the placeholder ramp. Re-check when the real
  palette lands — the placeholder gold only holds up as a large-text/accent
  colour, never as body text.

## Data

The app reads the progress object from integration spec §A2:

```
{ day, streak_current, streak_longest, badges[], last_logged_at, breakfasts_logged, child_name }
```

Badge keys match the spec enum exactly — `i_started`, `junior_chef`,
`breakfast_hero`, `nutrition_star`, `breakfast_champion` — so they pass through
untouched.

**The Breakfast Squad rename is display-only, and the wire keys now disagree with
the labels.** The enum in §A2 is the contract with Engage, so renaming it here
would break the platform side; instead `BADGE_DEFS` in `mock.js` maps each fixed
key to its squad name:

| Spec key (on the wire) | Shown in the app | Day (30-day) |
|---|---|---|
| `i_started` | Squad Recruit | 1 |
| `junior_chef` | Sidekick | 7 |
| `breakfast_hero` | Breakfast Hero | 14 |
| `nutrition_star` | Squad Champion | 21 |
| `breakfast_champion` | Yum City Superhero | 30 |

`junior_chef` carrying a badge called "Sidekick" is survivable but is the kind of
thing that rots. **If Flora signs off on Squad naming, the enum should be renamed
to match — that is a coordinated change with the Engage team** (§A2 payload,
`custom_attributes` already written on `Contact`, and any flow node referencing a
badge value), not something the web app can do alone. Flagged, not actioned.

**`days[]` is the single source of truth.** A scenario in `mock.js` declares only
what the mother actually did — which days she logged, which she missed, and where
she is now. Every counter above is then derived by `recompute()` in `app.js`, and
`logBreakfast()` marks the day and re-derives rather than incrementing anything by
hand. This is deliberate: hand-authored counters were how Home, Calendar, Report
card and the payload could drift apart and quietly disagree with what the grid
actually showed. They now cannot — a screen and the payload read the same derived
number or the data does not exist.

Each day carries `status` (`logged` / `missed` / `upcoming`) and a **separate**
`isToday` flag. Keeping them separate is what lets a day be both today and logged
and show both facts at once.

`app.js` has a `syncProgress()` seam where the real `POST /contacts/{id}/progress`
goes. It currently `console.info`s the payload; the states tray renders the same
payload live so a reviewer can see the contract without opening devtools.
`last_logged_at` is derived from the calendar via `BB.CHALLENGE_START`, so it
cannot fall out of step with the logged days either.

Tapping **Get the ingredients** opens a hand-off sheet: the pre-filled cart, the
§C1 URL built from the recipe's SKUs
(`?items=BB-500G:1,EGG-TRAY:1&ref=blueband-social-mission`), and one link out. That
is the full extent of commerce here — checkout, payment, delivery and order
tracking are Naivas's and are deliberately absent.

## Scenarios

The floating **States** control (bottom right) is a review aid, not product. It
jumps to: day 7 ready to log (default), day 7 already logged, streak at risk,
day 14 Breakfast Hero, and day 30 Champion. Every state reflects consistently
across all seven screens and the sync payload — it is not a per-screen mock.

Primary flow to demo: **States → day 7 ready → "I made breakfast" → streak
animates 6→7 → Junior Chef unlock → "See your week" → report card.**

Recovery flow worth demoing too: **States → streak at risk → home reads "Let's
pick it back up" with the 9-day streak *on hold* and the supportive banner →
"I made breakfast" → 10, streak carries on, banner clears.** Days 10 and 11 show
as missed on both the calendar and the report card throughout.

## Decisions taken

- **A missed day holds the streak, it does not reset it.** The deck is explicit
  ("Miss a day and get a warm re-engagement nudge, not a reset") while the
  handoff calls the state "streak at risk". Reconciled as: the number is
  preserved and marked *On hold — still yours*, the flame dims, and logging
  carries it straight on. A big red `0` would have broken the "never punitive"
  rule. **Worth confirming with Flora** — it materially changes what a streak means.
  Concretely: `STREAK_GRACE_DAYS = 3` in `app.js` — a gap of up to three days does
  not break a run. That number is a guess standing in for a product decision.
- **"At risk" means she hasn't logged since before yesterday** — not merely that a
  missed day exists somewhere behind her. Logging yesterday clears it. Without
  this the day-30 Champion state showed a "jump back in" nudge mid-celebration,
  because it has a missed day at 28 even though she logged on 29.
- **Today is a ring, not a fill.** The calendar legend promises four states, so
  all four have to be drawable at once. Today is an `outline` layered over
  whatever the day's status painted, which is why a logged today reads as a check
  inside the gold ring rather than losing its today-ness.
- **The report card's week reuses the calendar's cells.** "Logged" means one thing
  and looks like one thing — a blue check — in both places.
- **Leaderboard ties break alphabetically.** Stable across renders, and a rule a
  parent can verify at a glance instead of it looking arbitrary.
- **The monster is a nuisance, not a threat.** The comic's own missed-day framing
  is "miss breakfast and the monster gets powerful", but its register is gentle —
  gummy worms, naps, "tired and sleepy". So a missed morning gets 👾 and "the
  monster is getting bolder", the meter caps well short of full, and the copy
  always ends on the way back in. Nothing here tells a six-year-old they failed.
  The streak still holds rather than resets.
- **Squad language is dialled to the reader.** The mother's operational screens
  (home, calendar, report) stay practical and warm; the child-facing surfaces
  (HQ, ranks, celebrations) carry the full comic energy. The app is a mother's
  tracker with her child in it, not a children's game.
- **The journey phase names are not on the home screen.** "Show me" / "Help me
  again" are internal strategy labels and read as instructions to a user. They
  appear on the calendar screen under "The journey", where they have context. The
  home rail shows "N days to go" instead.
- **Day 3 is not a badge.** The deck's rewards slide lists day 3/7/14/30
  milestones, but the badge ladder slide and the handoff both specify
  1/7/14/21/30. Followed the ladder, which matches the spec enum.
- **Leaderboard shows streaks only, no scores or losers**, and the child's row
  reads live state so rank and number never disagree.

## Out of scope (per handoff §7)

WhatsApp conversation screens, the AI nutrition assistant, Naivas
checkout/payment/order tracking, real auth (entry is a WhatsApp token), and any
backend. Enrollment assumes the §A1 token already identified the user, so there
is no login or signup anywhere in the flow.

## Known gaps

- Recipes, riddles and the community numbers are written-in placeholders pending
  client content; they are all in `mock.js`, not in markup.
- Emoji stand in for food and badge art throughout.
- The certificate CTA on the day-30 celebration is inert.
