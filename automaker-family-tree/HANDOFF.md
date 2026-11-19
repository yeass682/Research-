# Automaker Family Tree: handoff notes for the next AI model

Last updated: 2026-11-18. The current build is the 88th version (v81 reference layer; v82 consolidation and Toyota trucks; v83 2020s American trucks; v84 'Spot it' notes; v85 year-by-year lineup; v86 Porsche / BMW families; v87 Camaro, older GM trucks, Nissan; v88 Ford / Mopar muscle and Raptor years).

> **This file replaced the older 80th build in GitHub.** Sizes below in section 1 describe the older build; the current numbers are: 50 groups, 118 brands, **648 engines and platforms**, **2,524 ratings** (2,373 checked), 826 rated vehicles. The file is about 1.95 MB and ~2,700 lines, because large data constants (`G`, `ATLAS_HISTORY`, `ENGINE_SOURCES`, …) each sit on one very long line. Use `grep -n -o` and line/offset reads, not whole-file reads.

## 1. What this project is

The project is a single, self-contained HTML page called **"Automaker Family Tree"**. It does three things:

- Maps the world's automakers: parent groups, brands, ownership stakes, alliances and supplier deals.
- Maps the engines and platforms each group builds or shares.
- Holds a large, hand-verified database of horsepower and torque ratings by vehicle, engine and model-year range.

It has four tabs:
- **Landscape:** an overview of every group.
- **Family tree:** an interactive node graph for one group.
- **Shared:** cross-group engine and platform deals.
- **Power:** ratings by engine, by model year, and a compare view of up to four vehicles.

**Current size:** 50 groups, about 330 engines and platforms, 1,640 ratings, 1,534 marked checked, 490 with "sources differ" notes, and 461 vehicles. The file is about 394 KB and 4,400 lines.

**Where the file lives:**
- **GitHub (canonical):** repository `yeass682/Research-`, `main` branch, folder `automaker-family-tree/`. The folder holds `automaker-family-tree.html` (the copy to edit) and this note, `HANDOFF.md`. Bring updated versions back into `main`.
- **Google Drive:** an older copy of this note is in the "Automaker Family Tree" folder. It is not kept up to date.
- **claude.ai:** a private artifact at https://claude.ai/artifact/FBEbuoQXZwNf68dFjW4EkH. Only the owner can open that link.
- **If copies disagree:** use the one with the most ratings. The `#pwcount` line on the Power tab shows the count.

## 2. How the user works with the AI, and what they want

- The user says "Keep going", "Onwards" or "Keep it up". Each time, the AI adds a **batch** of about 10–25 verified ratings and often new engines, tests, publishes, and reports back.
- **Focus areas the user has asked for** (keep returning to these):
  - Rare and niche variants (example: the 1999 Shelby SP360 Durango).
  - Shelby vehicles of every era.
  - Dodge Durango, every variant and year.
  - Ford trucks (F-150, Super Duty, Ranger), including lesser-known ones.
  - Mustang, including rare models.
  - Classic muscle cars.
  - Older years of the big American brands: Ford, Chevrolet/GM, Ram/Dodge.
  - "More truck years and engines."
  - Lesser-known Ford platforms (for example Panther and Fox).
  - Cadillac, Nissan, Mazda, Kia, Hyundai, Mercedes, NSX.
- **Verify facts.** After an early error, the user asked the AI to verify facts every few prompts. The AI had wrongly said Shelby's shop built the SP360 Durango; in fact Performance West Group built it under a license to Shelby's name.
  - Use web searches and confirm each figure.
  - Never invent a rating.
  - If a figure can't be confirmed, leave the row out or add it unchecked with a note.
- **Report format after each batch:**
  - The version number and the new totals.
  - What was added, grouped by make.
  - What was corrected.
  - What was left out and why.
  - Two to five ideas for the next round.
  - Sources as markdown links.
  - Plain language, no hype.
- **UI requests** (done in the 73rd version; keep this behavior):
  - Text must never be cut off with "…".
  - Each rating must show which trim, package and engine it applies to.
  - Tapping a rating opens a details card: configuration, years, exact engine and code, output, status, checked flag, source note, other configurations in the same years, and a web-search link for that rating.
  - A "Year by year" view lists every configuration for each model year.

## 3. File structure: the JavaScript data in the HTML

All data is inline JavaScript in the HTML.

**`G`** is the list of groups: `{id, n (name), cat, hq, s (sales in millions or null), own (ownership text), br:[brands], st:[stake/deal strings], e:[engines]}`.
- Categories: detroit, japan, korea, germany, europe, china, chinaev, india, evus, exotic, supply.
- Group ids: gm, ford, stellantis, toyota, honda, nissan, mitsubishi, mazda, subaru, suzuki, hyundai, vw, bmw, mercedes, renault, byd, geely, saic, changan, chery, gwm, leap, li, nio, xpeng, xiaomi, seres, tata, mahindra, tesla, rivian, lucid, ferrari, mclaren, aston, bugatti, koenigsegg, pagani, ineos, morgan, shelby, cummins, horse, cosworth, zf, allison, magna, lges, catl, huawei.

**Engine entry** (inside a group's `e` array):
```
[id, name, code, tag, fuel, description, "Brand|Model; Brand|Model", "othergroup:Brand|Model; ..."]
```
- `fuel` is one of: `g` gasoline, `d` diesel, `h` hybrid/range-extender, `e` electric (fuel-cell cars also use `e`), `o` gearbox/assembly.
- Field 7 lists the group's own vehicles.
- Field 8 lists other groups' vehicles ("ext"); these create links in the Shared tab.
- A brand must appear in that group's `br` list, or the page logs a "brand added" console warning.
- Defunct brands (Pontiac, Oldsmobile, Plymouth, Mercury, Saturn, Merkur) appear **only in POWER rows**, never in engine vehicle lists. Leave the vehicle field `""` when an engine only applies to those brands.

**`REL`** lists relationships between groups: `[fromGroup, toGroup, text]`.

**`POWER`** holds the ratings: `POWER["group:engineId"] = {why: "optional explanation", rows: [...]}`. Each row is:
```
["Brand|Model" or "othergroup:Brand|Model", startYear, endYear, hp, lbft, "label (trim/package/version)", "prev" or "", {k: kWh, r: EPA miles}]
```
- `endYear` 0 means still on sale.
- `lbft` 0 means torque unknown; it shows as "—".
- `"prev"` marks an earlier engine the vehicle later replaced. It is drawn with a dashed bar and filed under the newer engine family.
- Many POWER objects are object literals near line 1200. Later batches add rows with `POWER["x:y"].rows.push(...)`. A `.rows.push` throws if that POWER object doesn't exist yet, so search for it first.
- Constants `EVWHY` and `GROSSWHY` hold standard explanations. Pre-1972 ratings are SAE gross: use `why: GROSSWHY + "..."` and put "(SAE gross)" in the labels.

**`FILL`** maps `"group:eng|Model|startYear|hp"` to a label. It supplies clarifying labels for rows that have none where the engine family covers several sizes. It's applied right after `PW` is built.

**`CHECKED`** is a Set built from a template literal, one key per line: `group:engine|vehicleSubstring|startYear|hp`.
- The vehicle part matches with `includes()` against the model name, so `Durango` matches `Dodge|Durango`.
- Only add a key once the figure is confirmed by a published source.

**`DIFFER`** is an object with the same key format, mapping to a short note on where sources disagree. It works for checked and unchecked rows.

**`NOW = 2026`**. New batch code goes right before `const NOW=2026;`.

**Hash routes:**
- `#power-<group>-<engine>`, for example `#power-stellantis-hemi57`.
- `#power-year-YYYY`
- `#trace-<group>`
- `#shared`
- Compare uses `CMP=[...]` with `"group|Brand|Model"` keys.

## 3b. Reference layer (added in v81)

A "FILE MAP" comment at the top of the main `<script>` lists every data constant. The reference layer sits just before the final init line (`renderLandscape();renderSide();buildDeals();renderShared();route();`):

- **`ENGINE_NOTES`**: enthusiast field notes keyed by engine id: `{k: known for, w: watch for, id: how to tell it apart, sw: swap scene}`. Only add widely documented faults (recalls, TSBs, long-running owner reports). About 85 engines have notes so far. Note ids must exist in `EI`.
- **`ENGINE_SPOT`** ("Spot it", v84) sits right after `ENGINE_NOTES`. Keyed by engine id, it holds the trims, packages, badges, VIN characters and telltale hardware that identify an engine on a vehicle. It is merged into `ENGINE_NOTES[id].pk` at load. Vehicle panels gather the `pk` text of every engine into a "Which engine is it?" block (open when there are 4 or fewer engines), with a VIN-position-8 tip for Ford, GM and Stellantis and the glovebox SPID / RPO tip for GM. The engine index text search also covers `pk`, so filtering for "Tremor" or "TRX" finds the right engines. VIN characters are listed only where widely documented (Duramax: LB7 1, LLY 2, LBZ D, LMM 6, LML 8, L5P Y; Power Stroke: 7.3 F, 6.0 P, 6.4 R, 6.7 T; 426 Hemi J / R). About 135 engines have Spot it text: all major US truck engines, Toyota trucks and about 55 performance-car engines.
- **Year-by-year lineup** (v85, `lineupHTML` / `wireLineup`, just before `panelBeforeReference`): the first block in every vehicle panel that has ratings. A year select with ‹ / › steppers lists each engine offered that model year from `PW`, grouped by engine. It shows output range, one line per variant / output (duplicate labels merged), ✓ status, "New for YYYY" / "Last year" flags, and the engine's Spot it text. "Include variants and special editions" adds rows whose model name starts with the vehicle's name (F-150 → Raptor, Lightning, Tremor…; Ram 1500 → TRX, Classic…), with the choice stored in `aft.lineupVariants`. The chosen year is remembered across vehicles (`aft.lineupYear`) and also moves the panel's horsepower year selector. An engine appears for a year only if a rating row covers that year, so gaps in the data show up as missing engines; engines linked without any year data are listed at the bottom.
- **Lineup families and twins (v86)**: `LINEUP_FAMILIES` merges model names that are one truck line in the data (Super Duty / F-250 Super Duty / F-350 Super Duty; Silverado HD / 2500HD / 3500HD; Sierra HD; Ram Heavy Duty / 2500 / 3500; Boxster / Cayman). `LINEUP_TWINS` fills a year with the mechanical twin's lineup (Sierra ↔ Silverado, Canyon ↔ Colorado, Yukon ↔ Tahoe) when the vehicle has no ratings of its own that year, and labels it. `LINEUP_GENERIC` hides legacy catch-all engines (ford:cyclone, ford:mod54, gm:gen3, gm:gen4, stellantis:magnum, nissan:vq, vw:f6) when a specific engine row has the same vehicle and hp.
- **`SPEC`**: derived per engine: displacement (all "x.xL" in the name, so ranges like 6.8–7.3L work), cubic inches (only when the name states it), layout (from `tag`), induction (parsed from name and code words such as TSI, EcoBoost or Duramax; diesels default to turbo), and from `PW` the rated years, hp range, max torque and vehicle count. If an induction label is wrong, fix the engine's name, or adjust `inductionOf()`.
- **Engines tab (`#index`)**: filterable and sortable list of every engine: layout chips, fuel, induction, era, maker, has-ratings, has-notes. Filters persist in `localStorage` (`aft.index`).
- **Panel extras**: "at a glance" spec grid, field notes, Copy link / Search the web / Wikipedia / Show in family tree. Vehicle panels get a collapsible "What to check on these engines".
- **Search**: engine aliases include layout, induction, fuel and displacement, so "diesel V8 6.6" works. Result rows show output range. Focusing an empty box shows recently viewed items (`aft.recent`). Press `/` or Ctrl/Cmd+K to focus search.
- **Deep links**: `#engine/<group:id>` and `#vehicle/<group|Brand|Model>` open that item in the family tree.
- **Landing**: Quick lookup strip (example chips, recents, "Browse all engines"), and stat tiles linking to the Engines and Power tabs.
- **Consolidation (v82)**: 103 duplicate "atlasN" engines were merged into their canonical engines, so the total went from 695 to 601. Old ids still resolve through `ENGINE_ALIASES` (used by `#power-…` links). `ATLAS_HISTORY.linkedEngine`, `ATLAS_MAPPING` and `ENGINE_SOURCES` were remapped. The stale `engines` copy inside each `G` group was removed, because it is rebuilt at runtime from `g.e`. The remaining `atlasN` ids are distinct engines, for example `gm:atlas42` (4.2L Atlas I6), `toyota:atlas4` / `atlas6` (i-FORCE MAX hybrids) and the classic Chrysler LA / B / Slant Six entries.
- **Duplicate ratings**: after `PW` is built, an unsourced row is dropped when a source-linked row has the same engine, vehicle, years and hp. Identical sourced rows are kept once. Dropped rows go to `PW_DUPLICATES_DROPPED`.
- **Verification tiers**: `chk(r)` (row has `source`) shows a solid ✓ with a link. `r.earlier` (matches a `CHECKED` key, no link) shows a lighter ✓. `verified(r)` is either one, and the counts use it. `CHECKED` had been unused in the expanded edition; it is wired back in v82.
- **Batch helpers (v82)**, defined just before `const NOW=2026;`: `fixRows`, `dropRows`, `addRows(eid, why, rows)` and `cite(eid, test, sourceKey, extra)`. Source URLs live in `B82SRC`; `b82x(key)` builds a row's `{source, basis, coverage, note}`. Prefer adding source-linked rows this way over adding bare CHECKED keys. Cross-group rows (for example Cummins in Ram) use vehicle strings like `stellantis:Ram|Heavy Duty`, so test with `endsWith`.

## 4. Proven editing workflow (patch scripts)

1. Back up the HTML first: `cp automaker-family-tree.html vNN-backup.html`.
2. Write a small Python patch script. Each replacement must match exactly once:
```python
import sys
p=sys.argv[1]; s=open(p,encoding='utf8').read()
def rep(a,b):
    global s
    assert s.count(a)==1, ("count", a[:80], s.count(a))
    s=s.replace(a,b)
# 1) extend an engine's vehicle list / description (exact old text -> new text)
# 2) insert a new engine before an existing engine line, e.g. rep(''' ["l84","5.3L V8"''', ''' ["new",...],\n ["l84","5.3L V8"''')
# 3) data: rep('const NOW=2026;', '''/* ---- Batch NN: ... ---- */
# POWER["grp:eng"].rows.push([...]);  POWER["grp:new"]={why:"...",rows:[[...]]};
# const NOW=2026;''')
# 4) CHECKED: append keys after the current last key, anchored on 'LASTKEY`.split'
# 5) DIFFER: insert before '"toyota:uz|Sequoia|2005|282":'
open(p,'w',encoding='utf8').write(s); print("ok")
```
- **The current last CHECKED key is `stellantis:stla|Charger|2024|630`.** Anchor the next batch on ``stellantis:stla|Charger|2024|630`.split``.
- **Before adding an engine, search for its id within that group.** A duplicate engine id (two `["godz", ...]` entries in Ford) once broke the Ford family tree: one node got a NaN position, so the tree drew at zero height until the duplicate was removed. After each patch, check that no group has a repeated engine id and that `buildModel(g.id).height` is a finite number for every group.

3. Test in headless Chromium with Playwright. Load `file://...#power-<grp>-<eng>` at 1280 px and 390 px width. Then check:
   - There are no page errors.
   - The `#pwcount` text shows the new totals.
   - `document.documentElement.scrollWidth` equals the viewport width (no horizontal scroll).
   - Opening a vehicle node and tapping a `details.prd > summary` works.
4. Publish or update the artifact, or save the new HTML wherever the team keeps it.

## 5. Data rules (keep these)

- **US-market ratings** unless the label says otherwise, for example "(Japan market)" or "(US spec)".
- **Metric vs. US horsepower:** 1 PS is about 0.986 hp. Convert, and note the original metric figure in DIFFER.
- **Gross vs. net:** before 1972, ratings are gross. Say so in the label and the `why`.
- **Torque:** if it isn't confirmed, put 0 rather than guessing. Or include the commonly quoted value without a CHECKED key and explain in DIFFER.
- **Splitting rows:** use separate rows when the rating changed mid-generation or differs by transmission. Put the distinction in the label, for example "manual" or "(1998½ on)".
- **Special editions:** keep the build count in the label when it matters, for example "(209 built)".
- **Licensed vs. built:** be careful with "licensed vs. built by" claims, as with Shelby and Performance West.
- **Disputed "firsts":** word claims like "first", "smallest" or "most powerful" carefully ("often cited as").
- **Notes in the data:** use DIFFER notes rather than inline hedging in descriptions.

## 6. Recent batch log (latest at the bottom)

- **61:** diesel cars: VW TDI (EA189), BMW 335d, Mercedes BlueTEC, Jeep/Ram EcoDiesel, Cruze diesel.
- **62:** rarer Mustangs: 1964½ 260, K-code 289, Boss 302/351/429, 428/429 Cobra Jet, King Cobra, Cobra R 1995/2000, Bullitts, SN95 V6. New engines: Ford 385-series and 351 Cleveland.
- **63:** 1967–70 Shelby GT350/GT500. Diesel pickup history: 7.3/6.0/6.4 Power Stroke, LB7–LMM Duramax, 5.9 Cummins.
- **64:** classic Mopar: 426 Hemi and 383/440. Chevelle LS6/LS5, COPO ZL1. **Corrected** the Viper 1996–2002 row: the 1996 RT/10 was 415 hp; the GTS and all 1997+ cars were 450.
- **65:**
  - Cadillac: CT6 Blackwing 4.2TT, 1970 Eldorado 500 V8, V8-6-4, Allanté, ELR, CT4/CT5-V, XTS V-Sport.
  - Durango: Pursuit, 2026 R/T 392, Hellcat Hammerhead.
  - Chrysler Aspen.
- **66:** Nissan: Datsun 240Z/260Z/280ZX Turbo (L-series), Skyline GT-R R32–R34 (RB26), Sentra SE-R (SR20), 300ZX Z31, Q45, Maxima VQ30, Armada, GT-R50, Ariya.
- **67:** Durango:
  - Filled 1998–99 torque.
  - 2025 special editions.
  - V6 dropped for 2026.

  F-150 sweep: 4.2 V6, 4.6/5.4 tunes, 3.0 Power Stroke diesel, Lobo, Shelby Super Snake Sport.
- **68:**
  - Mazda: CX-90 inline-6 and PHEV, Millenia S Miller-cycle, first-gen RX-7.
  - Kia: Tau V8 Borrego/K900/Equus, Optima, Seltos, EV9 GT.
  - Shelby: Dodge Shelby Charger, 2013–14 GT500 Super Snake.
- **69:** Pontiac V8 (GTO, Trans Am including SD-455 and Turbo), Buick 455 GSX Stage 1, Olds 455 W-30, Hyundai Genesis sedan Tau V8.
- **70:**
  - Mercedes: 300 SL, 190E 2.3-16, 500E, SLR, SLS, AMG ONE, E 450, S 500, C 300.
  - NSX automatic.
  - Fords: Contour SVT, SVT Focus, Crown Victoria P71, Mark VIII.
  - GM: 1989 Turbo Trans Am, 1991–92 Z28.
  - Hyundai: Veloster Turbo, Kona Electric, Nexo.
  - Mazda: MX-3 V6, MX-6/Probe GT V6.
- **71:** Toyota 22R-TE Pickup, 80-series Land Cruiser (1FZ), 1996–2002 4Runner, Fiesta ST, Focus ST, Mercedes 560SEC, SL500 R129.
- **72:** BMW S14 E30 M3, S38 (E24 M6, E34 M5), E36 M3, 1M, M2 CS. Subaru WRX 2002–05, STI S209. VW Mk3 GTI VR6, Mk5 GTI.
- **73:** **UI redesign:**
  - Expandable rating rows.
  - "Year by year" view.
  - No truncation.
  - FILL labels.

  Data: Ram 1500 3.7 V6, Silverado 4.3 V6 (Vortec/EcoTec3).
- **74:** trucks: Frontier 2.4/3.3/supercharged, Dakota R/T and 2008 4.7, Ram Magnum V10, Super Duty 7.3 Godzilla, Chevy SSR, Avalanche 8.1, Ridgeline, Gladiator EcoDiesel.
- **75:** Dodge/Ram rarities (1978 Li'l Red Express, Rumble Bee, 2014+ Power Wagon). Mercedes 450 SEL 6.9 (new M100 engine), AMG Black Series (C 63, SL 65, SLS, AMG GT), G 63 AMG 6x6.
- **76:** classic Corvettes (Blue Flame six; new Gen I small-block engine; L88/L71 427s), Pontiac (Catalina 421 SD, 1967 Firebird 400, 1969 Grand Prix SJ), vans (Transit, Express), police (Caprice 9C1 and PPV, Charger Pursuit, Police Interceptor Utility).
- **77:** MINI (new Tritec supercharged and Prince turbo engines, JCW F56 and GP), Mercedes S 550 / S 580 / G 550 years (new M273 and M278 engines), Tahoe PPV, Police Interceptor Sedan, Charger 2006–10 V6s (new Chrysler LH V6 engine), Charger Daytona EV.
- **Fix (80th version):** removed the duplicate Ford "godz" engine that stopped the Ford tree loading.
- **81:** Reference layer: Engines index, at-a-glance specs, field notes, search upgrades, deep links.
- **82:**
  - Consolidation: 103 duplicate engines merged, 10 duplicate ratings dropped, and the earlier verification pass wired back in.
  - Misfiled rows moved: 2015–20 Escalade to L86; Tahoe L83 / L84 split; LZ0 rows; Civic IMA; 2009–10 F-150 5.4 three-valve; Wrangler JK 3.8 to a new EGH engine; F80 M3 / M3 CS to a new S55 engine; 2013–20 BRZ / FR-S / 86 to a new FA20 engine.
  - Corrections: 2018 Ram HD HO is 385 hp / 930 lb-ft, not 400. The F-150 5.8L is split into 1990–91 (210 / 315), 1992–93 (200 / 315) and 1994–96 (210 / 325). The 2027 L4B is confirmed at 350 / 455.
  - New Toyota engines: 22R-E, 22R-TE, 3VZ-E, 2F, 3F-E, 1FZ-FE.
  - New Toyota truck ratings: Pickup, T100, 1995–2004 Tacoma, 2000–04 Tundra V6, 2001–09 Sequoia, 2004–09 4Runner, FJ Cruiser 2007–13, Land Cruiser FJ60 / FJ62 / FJ80, Lexus GX 470 and LX 450 / 470. TRD supercharger kits are listed with their kit ratings (Tundra / Sequoia 504 hp, Tacoma 304 hp).
  - Source links added for Cobra / Terminator / GT500, Viper generations, AMG 55s and Miata NB / Mazdaspeed.
- **83:**
  - GM midsize: new 2.5L LCV engine. 2015–22 Colorado / Canyon with the LCV, LFX / LGZ and LWN diesel. The GMC Canyon had no ratings before. 2027 Colorado / Canyon L4B at 350 hp / 455 lb-ft. 2024 Sierra EV Denali Edition 1.
  - Ford: Maverick hybrid torque fixed (155 lb-ft). New Maverick Lobo. 2021–23 F-150 3.3 V6 cited. New global Ranger PHEV engine. The F-150 Lightning BEV ended in December 2025 with an EREV successor (added to the field notes).
  - Stellantis: Gladiator EcoDiesel 2021–23. New range-extended Ram 1500 REV (Ramcharger) engine, listed with its announced 663 hp / 615 lb-ft.
  - New group: Slate Auto (US EV truck start-up, 181 hp, 205 miles). Landscape bubbles now read "pre-production" instead of "supplier" when a non-supplier has no sales.
  - Source links added for the S2000 2006, Civic Si 2012–22, BRZ, F80 M3 / CS, 6.1L SRT8, i-FORCE MAX hybrids and the 2015–20 Escalade.
  - Flagged: the 2002 Escalade LQ9 (one listing says 5.3L only for 2002) and the S2000 2004–05 240 hp (Honda's later sheets say 237).
- **84:** "Spot it" identification notes for about 135 engines, plus the vehicle-panel "Which engine is it?" block. Corrected the 22R-TE description: it no longer claims a factory intercooler, which wasn't confirmed.
- **85:** Vehicle year-by-year lineup (year picker in vehicle panels).
- **86:**
  - Porsche: the umbrella `vw:f6` was split into M96, Mezger, M97, 9A1, 9A2, GT3 4.0, 992 Turbo, T-Hybrid, 718 turbo four, 718 4.0, Carrera GT V10 and 918 V8. The 911 rows moved onto them, and new rows were added for the 997 Turbo, GT2 RS, 992 Turbo / S, 992.2 Turbo S, 718s, Carrera GT and 918.
  - BMW: new S14, S52 (E36 US), S54, S65, S62, S85, N55, N52, N20, N63, M54, M62 and N62. The S54 / S65 M3 rows moved off the S58, the X5 M rows moved to the S63, and the old X5 V8s moved off the S68.
  - Year gaps: F-150 1997–98 (4.2 / 4.6 / 5.4), 2006–08 5.4 three-valve, 2019 / 2021 diesel and the 3.3; Colorado / Canyon 2007–12 (new LLV, LLR, LH8); Super Duty gas 2015–24 and 2026; Silverado / Sierra HD gas L96 / L8T.
  - Fixes: the Power → By year list on phones (fixed-width columns squeezed the rating), a mobile tab bar that hid the Power tab, the engine picker showing in non-engine Power modes, and Datsun added to Nissan's brands (removes the console warning).
- **87:**
  - Camaro 1967–2002 with new engines DZ 302, L72 / ZL1 427, LU5 Cross-Fire, TPI (LB9 / L98) and LTG 2.0T. The Camaro LT1 trim was added. The Camaro family now merges the fifth-gen SS / ZL1 / Z28 model names.
  - Older GM trucks: 1992 C/K 1500 (KBB 190 hp for the 5.7 TBI, conflicting with the 200 hp span; flagged), and the 1994–2004 S-10 (new 2.2L four, 4.3 LB4 / L35). GMT400 / GMT800 rows moved off the `gm:gen3` catch-all onto L05, L31, LM7 and LR4.
  - Nissan: new VQ30DE, KA24E / DE, SR20DE, VG30DE and L-series engines. Added the Maxima 1995–2023, Altima 2002-on, 240SX, 300ZX non-turbo, Datsun 240Z, Sentra SE-R (B13), Xterra 2000–15, Frontier VQ40 2005–19 and Armada 2004–24. Context-specific engine names were neutralized (VG33E, QR25DE, LH8, 396).
  - Infrastructure: `moveLegacy` now records `movedFrom`, and the earlier-pass ✓ check (placed right after `ENGINE_ALIASES`, which is a `const` defined late in the file) matches moved and merged ids. This restored ✓ on rows moved since v82. Don't reference `ENGINE_ALIASES` above its definition: it throws (TDZ) and stops the whole script.
- **88:**
  - Ford muscle: new Windsor 260 / 289 / 302 (5.0), Boss 302, FE (390 / 427 / 428), Boss 429, 429 CJ and Boss 351 engines. Mustang K-code, Fox 5.0 (1982–95), SN95 4.6, Bullitt, Shelby GT350 / GT500 / KR, Galaxie 427, Fairlane Thunderbolt and Torino Talladega / Cobra.
  - Mopar: Plymouth added to Stellantis' brands. Road Runner, GTX, 'Cuda / AAR, Challenger R/T / T/A, Super Bee, Charger Daytona, Superbird, Coronet R/T, Duster 340 and Demon 340, using the existing 340 / 383 / 440 / Hemi engines.
  - Raptor: SVT Raptor 2012–14, Gen 2 2017–20, Gen 3 2021–26, Bronco Raptor 2022–26 and the global Ranger Raptor 2019–22. Lineup families were added for Raptor, Mustang and Torino.
- **Still unverified (150 rows)**: view them with `PW.filter(r=>!verified(r))`. Left unverified on purpose: Tacoma 2RZ / 3RZ spans, 1996–2002 4Runner 5VZ 183 hp, 2005 Sequoia 282 hp, LX 470 2003–05, 2007–09 Tundra 4.0, FJ60 2F, the 22R-E (no rating listed because sources range from 105 to 116 hp), and the 1992–93 F-150 5.8.

## 7. Checked and left out (don't re-add without new evidence)

- E28 M5 at 256 hp: that figure is the US E24 M6.
- 2016–18 M2 and M2 Competition: not confirmed.
- 2006–14 WRX, Subaru SVX, Corrado VR6, Mk1/Mk2 GTI: sources conflicted.
- Toyota 22R-E non-turbo (sources say 105–113 hp, not 116), 3VZ-E V6 pickup, Mercedes 300D Turbo.
- Cobalt SS, Tiburon GT, Mazda CX-7, Probe GT turbo, Thunderbird Turbo Coupe 1987.
- 2005–07 Dakota 4.7 High Output, 2006–08 Ridgeline, 2011 Silverado HD 6.0L, 2011–19 Explorer EcoBoost figures.
- Shelby 1000, Shelby Baja Raptor, 2011 Shelby GT350, Shelby Raptor, MX-30 EV, Genesis G90/G80 5.0 V8.
- 280Z: sources say 149 or 170 hp.
- 2005–13 Power Wagon 5.7, 300 SEL 6.3, CLK 63 Black Series, G 500 4x4², Maybach 57 S, 1957 fuel-injected 283, 1967 L71, 1980 Trans Am Turbo 4.9, Chevy Astro, F-150 Police Responder, modern Mustang SSP, 2010 E 350.

## 8. Ideas queued for next rounds

- Older Silverado/Sierra HD gas engines (6.0L Vortec).
- 1980s–90s Ford F-250/F-350: 7.5L 460, 6.9/7.3 IDI diesels.
- Toyota T100, Chevy S-10 and Colorado history, GMC Sierra Denali generations.
- Panther-platform luxury cars: Lincoln Town Car, Mercury Grand Marquis.
- 1980s F-150, Ranger and Explorer gaps.
- Audi RS/quattro history, Porsche 911 generations, Honda Civic Type R and Integra Type R, Mitsubishi Evo and 3000GT VR-4.
- About 130 rows still have no trim label but belong to single-size engines. Adding real trim or package names, for example which Ram 1500 trims offered the 5.7 Hemi each year, would improve the details card.
- Periodically re-verify a sample of older rows, especially descriptions written early in the project.
