# RX-7 13B-T Tech Page — Accuracy Review & Fix Instructions for Copilot

**Page:** https://ruddyrid.github.io/20261107-rx7-13bt-tech/
**Repo:** https://github.com/ruddyrid/20261107-rx7-13bt-tech (branch `main`)
**Reviewed commit:** `c15ca56` — `index.html` (1,683 lines), `README.md`
**Review date:** 2026-11-08
**Reviewers:** three independent reviewers (specs/torque, claims/history,
sources/consistency), reconciled into this single report. Primary sources
read directly: Mazda 1988 RX-7 FSM (Technical Data §30, §1 Engine,
§2 Lubrication, §4B Turbo fuel & emission, §6 Clutch, §7A Transmission,
§12 Wheels & Tires), Mazda 1989 RX-7 FSM (Technical Data, §04B), Atkins
Rotary 13B spec sheet (FD 13B-REW — cross-check only, **not** an FC
authority), Haltech "Tuning rotary engines", RX7.org, RX7Club FSM
transcriptions, Mazda 13B/8C technical review 2023.

## How to use this file (read this first, Copilot)

You are the fixing agent, running on the owner's Omarchy server. Work
through this file top to bottom:

1. **Setup** — clone (or pull) the repo, then work in the local copy.
2. **§1 ERROR** — apply every fix. These are wrong against the FSM.
   Several errors repeat in multiple places (torque table + rebuild
   chips + print card + JS data) — fix **all** listed locations.
3. **§3 NIT** — apply these too. Small wording/value fixes.
4. **§2 VERIFY** — do **not** change these. They need a human decision
   (the owner, with the FSM open). Leave them exactly as they are.
5. **§4 DO NOT TOUCH** — values already verified correct. Do not
   "fix" them.
6. **§5 Verify** — run the checks, then report back.
7. **Do NOT commit or push your edits.** Leave them uncommitted in the
   working copy for the owner to review. Do not touch the Big Cartel
   store or tealsunltd.com — this is the GitHub Pages repo only.

Line numbers refer to `index.html` at the reviewed commit. If lines
have shifted, find the location by the quoted text. Keep the page's
existing wording and style; change only the numbers and sentences
named here. Never "fix" an FC value to the FD (13B-REW) figure — the
Atkins sheet is a different engine (e.g. FD tension bolts are 24–28
ft-lb, FC are 23–29 ft-lb).

**Counts (after de-duplication across the three reviews): 21 ERROR,
15 VERIFY, 29 NIT.**

## Setup (on the OptiPlex terminal)

```bash
cd ~
git clone https://github.com/ruddyrid/20261107-rx7-13bt-tech.git
cd 20261107-rx7-13bt-tech
git log --oneline -1   # should show c15ca56 (or newer — if newer, check
                       # the line numbers in this report still match)
```

Work in that folder. The file to edit is `index.html` (and `README.md`
where a finding says so).

## §0 — Disagreements between reviewers, and how they were resolved

Read this before touching the torque table — the three reviews
disagreed in places, and the wrong choice here would introduce new
errors.

1. **Torque table: keep the FSM's paired figures, do NOT recompute
   one column from the other.** One review recommended recomputing
   ft-lb from Nm (e.g. flywheel 290–360 → 288–361). That is rejected.
   The review that actually read the FSM proved the manual's native
   unit is kg·m and its N·m and ft-lb columns were rounded
   independently — the FSM itself prints pairs that do not convert
   exactly (flywheel: 390–490 N·m / 40–50 kg·m / 290–360 ft-lb).
   Recomputing would produce values that appear in no FSM. Rule: keep
   the FSM's printed pairs; fix only the note (§1 fix #4) and the five
   rows where the page's values differ from the FSM's printed pairs
   (fixes #3, #5, #6, #7, #8).
2. **Rear oil pressure regulator (51–59 ft-lb / 67–78 Nm) is correct
   as printed.** One review flagged it as the worst row; the
   FSM-reading review confirmed 67–78 N·m / 7.0–8.0 kg·m / 51–59 ft-lb
   is the FSM's own pair. Leave the row alone.
3. **Apex springs are 6 per rotor, not 3.** One review listed "3" as
   correct; the FSM-reading review found the FSM tabulates two springs
   per seal (Long + Short free-height rows) and Mazda part numbers
   N326-11-C04A (inner) and N326-11-C06C (outer), 6 of each per engine.
   Fix to 6 (§1 fix #12).
4. **Oil seal rings are 4 per rotor, not 2.** Same story: per-engine
   rebuild quantities are 8 oil seals / 8 springs. Fix to 4 (§1
   fix #15).
5. **S4 power is 136 kW, not 135 kW.** 182 hp × 0.7457 = 135.7 → 136.
   Wikipedia lists 136 kW. Fix (§1 fix #1).
6. **Quick-spool valve opens at ~2,700 rpm — keep it.** A forum post
   says 2,800, but the 1988 FSM §04B p. 4B-53 states 2,700. The forum
   figure loses.
7. **The torque table's ft-lb cells are NOT empty.** A text-only
   extraction showed blank ft-lb cells, but the values genuinely exist
   in the source (button text + `data-ft` attributes on all 27 rows).
   That is an extraction artifact, not a page defect. Do not
   restructure the table markup.

---

## §1 — ERROR fixes (apply all of these)

### Fix 1 — S4 power in kW is 135, should be 136
- **Locations:** spec table, `index.html:632`; JS `ENGINES`, line 1324
  (`kw: 135`).
- **Page says:** "182 hp (135 kW) @ 6,500" (S4).
- **Fix:** change to `182 hp (136 kW)` in the table and `kw: 136` in
  JS. S5's 149 kW is correct (200 × 0.7457 = 149.1).

### Fix 2 — Power-curve hp and torque data contradict each other
- **Locations:** JS `CURVES`, lines 1533–1538 (rendered by the
  "Representative power curve" chart).
- **Problem:** physics requires hp = torque × rpm / 5252 at every
  point. The plotted curves violate it by up to 41.6 hp (S5 at 6,500
  rpm: 200 hp listed, 158.4 hp implied by the listed 128 lb-ft). The
  page's "Shape, not gospel" caveat covers real-world variation, not
  two plotted lines contradicting each other.
- **Fix:** redraw one curve from the other so hp = tq × rpm / 5252
  holds at every point, keeping the rated peaks (S4: 183 lb-ft @
  3,500, 182 hp @ 6,500; S5: 196 lb-ft @ 3,500, 200 hp @ 6,500).
  Use these consistent torque sets (illustrative reconstruction, not
  measured dyno data — keep the chart labelled as such):
  - S4 tq at 2,000–7,000 rpm: `[150, 168, 178, 183, 180, 174, 166, 158, 159, 147, 128]`
    → hp `[57, 80, 102, 122, 137, 149, 158, 165, 182, 182, 171]`
  - S5 tq at 2,000–7,000 rpm: `[160, 178, 190, 196, 193, 188, 180, 172, 174, 162, 140]`
    → hp `[61, 85, 109, 131, 147, 161, 171, 180, 199, 200, 187]`
  - Alternative: keep the hp arrays and replace the torque arrays
    with tq = hp × 5252 / rpm.

### Fix 3 — Spark-plug torque is ~50% too high (over-torque risk)
- **Locations:** torque table, line 890; Rebuild Stage 8 chip, line
  1062 ("Plugs 14–19 ft-lb"); print card, line 1271. Fix all three,
  including the `data-ft` / `data-nm` attributes.
- **Page says:** spark plugs 14–19 ft-lb / 20–27 Nm.
- **Source:** 1988 FSM Technical Data §30, p. 30-4: **13–18 N·m /
  1.3–1.8 kg·m / 9.4–13 ft-lb**. Corroborated by FSM §5 (12.7–17.7
  N·m) and the S5-FSM list ("Spark Plug 113–156 in-lbs" = 9.4–13 ft-lb).
- **Fix:** `9.4–13 ft-lb (13–18 N·m)` in all three locations. The
  page's own note ("Aluminum housing; do not overtighten") is correct
  — the number contradicts it. Most safety-relevant torque error on
  the page.

### Fix 4 — Torque-table note: the "converted at 1.3558" claim is false
- **Location:** torque table note, line 898.
- **Page says:** "Units: values are converted at 1 lb-ft = 1.3558 Nm
  and rounded to the nearest whole number."
- **Problem:** the FSM's N·m column is not a conversion of its ft-lb
  column. Mazda's native unit is kg·m; N·m = kg·m × 9.80665, rounded
  independently. Example: flywheel 40–50 kg·m → 392–490 N·m, printed
  390–490, while 290–360 ft-lb × 1.3558 = 393–488.
- **Fix (line 898, units sentence only):** replace with: "Units: N·m
  and ft-lb are the factory manual's own paired figures (the manual's
  native unit is kg·m; its N·m and ft-lb columns were rounded
  independently, so the two columns do not convert exactly at
  1 lb-ft = 1.3558 Nm)." Do **not** recompute any N·m from ft-lb —
  that would produce values that appear in no FSM. Leave the rest of
  the note alone (the "where the two manuals differ" sentence is a
  §2 VERIFY item, not to be touched here).

### Fix 5 — "Turbo to manifold nuts": both columns wrong
- **Location:** torque table row, line 883 (both columns + attributes).
- **Page says:** 24–30 ft-lb / 32–40 Nm.
- **Source:** 1988 FSM §30: "Turbocharger" **44–54 N·m / 4.5–5.5
  kg·m / 32–40 ft-lb** (also FSM §4B p. 4B-57: 44.1–53.9 Nm).
  Diagnosis: the page's N·m column (32–40) is a copy of the FSM's
  ft-lb column.
- **Fix:** `32–40 ft-lb / 44–54 Nm`.

### Fix 6 — "Downpipe nuts": FSM figure is 33–39 / 44–54
- **Location:** torque table row, line 884.
- **Page says:** 26–34 ft-lb / 35–46 Nm.
- **Source:** 1988 FSM §4B p. 4B-57, turbocharger installation:
  "Front converter; 44.1–53.9 Nm (4.5–5.5 kg·m, 33–39 ft-lb)". The
  front converter is the pipe bolted to the turbo outlet — i.e. the
  downpipe joint. No other downpipe/front-pipe torque appears in §4B.
- **Fix:** `33–39 ft-lb / 44–54 Nm`. If the row means a different
  downstream joint, the FSM gives no torque for it at all — label the
  row unattributed instead.

### Fix 7 — "Driveshaft to differential": matches neither FSM table
- **Location:** torque table row, line 888.
- **Page says:** 32–43 ft-lb / 43–58 Nm. The low end (32 ft-lb) is
  below both FSM ranges.
- **Source:** 1988 FSM §30 §8 Propeller Shaft: "Propeller shaft to
  companion flange" **49–59 N·m (36–43 ft-lb)**. Same manual §9 Rear
  Axle: "Driveshaft (differential side)" **54–64 N·m (40–47 ft-lb)**.
- **Fix:** per the page's own spanning rule,
  `36–47 ft-lb / 49–64 Nm` (spans the §8 and §9 tables).

### Fix 8 — Wheel lug nuts: N·m column should be 90–120
- **Location:** torque table row, line 889 (Nm column + `data-nm`).
- **Page says:** 65–87 ft-lb / 88–118 Nm.
- **Source:** 1988 FSM §12 Wheels & Tires, p. 12-6: "Lug nut
  tightening torque: **90–120 Nm (9.0–12.0 kg·m, 65–87 ft-lb)**".
  The page converted from ft-lb instead of copying the FSM's N·m.
- **Fix:** `65–87 ft-lb / 90–120 Nm`.

### Fix 9 — Apex-groove standard range is model-year specific
- **Locations:** blueprint "Critical clearances" panel, line 822;
  Rebuild Stage 2 specs, line 948; wear-limit note, line 827.
- **Page says:** one standard range, 0.0020–0.0040 in, presented as
  the range from both the 1988 and 1989 FSMs.
- **Source:** 1988 FSM p. 30-2: RE 13B (Turbo) **0.051–0.101 mm
  (0.0020–0.0040 in)**. 1989 FSM p. TD-2: RE 13B (Turbo) **0.041–0.091
  mm (0.0016–0.0036 in)**. Wear limit 0.15 mm (0.0059 in) agrees in
  both years; the standard range does not.
- **Fix (lines 822, 948):** `Apex groove — 1988 Turbo: 0.0020–0.0040 in
  (0.051–0.101 mm); 1989 Turbo: 0.0016–0.0036 in (0.041–0.091 mm);
  limit 0.0059 in (0.15 mm) both years.` And line 827: replace
  "Standard ranges from the 1988 and 1989 factory service manuals."
  with "Standard ranges differ between the 1988 (S4) and 1989 (S5)
  factory service manuals — the apex-groove range tightened in 1989.
  Confirm the limit for your model year before assembly."

### Fix 10 — Corner-seal projection inequality is backwards
- **Locations:** Rebuild Stage 3 specs, line 967; JS
  `PARTS.corner`, line 1359.
- **Page says:** "Corner projection ≤ 0.020 in" — a maximum.
- **Source:** 1988 FSM p. 30-3: "Corner seal and spring — Protrusion
  limit: 0.50 mm (0.020 in)". 1989 FSM p. TD-3 relabels the same row
  **"Protrusion min. 0.50 (0.020)"**. Atkins: "Minimum Projection of
  the Corner Seal 0.02 in". 0.020 in is a **minimum** allowable
  protrusion — below it the seal is worn out.
- **Fix (both locations):** `Corner projection ≥ 0.020 in (0.50 mm
  minimum)`. A builder following the page as written would reject good
  seals and accept worn ones.

### Fix 11 — "Side seal max 0.016 in" misattributes the FSM spec
- **Locations:** blueprint "Critical clearances" panel, line 823;
  Rebuild Stage 3 specs, line 967; JS `PARTS.side`, line 1354;
  wear-limit note, line 827.
- **Page says:** a "side seal max clearance" of 0.016 in, as if it were
  the side seal's own clearance limit.
- **Source (1988 FSM p. 30-3; identical 1989 p. TD-3):** "Clearance
  of side seal and rotor groove": Standard 0.028–0.078 mm
  (**0.0011–0.0031 in**), Limit 0.10 mm (**0.0039 in**). "Clearance of
  side seal and corner seal": Standard 0.05–0.15 mm, **Limit 0.40 mm
  (0.016 in)** — a separate joint clearance where the side seal meets
  the corner seal.
- **Fix:** relabel all three locations `Side-to-corner seal clearance,
  max 0.016 in (0.40 mm)` and add the seal's own row to the clearances
  panel: `Side seal groove clearance 0.0011–0.0031 in, limit 0.0039 in
  (0.10 mm)`. Line-827 note: `…side-seal-to-corner-seal clearance
  limit 0.016 in (0.40 mm); side-seal-to-groove limit 0.0039 in
  (0.10 mm).`

### Fix 12 — Apex-spring count is 3 per rotor; it is 6
- **Location:** blueprint "Seal package" panel, line 811 (`Apex
  springs 3`).
- **Page says:** 3 apex springs per rotor (one per apex seal).
- **Source:** 1988 FSM p. 30-2 and 1989 FSM p. TD-2 both tabulate
  apex-spring free height as **two** springs, "Long" and "Short" —
  Long standard 6.25 mm (0.246 in), limit 4.6 mm (0.181 in); Short
  standard 3.3 mm (0.130 in), limit 1.7 mm (0.067 in). Mazda parts:
  stock springs N326-11-C04A (inner) and N326-11-C06C (outer),
  quantity 6 each per engine — 3 inner + 3 outer per rotor.
- **Fix (line 811):** `Apex springs — 6` (sub-label if the panel
  allows: `3 inner/short + 3 outer/long`). Per-engine total is 12,
  not 6 — a builder ordering from this panel would come up 6 short.

### Fix 13 — Rotor-cycle sentence is wrong by a factor of three
- **Location:** blueprint closing note, line 831.
- **Page says:** "…each face sees one intake, compression, power, and
  exhaust event per eccentric shaft revolution."
- **Source:** each rotor face completes one Otto cycle per **rotor**
  revolution; the rotor turns once per three eccentric-shaft
  revolutions, so one face needs **three shaft revolutions (1,080°)**
  to complete a cycle. A two-rotor engine delivers two power impulses
  per shaft revolution.
- **Fix (line 831):** "The rotor turns at one-third of the eccentric
  shaft speed, and each face completes one intake, compression, power,
  and exhaust cycle per rotor revolution — three eccentric-shaft
  revolutions. Because the three faces are phased 120° apart, a
  two-rotor engine still delivers two power impulses per
  eccentric-shaft revolution."

### Fix 14 — "Two of everything except the eccentric shaft and stationary gears" wrongly excepts the stationary gears
- **Location:** blueprint "Seal package" note, line 817.
- **Page says:** "Counts are for one rotor. A full 13B rebuild needs
  two of everything except the eccentric shaft and stationary gears."
- **Source:** a 13B has **two stationary gears per engine, one front
  and one rear** (1988 FSM lists "Front stationary gear plate" and
  "Rear stationary gear" as separate rows; the page's own JS
  `PARTS.stationary` says "One front, one rear"). Only the eccentric
  shaft is singular.
- **Fix (line 817):** "Counts are for one rotor. A full 13B rebuild
  needs two of everything except the eccentric shaft (one per engine).
  Stationary gears are two per engine — one front, one rear."

### Fix 15 — "Oil seal rings 2" per rotor undercounts the rings
- **Location:** blueprint "Seal package" panel, line 814.
- **Page says:** 2 oil seal rings per rotor.
- **Source:** each rotor face carries an oil seal made of an **inner
  ring + outer ring** (plus spring and O-rings). Per-engine rebuild
  quantities: **8 oil seals, 8 oil seal o-rings, 8 oil seal springs**
  — i.e. 4 rings, 4 springs and 4 O-rings per rotor.
- **Fix (line 814):** either `Oil seal rings — 4 (2 inner + 2 outer)`
  if the row counts rings, or relabel the row `Oil seals — 2 (one per
  face; each an inner + outer ring pair)`. Do not leave "Oil seal
  rings 2" as-is.

### Fix 16 — Oil-pressure quick spec contradicts FSM §2
- **Locations:** quick specs table, line 1165 ("Oil pressure — ≥ 30
  psi hot @ 3,000"); print card, line 1296.
- **Page says:** oil pressure ≥ 30 psi hot at 3,000 rpm.
- **Source:** 1988 FSM §2 Lubrication System, p. 2-5, OIL PRESSURE
  check: warm engine, run at 3,000 rpm — "Standard oil pressure:
  **441–539 kPa (4.5–5.5 kg/cm², 64–78 psi)**".
- **Fix (line 1165 and print card):** `Oil pressure — 64–78 psi @
  3,000 rpm warm`. An engine reading 35 psi at 3,000 rpm would pass
  the page's check and fail the FSM's. (The bare "Oil pressure ≥ 30
  psi hot" chips with no rpm stated are a separate §2 VERIFY item —
  do not change them here.)

### Fix 17 — Turbo II did not join the range at the 1985/1986 FC launch
- **Locations:** §01 timeline card, line 595 (dated 1985); hero
  lede, line 526; family intro, line 590.
- **Page says:** the Turbo II joins the range with the 1986 FC launch.
- **Source:** the FC launched for model year 1986 as a naturally
  aspirated car in the US. The Turbo II arrived for model year 1987
  (MotorTrend: "The RX-7 Turbo II arrived for 1987 with 182 hp";
  Grassroots Motorsports: turbo announced winter 1986 as a 1987
  model; Wikipedia: "optional turbocharged model, (1987–1988) known
  as the Turbo II"). JDM turbo FCs did exist from the October 1985
  Japanese launch, but this page is US-framed (SAE net ratings,
  "182 hp"), so the card is wrong as written.
- **Fix (line 595 card):** "The second-generation RX-7 arrives for
  1986 as a naturally aspirated car. The Turbo II joins the US range
  for 1987 with a twin-scroll Hitachi turbo, a quick-spool valve, and
  8.5:1 rotors." And line 590: "The FC Turbo II ran the 13B-T from
  1987 to 1991 in the US." And line 526: "The Mazda 13B-T turbo rotary
  engine powered the 1987–1991 FC RX-7 Turbo II."

### Fix 18 — "Dual-scroll" vs "twin-scroll" is a false distinction, and the S4 does not separate the rotors' exhaust pulses
- **Locations:** timeline, lines 595/597; specs intro, line 609; spec
  table, line 636; power section, lines 661–662.
- **Page says:** S4 = "dual-scroll" turbo; S5 = "twin-scroll" turbo —
  presented as two different turbo types — and the S4's turbine
  "separates the exhaust pulses from the two rotors."
- **Source:** "dual-scroll" and "twin-scroll" are synonyms: a divided
  turbine housing with two scroll passages. Both turbos are
  twin-scroll. The real difference is the control system: the S4 has
  a switching (quick-spool) valve that closes one scroll below
  ~2,700 rpm; the S5 has a fixed divided manifold with no valve.
  Haltech describes the S4 as a "twin scroll turbo with control
  diaphragm" and the S5 as a "larger turbo with no twin scroll" —
  "no twin scroll" there means no twin-scroll *control system*, not a
  different scroll architecture. Per-rotor pulse separation belongs to
  the S5: its manifold has "two independent passage[s]… fully
  separating the exhaust from each rotor" (RX7Club). The S4's two
  scrolls are a small primary and a larger secondary passage fed from
  a common collector; the flap closes one scroll below 2,700 rpm to
  raise gas velocity.
- **Fix:** use "twin-scroll" for both and let the valve/manifold do
  the distinguishing. Line 609: "…the switch from a twin-scroll turbo
  with a quick-spool (switching) valve to a twin-scroll turbo with a
  fixed divided manifold and no valve." Line 636: S4 "Hitachi HT-18S,
  twin-scroll with quick-spool (switching) valve"; S5 "Hitachi HT-18S,
  twin-scroll with divided manifold, no switching valve." Line 661:
  "The twin-scroll turbine has two passages, and a vacuum-operated
  switching valve closes one scroll below roughly 2,700 rpm to raise
  exhaust gas velocity and spool the turbo early — it opens above
  roughly 2,700 rpm to keep the top end from choking. That is the
  quick-spool system, and it is the extra plumbing the Series 4
  carries."

### Fix 19 — The stack diagram and dry-stack step omit the intermediate (center) plate
- **Locations:** §06 Stage 05 step 1, line 1000; stack-order diagram,
  lines 1089–1096; diagram arrow labels, lines 1096–1097.
- **Page says:** "Dry-stack the front side plate, rotor housing,
  rotor, rotor housing, and rear side plate." Diagram: FRONT COVER →
  FRONT SIDE PLATE → ROTOR HOUSING + ROTOR → ROTOR HOUSING + ROTOR →
  REAR SIDE PLATE → REAR COVER.
- **Source:** a 13B has **three** side housings: front, intermediate
  (center), and rear. The intermediate plate sits between the two
  rotor housings (Mazda assembly order: front housing → front
  stationary gear → front rotor → front rotor housing → **intermediate
  housing** → rear rotor → rear rotor housing → rear housing). A
  builder following the page literally would stack two rotor housings
  against each other with no center plate — the engine cannot be
  assembled that way.
- **Fix (line 1000):** "Dry-stack the front side plate, front rotor
  housing and rotor, intermediate (center) plate, rear rotor housing
  and rotor, and rear side plate. Confirm the order and the dowel pins
  before any sealant goes on." **Fix (diagram):** FRONT COVER →
  FRONT SIDE PLATE → ROTOR HOUSING + ROTOR → INTERMEDIATE (CENTER)
  PLATE → ROTOR HOUSING + ROTOR → REAR SIDE PLATE → REAR COVER.
  **Fix (arrow labels):** the horizontal arrows on a vertical stack
  read backwards — replace `← front` / `rear →` with `TOP = FRONT` /
  `BOTTOM = REAR`. (Secondary note: the front and rear covers bolt on
  after the tension-bolt stack is clamped, so including them in the
  "stack order" is a simplification; the critical omission is the
  intermediate plate.)

### Fix 20 — The tension bolts do not "set" rotor side clearance
- **Locations:** §06 Stage 06 "Why it matters", line 1025; blueprint
  hotspot text, `PARTS.tension`, line 1373.
- **Page says:** "The tension bolts set rotor side clearance. Uneven
  torque means uneven clearance, and an uneven stack is a short-lived
  engine." / "They set rotor side clearance."
- **Source:** rotor-to-side-housing clearance is a machined-dimension
  clearance, computed from measurements — Mazda's procedure: rotor
  and side housing clearance = (rotor housing width) − (maximum rotor
  width), measured with a micrometer; if below the minimum, replace
  the rotor. The tension bolts clamp the stack. Uneven torque can
  distort housings and bind the rotor, but it does not set the
  clearance.
- **Fix (line 1025):** "The tension bolts clamp the stack evenly.
  Rotor side clearance is set by the machined widths of the rotor and
  housings and is verified by measurement — uneven torque distorts
  the stack and can bind the rotor, but it does not set the
  clearance." **Fix (line 1373):** "Six bolts clamp the entire housing
  stack. They must be torqued evenly in stages so the stack clamps
  flat; rotor side clearance itself is set by the machined widths of
  the rotor and housings."

### Fix 21 — The housing face is hard chrome plating, not "chrome-carbide"
- **Locations:** §04 blueprint hotspot default text, line 792; §06
  Stage 01 step 4, line 927; `PARTS.housing`, line 1343.
- **Page says:** "The chrome-carbide face is not bored or honed…" /
  "Inspect the chrome-carbide housing faces…"
- **Source:** production 13B rotor housings are **hard chrome
  plated** (JHB Performance: "Mazda OEM rotor housings are hard chrome
  plated"; US Patent 4,729,729: rotor housing inner wall "applied with
  hard chromium plating layer"). A "chrome-carbide base cermet"
  coating is the R26B Le Mans 4-rotor engine's housing coating (Mazda
  SAE paper 920309) — not a production 13B's.
- **Fix (lines 792 / 1343):** "The hard-chrome-plated face is not a
  DIY bore-or-hone job; if it is flaking, chattered, or worn past
  spec, the housing is scrap." **Fix (line 927):** "Inspect the
  hard-chrome housing faces for flaking, chatter, and apex-seal track
  wear." Wording note: keep the warning but admit the specialist
  exception — "The hard-chrome face is not a DIY bore-or-hone job —
  specialist reconditioning can remove only a few thousandths of
  chrome, and a housing that is flaking, chattered, or worn past spec
  is scrap."

---

## §2 — VERIFY (do NOT change these — owner decision needed)

These need the owner to check the FSM (the foxed.ca PDFs are scanned
images; text fetch could not verify them) or make a judgment call.
Copilot: leave every item below exactly as it is.

### V1 — Boost: page's 5.5 / 7.5 psi vs FSM Technical Data 6.56 / 8.25 psi
- **Locations:** spec table line ~631; hero gauge/KPI lines ~575,
  587; JS `ENGINES` lines 1325/1330; table note line 647; Stage 8
  chip line 1062; README.md:23.
- **Problem:** 1988 FSM Technical Data p. 30-8 lists boost pressure
  45.2 kPa (**6.56 psi**); 1989 FSM p. TD-6 lists 57.0 kPa
  (**8.25 psi**). The page's 5.5 / 7.5 psi are ~1 psi lower in both
  series. The README and the page are never reconciled — a reader
  opening both sees two different "factory" boost numbers with no
  explanation.
- **Why not changed:** 5.5 / 7.5 are the widely published
  manifold-boost figures, and the page's own note ("a healthy stock S4
  typically logs 5–6 psi and an S5 7–8 psi") matches what a gauge
  reads. The likely explanation is measurement point (FSM figure at
  the turbocharger outlet, before intercooler/piping pressure drop;
  manifold sees ~1 psi less), but it could not be confirmed.
- **Owner action:** verify the measurement point in FSM 04b/04B
  ("Boost Pressure Inspection" — test conditions, rpm, solenoid
  state). If confirmed, add a footnote: "FSM Technical Data lists
  boost pressure as 6.6 psi (S4) / 8.3 psi (S5) at the turbocharger;
  ~5.5 / 7.5 psi at the manifold after intercooler drop."

### V2 — Fuel cut "~7,200 rpm" could not be confirmed
- **Locations:** spec table line 635; ignition table line 1154; print
  card line 1261; table note line 647.
- **Problem:** no fuel-cut rpm appears in the FSM Technical Data
  pages read. RX7Club threads report the FC fuel cut higher and
  inconsistently (S4 limiter "around 7800", owners to ~7,500 before
  cut, S5s to ~8,000–8,500). The 7,000 rpm redline itself is correct
  for both turbo series (only the S5 **NA** got 8,000 rpm).
- **Owner action:** soften or source the number, e.g. "7,000 rpm
  redline; fuel cut above redline (exact rpm varies by ECU/year —
  verify against FSM engine-control section)". Also distinguish the
  **rpm** fuel cut from the separate **overboost** fuel cut: on turbos
  the factory ECU cuts fuel to the rear rotor at ~8.5 psi of boost.

### V3 — Bare "Oil pressure ≥ 30 psi hot" chips (no rpm stated)
- **Locations:** Stage 1 chips line 929; print card line 1296.
- **Problem:** FSM §2 standard is 64–78 psi @ 3,000 rpm warm (see
  §1 fix #16); no hot-idle oil-pressure spec was found in §2.
- **Owner action:** if the chip is meant as a hot-idle floor, label
  it: "Oil pressure ≥ 30 psi hot idle (shop rule of thumb — FSM spec
  is 64–78 psi @ 3,000 rpm warm)". Otherwise replace with the FSM
  figure per fix #16.

### V4 — Premix "1 oz / US gal if OMP deleted" is community practice, not a factory figure
- **Locations:** quick specs line 1171; print-card footer line 1302.
- **Problem:** no premix ratio appears in the FSM (it assumes the
  OMP). 1 oz/gal (≈128:1) is the widely used community ratio for a
  deleted OMP.
- **Owner action:** no value change; optionally tag it "(community
  standard — not an FSM figure)".

### V5 — "Intercooler pipe clamps" 4–6 ft-lb / 5–8 Nm: no FSM spec for hose clamps
- **Location:** torque table row, line 885.
- **Problem:** the FSM has no torque spec for intercooler hose/pipe
  clamps. Closest FSM figure is "Intercooler bolts" 69–95 in-lb =
  5.8–8.0 ft-lb (S5-FSM list) — mounting bolts, not clamps.
- **Owner action:** either rename the row to "Intercooler mounting
  bolts" with the FSM value 5.8–8.0 ft-lb / 8–11 Nm, or keep the clamp
  row and label it shop practice ("snug only" is already in the Notes
  column — the value itself needs the same treatment).

### V6 — "Bellhousing bolts" 28–38 ft-lb: probably 1 unit high at the low end
- **Location:** torque table row, line 887.
- **Problem:** S5-FSM list: "Trans to Engine bolts 27–38 ft-lbs".
  FSM §7A gives no torque for transmission installation at all. The
  §7B automatic-transmission table's "Converter housing to engine
  31–46 N·m / 23–34 ft-lb" is a different joint.
- **Owner action:** likely corrected value **27–38 ft-lb / 37–52 Nm**.
  Owner to confirm against the 1989 FSM procedural sections.

### V7 — "Knock sensor" 15–25 ft-lb: the FSM states no torque for it
- **Location:** torque table row, line 891.
- **Problem:** no knock-sensor torque row in the 1988 FSM §30
  tables, and FSM §4B (p. 4B-52) gives no torque for knock-sensor
  installation either. The Atkins FD sheet lists "Knock Sensor
  15–25 ft/lb" — same numbers, but a different engine and not an FC
  authority.
- **Owner action:** keep the row only as an unattributed/shop-practice
  value, or remove it.

### V8 — "Where the two manuals differ, the range spans both" is unverifiable from the two named Technical Data PDFs
- **Location:** torque table note, line 898 (the spanning sentence —
  distinct from the units sentence fixed in §1 fix #4).
- **Problem:** the 1989 FSM Technical Data PDF contains **no
  tightening-torque tables at all** (only the standard bolt/nut
  table). No 1988-vs-1989 torque difference could be verified from
  the two Technical Data PDFs; the 1989 values would have to come from
  the 1989 FSM's procedural sections.
- **Owner action:** either verify the 1989 torque figures against the
  1989 FSM procedural sections before claiming the ranges span both
  manuals, or reword the note to say the values are from the 1988 FSM
  (with 1989 cross-checks where noted). Note: the apex-groove year
  split (§1 fix #9) is the one 1988-vs-1989 difference actually
  established in this review — from the Technical Data tables, which
  do exist in both years for clearances.

### V9 — Apex-seal construction (2-piece vs 3-piece) is not stated on the page
- **Location:** blueprint "Seal package" panel, line 808 (`Apex seals 3`).
- **Problem:** nothing on the page is wrong — 3 apex seals per rotor
  is correct as a count. But whether the S4/S5 Turbo apex seal is a
  2-piece or 3-piece assembly could not be confirmed. The FSM lists
  apex-seal height as "upper and lower" (standard 8.0 mm, limit
  6.5 mm), which implies a two-piece (upper + lower) seal, while some
  secondary sources describe 3-piece 2 mm seals for the FC era.
- **Owner action:** do not add "3-piece" or "2-piece" wording without
  checking the FSM Engine Inspection section first.

### V10 — "Rotor bearing is the highest-load bearing in the engine" is unsourced
- **Locations:** Stage 04 "Why it matters", line 987; `PARTS.bearing`,
  line 1378.
- **Problem:** the bearing type is right (caged needle bearing in the
  rotor hub; one per rotor) and "heavily loaded" is plausible, but no
  authoritative source was found stating it is the *highest-load*
  bearing in the engine — the main bearings in the stationary gears
  also carry full shaft loads. The superlative is repeated twice
  without a source.
- **Owner action:** soften until sourced: "The rotor bearing is one of
  the most heavily loaded bearings in the engine. Brinelling from
  detonation or low oil pressure is common on a high-boost car —
  replace it while you are in there."

### V11 — "Recheck the tension bolts" after heat-cycling is not a documented FSM step
- **Locations:** Stage 08 step 4, line 1060.
- **Problem:** rechecking *exhaust* fasteners after heat cycles is
  standard practice, but no Mazda FSM procedure requires a
  tension-bolt recheck after heat cycling. A torque recheck on an
  already-torqued bolt also reads breakaway torque, not clamp load,
  so a "recheck" can mislead a builder into over-torquing.
- **Owner action:** change to "Heat-cycle the engine, recheck the
  exhaust fasteners, then compression-test and boost-leak-check before
  any load." (Drop the tension-bolt recheck, or label it explicitly as
  a shop check rather than an FSM step.)

### V12 — Project JDM link contradicts the page on torque and mislabels power
- **Location:** `index.html:1228` (`https://projectjdm.org/wiki/specs/rx7-fc`).
- **Problem:** the linked page lists **"Torque 260 Nm (192 lb-ft) @
  3,500 rpm"**, contradicting the page's 196 lb-ft (266 Nm) and
  RX7.org's 196 lb-ft. It also lists the Series 4 as **"185 HP"**
  (that is 185 PS, the JDM figure — 185 PS ≈ 182 hp), lists only
  **8.5:1** compression (omitting the S5's 9.0:1), and writes
  **"200 PS (197 HP)"**, conflating PS and hp.
- **Owner action:** remove the Project JDM link from the Sources
  section, or annotate it as a JDM/market summary that conflicts with
  the FSM on torque ("JDM summary — its 260 Nm / 192 lb-ft torque
  figure conflicts with the FSM and RX7.org; do not use it for
  torque").

### V13 — AusRotary thread does not corroborate 7.5 psi as the link description claims
- **Location:** `index.html:1230` (described as "Community
  corroboration for 7.5 psi stock boost").
- **Problem:** the thread (2005, "Stock boost on S5 13BT =?") never
  states 7.5 psi. Values actually reported: "6–8 PSI", "5 psi on the
  boost gauge", "0.4 bar" (~5.8 psi), "7/8 psi" (after the stock BOV
  was removed), plus the false claim that S5s "run 6 psi until they
  have done 30,000k's and then it was sposed to change to 12 psi" (a
  myth, not corrected in the fetched text).
- **Owner action:** reword the link description to *"Community
  discussion of stock S5 boost readings (5–8 psi reported)"* or remove
  the link.

### V14 — Mazda 13B/8C PDF is the wrong 13B for "compression reference"
- **Location:** `index.html:1231` (described as "13B geometry,
  displacement, and compression reference").
- **Problem:** the paper is about the 8C engine vs the **2008MY
  13B-MSP (RX-8)**. Its Table 1 confirms the geometry the page uses
  (654×2 cc, e=15, R=105, b=80) — but its 13B compression ratio is
  **10.0:1**, not the FC's 8.5:1 / 9.0:1.
- **Owner action:** reword the description to *"13B geometry and
  displacement reference (2008MY 13B-MSP; its 10.0:1 compression
  differs from the FC's 8.5/9.0:1)"*.

### V15 — "Eccentric shaft lock bolt" 80–98 ft-lb can be confused with the front hub bolt (180–200 ft-lb)
- **Locations:** torque table row line 869; Stage 07 line 1043.
- **Problem:** the cited Atkins sheet lists a different fastener,
  **"Front Eccentric Shaft Hub Bolt 180–200 ft-lb"**, which is not the
  same bolt as the page's pulley-boss lock bolt. A reader
  cross-checking the Atkins sheet could apply 180–200 ft-lb to the
  lock bolt (or 80–98 to the hub bolt). The page never names the two
  bolts distinctly. The 80–98 ft-lb figure itself is FSM-verified.
- **Owner action:** confirm 80–98 ft-lb against the FC FSM, then
  rename the row to **"Eccentric shaft lock bolt (pulley boss)"** and,
  if the FSM lists the front hub bolt for the FC, add it as a separate
  row so the two are never confused.

---

## §3 — NIT fixes (apply in the same pass)

### N1 — "79.8 cu in" vs FSM "80 cu in"
- **Location:** JS line 1430 (`'79.8 cu in · 654 × 2'`).
- **Fix (optional):** use "80 cu in (40.0 × 2)" to match the FSM's own
  figure (both FSMs list displacement as "654 × 2 (40.0 × 2)" cu in).
  1,308 cc = 79.82 cu in is arithmetically exact; the FSM rounds
  per-rotor to 40.0.

### N2 — Waste-spark note overgeneralises: only the leading coil is waste-spark
- **Location:** reference section, waste-spark inline note (~line 1156).
- **Page says:** "the FC ignition fires paired plugs on the exhaust
  stroke to simplify the coil architecture."
- **Fix:** "The leading coil is a dual-post waste-spark coil (one
  leading plug fires on its exhaust stroke); the trailing plugs are
  fired in turn by the toggle igniter."

### N3 — S5 torque hedge is adequate; strengthen it with the metric anchor
- **Location:** table note, line 647.
- **Page says:** "Series 5 is sometimes quoted as 195 lb-ft (264 Nm)
  rather than 196 lb-ft (266 Nm) — both appear in period road tests,
  so treat it as ±1 lb-ft."
- **Fix (optional):** add the anchor: "…the underlying figure is 265
  Nm (195.5 lb-ft), so the 195/196 split is rounding."

### N4 — Timing row could carry the trailing figure and tolerances
- **Location:** ignition table, line 1152.
- **Fix (optional):** "Leading 5° ATDC (±1°) · Trailing 20° ATDC ·
  test/check connector grounded" (1988 FSM: Leading 5° ATDC, Trailing
  20° ATDC; 1989 FSM: Leading 5° ± 1° ATDC, Trailing 20° ± 2° ATDC).
  The FSM plug-gap figures differ by year (1988: 2.0 mm; 1989:
  1.4 mm) — the page wisely quotes no gap ("Surface-gap · do not
  adjust"), which is the right call; do not add a gap.

### N5 — Oil row: 1989 FSM specifies mineral oil only
- **Location:** quick specs, line 1170 ("10W-30 · 20W-50 in heat").
- **Fix (optional):** "10W-30 · 20W-50 in heat · mineral oil (per 1989
  FSM)" (1989 FSM adds API service "Fuel efficient" SF **(Mineral
  only)**).

### N6 — Timeline "1991: FD3S arrives" needs a market qualifier
- **Location:** timeline, line 599.
- **Fix (optional):** "The FD3S arrives in Japan in late 1991 (US 1993
  model year) with the 13B-REW twin-turbo." (FD launched in Japan
  October 1991; US model year 1993. The 255 hp figure is the US
  rating; JDM was 255 PS = 252 hp.)

### N7 — Exhaust manifold ft-lb 24–33 vs 23–34: the FSM contradicts itself; span both
- **Locations:** torque table row, line 882; print card, line 1272.
- **Page says:** 24–33 ft-lb / 31–46 Nm.
- **Source:** 1988 FSM §30 engine table: 31–46 N·m / 3.2–4.7 kg·m /
  **23–34 ft-lb**. The same manual's EGI sections give 31.4–46.1 N·m
  (**24–33 ft-lb**). The page's 24–33 matches the EGI tables; its
  31–46 N·m matches both.
- **Fix:** `23–34 ft-lb / 31–46 Nm` (spans both FSM tables, per the
  page's own spanning rule).

### N8 — Flywheel nut: the FSM gives two different ranges in two sections
- **Locations:** torque table row, line 868; warn box, line 845;
  Stage 8 step, line 1057; Stage 8 chip, line 1062; print card,
  line 1270.
- **Page says:** 290–360 ft-lb (390–490 Nm). No change strictly
  required — the page follows the §30 engine table, which is
  defensible. FSM §6 Clutch installation (p. 6-16) says
  **400–500 N·m (40–50 kg·m, 289–362 ft-lb)**; the S5-FSM list agrees
  ("Flywheel Nut MT 289–362 ft-lbs").
- **Fix (optional):** if the page wants one number everywhere, note
  the §6 figure in the flywheel row's Notes column.

### N9 — Intake manifold: §30 engine table vs EGI-section tables differ at the low end
- **Location:** torque table row, line 881.
- **Page says:** 14–19 ft-lb / 19–25 Nm (matches §30 engine table).
  The EGI sections give 19.1–26.0 N·m (1.95–2.65, **15–19** ft-lb).
- **Fix (optional):** per the page's spanning rule the row could read
  14–19 ft-lb / 19–26 Nm. Trivial.

### N10 — "Clean, lightly oiled threads" vs the FSM's lug-nut instruction
- **Location:** §05 intro, line ~849 ("Values are for clean, lightly
  oiled threads unless noted").
- **Fix:** add "except wheel lug nuts — the FSM says never oil lug
  nuts, bolts, or wheels" to the §05 intro or to the lug-nut row's
  Notes column (1988 FSM §12: "Never apply oil to the nuts, bolts, or
  wheels: doing so might cause looseness or seizure of the lug nuts.").

### N11 — "New flywheel/counterweight nut" is shop practice, not an FSM instruction (and the FSM's actual steps are missing)
- **Locations:** torque-table note, line 868; warn box, line 845;
  Stage 8 step 1, line 1057.
- **Page says:** "New nut" / "Fit a new flywheel/counterweight nut".
- **Source:** neither FSM §6 Clutch installation nor FSM §1 Engine
  assembly (p. 1-62) says to fit a new locknut. Both say: apply
  threadlocking compound to the eccentric shaft threads, apply sealant
  to the contact surface of the locknut, fit the ring-gear brake and
  tighten the nut with the flywheel box wrench to 390–490 N·m
  (290–360 ft-lb).
- **Fix (Stage 8 step 1, line 1057):** "Fit the flywheel/counterweight
  nut — a new nut is recommended — with threadlocking compound on the
  shaft threads and sealant on the nut face, and torque it to 290–360
  ft-lb with a holding tool." Fitting a new nut is sensible practice
  and does not contradict the FSM — but it is not what the FSM
  instructs, and the page currently omits what the FSM *does*
  instruct.

### N12 — "Never impact the eccentric shaft" is shop practice, not FSM wording
- **Locations:** torque-table note, line 868; warn box, line 845.
- **Source:** no such instruction was found in FSM §1 (all 83 pages
  searched) or §6. The FSM's method is the ring-gear brake + flywheel
  box wrench, which inherently keeps reaction torque off the shaft.
- **Fix:** label it as recommended practice; do not present it as an
  FSM instruction. Sound advice, consistent with the FSM's
  holding-tool method.

### N13 — "Three passes" for tension bolts is shop practice, not the FSM's instruction
- **Locations:** torque-table note, line 866 ("Crisscross, three
  passes"); Stage 6 title (~line 1013), step 1 (line 1019), chips
  (line 1024); diagram SVG text, line 1082.
- **Source:** 1988 FSM §1 Assembly (p. 1-61): "Install the tension
  bolts and tighten them **gradually in the order shown in the
  figure**. Tightening torque: 31–39 N·m (3.2–4.0 kg·m, 23–29 ft-lb)."
  No pass count is specified anywhere in §1. The FSM's order is the
  numbered 18-position pattern in Fig. 67U01X-163, not a 6-bolt
  1↔4/2↔5/3↔6 diagram (the page's diagram is a simplification; its
  caption correctly tells the reader to confirm the factory sequence).
- **Fix (Stage 6 step 1, line 1019):** "Torque the six tension bolts
  gradually in the FSM's numbered order (Fig. 67U01X-163), alternating
  opposite bolts — three equal passes is the usual shop method. Bring
  the stack down evenly; do not finish one side before starting the
  other." Keep the "three passes" wording in the title/chips only with
  a "(shop method)" qualifier, or drop the count there. **Diagram:**
  label it "Illustrative opposite-pair pattern — confirm the factory
  sequence in the FSM for your model year" and keep the caption as is.

### N14 — Stage 6 step 4 implies the FSM may specify a tension-bolt stretch limit; it does not (in §1)
- **Location:** Stage 6 step 4, line 1022.
- **Page says:** "Check tension-bolt length if the FSM specifies a
  stretch limit. Replace any bolt that is out of range."
- **Source:** FSM §1 contains no tension-bolt length or stretch limit.
- **Fix (line 1022):** "The FSM gives no stretch/length limit for the
  tension bolts — inspect them for damaged threads or visible necking
  and replace any suspect bolt; always fit the new seal washers the
  FSM specifies." (The FSM does specify new seal washers and oiled
  threads for the tension bolts — the table note covers the oiling but
  never mentions the new seal washers.)

### N15 — "Apex seal 2.0 mm" is the nominal size class, not the measured seal thickness
- **Locations:** Stage 3 specs, line 967; JS `PARTS.apex`, line 1349.
- **Source (1988 FSM p. 30-2; identical 1989):** apex-seal width
  1.910–1.939 mm (0.0752–0.0763 in); apex-seal groove width
  1.995–2.012 mm (0.0785–0.0792 in). "2.0 mm" is the size class (vs
  3 mm seals), not a measurement.
- **Fix:** `Apex seal 2.0 mm (nominal; actual 1.910–1.939 mm)` / JS:
  `3 per rotor · 2.0 mm nominal`.

### N16 — Apex hotspot tells the reader to check "spring free height" but the page states the spec nowhere
- **Location:** JS `PARTS.apex` text, line 1348.
- **Fix:** add the values to the hotspot or the clearances panel:
  `Apex springs — free height: long 6.25 mm std / 4.6 mm limit; short
  3.3 mm std / 1.7 mm limit.` (1988 FSM p. 30-2 / 1989 FSM p. TD-2;
  identical in both years. This also corroborates §1 fix #12: two
  springs per seal.)

### N17 — Rotor side clearance has a minimum limit the wear-limit note omits
- **Location:** blueprint wear-limit note, line 827.
- **Source (1988 FSM p. 30-2; 1989 FSM p. TD-2):** "Clearance of side
  housing and rotor — Standard 0.12–0.21 mm (0.0047–0.0083 in);
  **Limit 0.10 mm (0.0039 in)**." Unlike the other limits in the
  note, this one is a **minimum**: below 0.10 mm the rotor binds.
- **Fix:** append to the line-827 note: `Rotor side clearance also
  has a minimum limit of 0.0039 in (0.10 mm).`

### N18 — "Behaves like a larger engine in airflow terms" is vague; the standard equivalence is citable
- **Location:** blueprint closing note, line 831.
- **Fix (optional):** "…That is why a 1.3 L rotary breathes like a
  2.6 L four-stroke in airflow terms…" (A two-rotor Wankel displacing
  654 cc per face inhales 1.3 L per eccentric-shaft revolution, 2.6 L
  after two revolutions.) Not an error — qualitative judgement, a
  sharpening opportunity only.

### N19 — "Oil rings" is the wrong term for a rotary
- **Location:** §07 troubleshooting table, line 1199 ("Oil
  consumption" row).
- **Page says:** likely cause listed as "OMP rate, turbo seal, oil
  rings."
- **Fix:** "OMP rate, turbo seal, oil seal rings." (A 13B has no
  piston-style oil rings; oil control on the rotor sides is done by
  the oil seal rings.)

### N20 — Print card footer names a file that does not exist
- **Location:** `index.html:1303` (`<span>rx7-13bt-tech.html</span>`).
- **Fix:** change the text to `index.html` or to the live URL
  `ruddyrid.github.io/20261107-rx7-13bt-tech/`.

### N21 — "US SAE" rating toggle is a dead control
- **Location:** `index.html:674` (a single button with no alternative
  and no click handler above the series comparison bars).
- **Fix:** remove the control, or implement a JDM (PS) toggle that
  swaps the bar values to 185/205 PS and 264/266 Nm.

### N22 — SAE/Metric toggle does not cover clearances, compression, or stage chips
- **Locations:** clearances lines 822–825 (inches only), compression/
  fluids lines 1153–1175 (psi/°F only), stage spec chips lines
  948/967/1024/1043/1062 (ft-lb and inches only). The toggle only
  changes the gauge, the KPI cards, and the torque table.
- **Fix:** add metric equivalents to the clearance card (apex groove
  0.05–0.10 mm, side seal max 0.4 mm, rotor side clearance 0.12–0.21
  mm, end play 0.04–0.07 mm) and to compression (≥85 psi ≈ 586 kPa /
  5.9 bar per face), or add a short note that the unit toggle covers
  torque and gauge values only.

### N23 — README: duplicate live URL as a "temporary preview"
- **Location:** `README.md:7` and `README.md:9` — identical URLs.
- **Fix:** delete line 9.

### N24 — README Sources list omits three sources the page lists
- **Location:** `README.md:55–63`.
- **Fix:** add "Project JDM FC3S spec, Wikipedia Mazda RX-7, and the
  AusRotary S5 boost discussion" to the sentence, or change it to "and
  the secondary references linked on the page."

### N25 — README deployment steps are correct, but GitHub domain verification is missing
- **Location:** `README.md:41–52`.
- **Fix (optional addition):** add a step to verify the domain in
  GitHub (Settings → Pages → verify with the `_github-pages-challenge`
  TXT record) to prevent another account claiming
  `tech.tealsunltd.com`. Also note the DNS change can take up to 24 h
  to propagate before HTTPS can be enabled.

### N26 — Rebuild checklist persists per browser only, and the page doesn't say so
- **Locations:** progress card lines 906–911; localStorage key
  `rx7-13bt-build-v1`, lines 1608–1625.
- **Fix:** add a small line under the progress card: *"Checklist is
  saved in this browser only."*

### N27 — README boost figure: 8.25 → 8.27 psi
- **Location:** `README.md:23` ("The 1989 FSM lists a 57 kPa (8.25
  psi) boost-pressure spec").
- **Fix:** 57 kPa = 8.27 psi. Pure arithmetic — this does not resolve
  the §2 V1 question (whether 57 kPa is a manifold or
  turbocharger-outlet figure), it only corrects the conversion.

### N28 — Torque values inside `<button>` elements (SEO, optional, low priority)
- **Location:** `index.html:866–892`.
- **Note:** not a rendering bug — the values display fine. But a
  text-only crawl of the live page renders the ft-lb column blank,
  which weakens search indexing of the page's most-used numbers.
- **Fix (optional):** none required for readers. If SEO matters,
  consider plain text in the ft-lb cell with the copy action on a
  small adjacent icon, or add a `<noscript>`/plain-text fallback.

---

## §4 — DO NOT TOUCH (verified correct)

These were checked against the same sources and hold up. Do not
"correct" them — especially do not "correct" an FC value to the FD
figure.

- **Spec table:** displacement 1,308 cc (654 × 2); rotor width 80 mm;
  eccentricity 15 mm; generating radius 105 mm; compression 8.5:1 S4
  / 9.0:1 S5 (the 9.7:1 figure belongs to the S5 NA, not the turbo);
  power/torque 182 hp @ 6,500 / 183 lb-ft @ 3,500 and 200 hp @ 6,500 /
  196 lb-ft @ 3,500 (S5 195 lb-ft also published — see §3 N3);
  specific output 139 / 153 hp/L; bar conversions 0.38 / 0.52; redline
  7,000 rpm both turbo series (8,000 rpm belongs only to the S5 NA);
  OMP, TPS, intercooler pipe, and ignition splits exactly as Haltech
  states them; fuel system "Port injection, 4 injectors" (primary +
  secondary, 550 cc each); transmission 5-speed manual, both (Turbo
  II was manual-only).
- **Ignition/reference:** firing order 1-2 (front → rear); rotor
  phasing 3:1 eccentric : rotor; plugs NGK BUR7EQ (leading) / BUR9EQ
  (trailing); base leading timing 5° ATDC, check connector grounded;
  idle 725–775 rpm; compression ≥ 85 psi / face @ 250 rpm, ≤ 21 psi
  spread; "strong engine typically reads 100–120 psi".
- **Fluids, all:** engine oil (dry) 5.8 L / 6.1 qt; coolant (with
  heater) 8.7 L / 9.2 qt; transmission 2.5 L / 2.6 qt; differential
  1.3 L / 1.4 qt; fuel tank 63 L / 16.6 gal (S4) and 70 L / 18.5 gal
  (S5); brake fluid DOT 3 or DOT 4; oil viscosity 10W-30 / 20W-50 in
  heat; coolant temp 180–200 °F normal; octane 91+ (US pump).
- **JDM/timeline:** S4 185 PS, S5 205 PS, Infini IV 215 PS; FD3S
  13B-REW 255 hp as the US rating (JDM: 255 PS = 252 hp); FC
  production from September 1985 (1986 model year); S4 1986–88; S5
  1989–91; Infini IV June 1990–February 1991.
- **Torque table, 17 of 27 rows match the FSM exactly:** tension
  bolts 23–29, stationary gear bolts 12–17, flywheel nut 290–360,
  eccentric shaft lock bolt 80–98, front pulley bolts 5.8–8.0, front
  cover bolts 12–17, oil pan bolts 5.8–8.0, oil pump bolts 5.1–7.2,
  oil pump sprocket nut 23–34, rear oil pressure regulator 51–59 /
  67–78, OMP bolts 5.8–8.0, oil strainer bolts 5.1–7.2, water pump
  bolts 13–20, water pump housing nuts 14–17, thermostat housing bolts
  14–17, intake manifold bolts 14–19, CAS lock bolt 5.8–8.0. Pressure
  plate bolts 13–20 verified against FSM §6.
- **Blueprint/clearances:** geometry R 105 / e 15 / b 80 mm;
  displacement formula arithmetic (654 cc/rotor, 1,308 cc total);
  rotor at ⅓ shaft speed; seal counts apex 3 / corner 6 / side 6 /
  corner springs 6 / side springs 6 / bearing 1 per rotor; rotor side
  clearance 0.0047–0.0083 in; shaft end play 0.0016–0.0028 in, limit
  0.0035 in; apex groove wear limit 0.0059 in; tension bolts 6 per
  engine at 23–29 ft-lb. Do not "correct" any FC value to the FD
  figure: the Atkins sheet differs on rotor side clearance (FD
  0.0048–0.0066 in vs FC 0.0047–0.0083 in) and tension bolts (FD
  24–28 vs FC 23–29), and the page's FC values are correct.
- **Claims verified correct:** toggle trail igniter terminology;
  quick-spool valve opens above ~2,700 rpm; S5 duty-cycle boost
  solenoid; S4 mechanical rod-type OMP vs S5 electric stepper-motor
  OMP; single TPS (S4) vs dual TPS (S5); straight (S4) vs tapered
  (S5) intercooler feed pipe; firing order 1-2; rotor phasing 3:1;
  displacement formula V = 3√3 · e · R · b = 654 cc per rotor; seal
  install order (apex, then corner, then side seals; oil seal rings to
  the rotor sides); pre-oiling before first start ("do not dry-crank
  a fresh rotary"); "the tension bolts are not interchangeable with
  cover bolts"; keep housings and side plates paired and marked;
  "fit the main bearings into the stationary gears and the rotor
  bearing into the rotor hub"; "sealant only where the FSM specifies";
  "never let the eccentric shaft take the reaction through the rotor"
  (flywheel nut warning).
- **Consistency verified:** exactly 8 rebuild stages; idle 725–775
  rpm identical in three places; fuel tank 63 L / 70 L identical
  everywhere; tension-bolt 23–29 ft-lb identical in four places; stage
  07/08 torque values match the table; power curve and bar charts are
  clearly labelled illustrative and the bar widths match the stated
  scales; sitemap / robots / canonical all agree; all 12 source links
  resolve (HTTP 200), as do the live site, both Teals Unltd. links,
  and the og:image — no dead links.

---

## §5 — Verification steps after editing

1. Serve the page locally and open it in a browser:
   `python3 -m http.server 8000` from the repo folder, then
   http://127.0.0.1:8000 — confirm the page renders, no console errors,
   the torque table toggle (SAE/Metric) still works, and the checklist
   still works.
2. Spot-check every §1 fix by searching the file for the old value —
   none of the old values (135 kW, plugs 14–19, turbo 24–30, downpipe
   26–34, driveshaft 32–43, lugs 88–118, "≤ 0.020 in", "Apex springs
   3", "Oil seal rings 2", "chrome-carbide", "dual-scroll") should
   remain anywhere in `index.html` except in a quoted historical
   context.
3. Re-run the conversion sanity check on the torque table: the only
   rows whose Nm is not ft-lb × 1.3558 should be the FSM-paired rows
   listed in §0 item 1 (flywheel, regulator, water pump, housing
   nuts, intake, exhaust manifold, eccentric shaft lock bolt) — and
   those are correct as printed; do not "fix" them.
4. Check the power curve: for each rpm point, hp ≈ torque × rpm /
   5252 (within rounding).
5. Check the stack diagram now includes the intermediate (center)
   plate between the two rotor housings.
6. `git diff --stat` and `git diff index.html | head` — review the
   diff yourself. Then **stop. Do not commit, do not push.** Report
   back to the owner with a short list of what changed, and the owner
   reviews and commits.

---

## Appendix — corrected torque rows (summary for §1)

Only the rows that change. All other torque rows are FSM-verified as
printed (§4).

| Fastener | Page now (ft-lb / Nm) | Corrected (ft-lb / Nm) |
|---|---|---|
| Spark plugs | 14–19 / 20–27 | **9.4–13 / 13–18** |
| Turbo to manifold nuts | 24–30 / 32–40 | **32–40 / 44–54** |
| Downpipe nuts | 26–34 / 35–46 | **33–39 / 44–54** |
| Driveshaft to differential | 32–43 / 43–58 | **36–47 / 49–64** |
| Wheel lug nuts | 65–87 / 88–118 | **65–87 / 90–120** |
| Exhaust manifold bolts | 24–33 / 31–46 | **23–34 / 31–46** |

Also update the `data-ft` / `data-nm` attributes on each changed row
and the print-card values where affected (spark plugs, flywheel stays
290–360).

*End of report. Working papers from the three reviewers are kept
locally with the reviewer, not in this repo.*
