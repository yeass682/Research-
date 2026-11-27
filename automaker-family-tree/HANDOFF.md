# Automaker Family Tree: handoff notes for the next AI model

Last updated: 2026-11-27. The current build is the 124th version (v81 reference layer; v82 consolidation and Toyota trucks; v83 2020s American trucks; v84 'Spot it' notes; v85 year-by-year lineup; v86 Porsche / BMW families; v87 Camaro, older GM trucks, Nissan; v88 Ford / Mopar muscle and Raptor years; v89 bug fixes and quality-of-life; v90 Honda / Acura rare variants; v91 Charger / Challenger deep dive; v92 trims consolidated into vehicles; v93 trim consolidation for every make; v94 Ford descriptions, About cards and engine clean-up; v95 rare and high-output trucks; v96 Silverado years, Lexus / Hyundai / Mercedes; v97 Hyundai / Lexus / Audi gap audit; v98 design pass; v99 Nissan / NISMO / Tundra; v100 Chevrolet SUVs and cars, Ford Transit / Expedition / Super Duty / Taurus; v101 Chevrolet / Toyota / Nissan gap fill; v102 Ford trims and packages by year; v103 Nissan years and rare variants, C/K and Silverado 1500 / 2500; v104 Infiniti, Titan, Sierra and Silverado trims; v105 Tahoe / Yukon, GMT800 and HD trims, Armada / QX80; v106 Corvette 1953–2027, AMG GT / SLS, Colorado, Expedition / Navigator; v107 Silverado 1500 audit; v108 merged "Engines & ratings" section; v109 Porsche, Acura and BMW history; v110 Camaro 1967–2002 and rare Camaros, Silverado trims by year and rare Silverados; v111 Tacoma configurations by year; v112 Mustang 1999–2014 variants and engines; v113 Ford Focus 2000–18 and Mustang 2015–26; v114 Chevelle 1964–77 and trim explanations; v115 Mercedes-Benz model history and GMC Sierra trims; v116 clearer Mercedes AMG labels and E-Class explanations; v117 C / S-Class explanations, SL R232, GLC and GLE 2024-on; v118 Durango trims by year and Dodge trim explanations; v119 Cadillac Escalade configurations by year, Dodge Ram 1500 / Dakota trims and classic Dodge muscle-car trims; v120 Escalade gaps, Ram 1500 1995–2008 and Dakota trims from Cars.com; v121 Tahoe / Suburban / Yukon by year and Dodge source reconciliation; v122 Jeep Wrangler and Grand Cherokee by year, Ram 1500 2011–26; v123 Gladiator, Jeep gap years, F-150 explanations, Sierra 1999–2009; v124 "Terms & badges explained" glossaries, Tundra and 4Runner by year).

> **This file replaced the older 80th build in GitHub.** Sizes below in section 1 describe the older build; the current numbers (v124) are: 50 groups, **786 engines and platforms**, **4,516 ratings** (4,252 checked, 401 with "sources differ" notes; 515 vehicles in the page header), 521 rated vehicles (the Silverado Callaway SC602 and Yenko/SC entries are now trims of the Silverado 1500). The file is about 1.95 MB and ~2,700 lines, because large data constants (`G`, `ATLAS_HISTORY`, `ENGINE_SOURCES`, …) each sit on one very long line. Use `grep -n -o` and line/offset reads, not whole-file reads.

> **Standing practice (from v124):** every vehicle worked on gets a `GLOSSARY` entry explaining its brand-specific terms (cabs, beds, engine badges, trim names, packages, features, naming history), in addition to TRIM_INFO.

> **Trim explanations (from v114 on):** the owner wants every trim / package / configuration explained ("what it included"), and past entries back-filled over time. Data lives in `TRIM_INFO` (defined in the Batch 114 block): `TRIM_INFO["group|Brand|Model"] = [{t:'Trim name', y:[from,to], d:'what it included', s:'B82SRC key'}]`. Add entries with `TRIM_INFO["…"]=[…]` or by pushing to an existing list in later batches. The `v114-trim-info` style/script layer (before `</body>`) underlines matching trim names in the year view and shows a per-year "What these trims and packages include" panel. Matching: the trim text in the year view equals the entry name or starts with it followed by a space, "(", "," or ";" (longest name wins); the selected year must fall inside `y` if given. Use a fresh `B82SRC` key for each source URL — keys such as `cv22` were reused between batches (v110 Silverado vs v111 Tacoma), and `TRIM_INFO` resolves keys at render time, so a reused key points at the later batch's URL. Covered so far: Chevelle, Camaro, Silverado 1500, Tacoma, Mustang, Focus.

> **How the owner wants vehicles presented (from v110 on):** follow the Porsche 911 model. In the year view, every year should list each trim / package / configuration offered that year with its engine, as `Trim / Trim / Trim · short note · engine`. When adding a vehicle or year, add one row per engine per year with the full trim list, rather than a single engine-only row spanning many years. Rare and limited editions go into their base vehicle (as trims) and, if their ratings are not documented, into that vehicle's "Rare and special …" guide (`NAME_GUIDES`).

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
- **claude.ai:** the current build (v101) is published at https://claude.ai/artifact/7X4st1BkEi87ea775MRc5R. Republish that one for later versions. Who can open it depends on the owner's Share setting. The older artifact https://claude.ai/artifact/FBEbuoQXZwNf68dFjW4EkH still holds an earlier, smaller build (about 420 KB). That build has the 1953–69 Corvette (Blue Flame six, Gen I small-block) and Chevelle big-block entries that this lineage lost; use it as a reference when re-adding them.
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
- **89 (fixes / QoL):**
  - Metric-converted ratings are rounded for display (`hpExact` / `tqExact` keep the decimals).
  - 22 vehicles that existed only in rating rows are auto-linked (`VEH_AUTO`), so they're searchable and get panels and lineups.
  - "Sources differ" notes and earlier-pass ✓ now follow moved or merged rows (`rowIds()`), and match on rounded hp.
  - Family tree "Fit to width", with a minimum of 45%, replaces fit-to-height, which shrank big groups to 20%.
  - Power tab engine list has a sticky filter box.
  - ← / → keys step the vehicle lineup year.
  - Power timeline rows grow when a vehicle name wraps (`fitTimelineNames`) instead of clipping the second line.
- **Audit script idea:** check duplicate engine ids, y2 < y1, hp ≤ 0, PW keys missing from VEH, and decimal hp. The v89 audit found no structural errors.
- **90:**
  - New Honda / Acura engines: B18 (Integra LS / GS-R / Type R), K20A2 / K20Z1 (RSX Type-S), K24A2 (TSX), J32 (TL / CL), J37 (TL SH-AWD), ECA1 + IMA (first Insight), E07A (Beat) and S07A turbo (S660).
  - New ratings: 1994–2001 Integra, 2023-on Integra 1.5T, RSX 2002–06 (incl. the 2006 SAE re-rating), TSX, TL 2002–14, 1993–96 Prelude VTEC, Del Sol VTEC, CR-X Si 1985–91, Element 2007–11, Ridgeline 2007–26, Odyssey 2011-on, first-gen Insight, Beat and S660.
- **91:**
  - Charger and Challenger across every generation. Classic Charger 1966–71 (318 / 361 / 383 / 440 / Six Pack / Hemi, Charger 500) and Challenger 1971–74 (gross to net, 340 to 360).
  - Modern packages: Daytona R/T, Daytona / Daytona 392, SRT 392, Super Bee 2008–09 and 2012–14, Scat Pack Widebody, T/A, T/A 392, Shaker, Scat Pack 1320, Super Stock, Jailbreak (both cars), Demon on 91 octane (808 hp), Black Ghost, the Charger Daytona EV R/T / Scat Pack and the 1983–87 Shelby Charger / GLHS. New engines: 3.5L EGJ V6, 361 B, 360 LA and the 2.2L K four.
  - Correction: the Charger SRT Hellcat is split into 707 hp (2015–20) and 717 hp (2021–23). The old 2015–23 row claimed 707 hp throughout.
  - New `NAME_GUIDES`: per-vehicle disambiguation notes shown as a collapsible "names, decoded" block. Charger / Challenger is the first entry; add more for other confusing name families (e.g. Mustang GT / Cobra / Shelby, Silverado SS / Intimidator).
- **92 (trims vs vehicles):**
  - `TRIM_CONSOLIDATE` (runs right after `PW` is built) folds package / trim "vehicles" into the real vehicle and prefixes the trim to the row label ("Scat Pack · 3.0L SIXPACK H.O. …"). The original names are kept in `mOrig` / `keyOrig`, and `TRIM_MERGED` maps old keys so deep links and verification still work.
  - The Charger and Challenger went from 24 vehicle entries to 4 (Charger, Challenger, the 1969 Charger Daytona and Charger 500). The Shelby Charger stays separate. Extend the map to other makes as needed (e.g. Mustang Shelby / Mach 1, F-150 Tremor / Limited).
  - Vehicle search also matches trims, so "Scat Pack" finds the Charger and Challenger.
  - The year lineup now shows one line per output level, listing every trim / package that carried it ("Scat Pack / Scat Pack Widebody / Super Bee …"), with engine detail underneath.
  - 2026 gas Charger: SIXPACK is the engine (Hurricane I6). R/T = S.O. 420 hp, Scat Pack = H.O. 550 hp, and the model year was corrected from 2025 to 2026. The engine was renamed "3.0L Hurricane / SIXPACK twin-turbo I6". Charger Daytona EV rows are now "Daytona R/T (EV)" and "Daytona Scat Pack (EV)" on the Charger.
- **93 (consolidation for every make):**
  - `TRIM_CONSOLIDATE` now covers about 430 trim / package / edition / generation keys across every make (generated from the vehicle-key list: a key whose model name is another key's name plus a suffix folds into the base). Rated vehicles went from 825 to 484 and vehicle entries from 1,319 to 887.
  - Folded into the base: Corvette Z06 / ZR1 / Grand Sport, F-150 Raptor / Raptor R / SVT Raptor / SVT Lightning / Tremor, Ram 1500 TRX / Rebel / RHO, Mustang Shelby / Boss / Mach 1 / SVT Cobra, Camaro SS / ZL1 / Z/28, Civic Si / Type R, WRX STI, Golf GTI / R, GT-R editions, Lexus IS 350 / IS 500 and similar, Range Rover editions, Tesla Plaid / AWD, and many more.
  - Kept separate (distinct model lines): BMW M models (X3 M, X5 M, Z4 M, 1 Series M), Bronco Sport, Explorer Sport Trac, F-150 Lightning (EV), Mustang Mach-E, Transit Custom, Titan XD, Ram 1500 Classic / REV / 3500 Chassis Cab, Ioniq 5 / 6 / 9, Range Rover Sport, Discovery Sport, Porsche 718 / Macan Electric, Corolla Cross, Charger 500, the 1969 Charger Daytona, the Shelby Charger.
  - Defender: new key `tata|Defender|Defender (classic)` (1948–2016 cars plus the Works V8 / Classic Defender conversions); everything else is the 2020-on Defender. `LINEUP_APART` keeps the classic out of the new Defender's year view.
  - Spelling duplicates merged: Leaf → LEAF, IONIQ 5/6/9 → Ioniq 5/6/9, RS3 → RS 3, RS6 → RS 6. A check for vehicles with the same name in two groups found none.
  - Trims that had no rating rows (vehicle entries only) also fold in. Guide links (`spotterHTML`) follow `TRIM_MERGED`. Redundant `LINEUP_FAMILIES` (Camaro, Raptor, Mustang) removed.
  - Seven ratings became exact duplicates after the merge and are dropped by `PW_DUPLICATES_DROPPED` (each had a sourced twin).
  - Lineup: generation tags such as "(C7)" or "(fifth generation)" are hidden in the year view (the year already says it); near-duplicate details are collapsed; the related-models checkbox names the models it adds; "Last year" is now "Last rated year" with a tooltip, because older years are not fully covered.
  - Data: the 2020–26 LT2 Corvette rows were filed under the 2027 LS6 and moved to the LT2. Added the E-Ray (655 hp combined, 2024–26; GM gives no combined torque), the 1968 Mustang (289 2V, 302 2V, 302 4V, 390 GT) and the 1969 Mustang (302 2V, 351 Windsor 2V / 4V, 390 GT). Corvette C7 labels unified.
- **94 (Ford review):**
  - New `VEH_ABOUT` (defined just before `NAME_GUIDES`): an About card at the top of each vehicle panel with a summary, a collapsible generation list (`g: [[years, generation, what changed]]`), an optional `note` and an `id` (identify-it tip). All 50 Ford / Lincoln / Mercury vehicles have one; Super Duty and chassis-cab keys share text. **Next: GM, then Stellantis, then the other makes.**
  - Ford engine descriptions rewritten as real context. Earlier text was internal data notes ("no interchangeability is inferred", "retained as a year-specific snapshot"); other makes still have some of these and should get the same treatment.
  - The catch-all engines `ford:mod54` (Modular truck V8s) and `ford:cyclone` (non-turbo V6s) are gone: every row moved to the exact engine (Windsor 5.0, Triton 4.6 / 5.4 two- and three-valve, 4.6 three-valve truck, Cyclone 3.3 / 3.5 / 3.7, Cologne 4.0). `ENGINE_ALIASES` sends old links to `ford:triton543v` / `ford:cyclone33`; `resolveDeepLink` now honors aliases.
  - New engines: `ford:i6300` (4.9L / 300 six), `ford:hy20` (2.0L hybrid: Fusion and C-Max had been filed under the 2.5L), `ford:puma32` (3.2L Duratorq five-cylinder: the global Ranger 3.2 had been filed under EcoBlue). The 2013–14 GT500 row moved from the 5.2L Predator to the 5.8L Trinity.
  - Consolidated: Fiesta Sedan / SE EcoBoost Sedan / ST → Fiesta; Focus ST / RS / SVT → Focus; Contour SVT → Contour; Crown Victoria Police Interceptor → Crown Victoria.
  - Data: Edge (2015–24, new vehicle), 2011–19 Explorer (3.5 V6, 2.0 / 2.3 EcoBoost, 3.5 EcoBoost Sport / Platinum), 2025-on Expedition trims, Escape closed at 2026 (production ended December 17, 2025), Maverick hybrid wording fixed (optional for 2024 only).
  - New notes: known issues for the Cyclone (internal water pump), Cologne 4.0 (timing chains), SHO V8 (cam sprockets), 2.7L (composite oil pan), Focus RS 2.3L (head gasket), 3-valve 4.6L (two-piece plugs); "Spot it" text for most Ford engines that had none.
- **95 (rare / high-output trucks and the packages that carried each engine):**
  - Added: Silverado SS 2004–07, SSR 2004 and 2005 (LS2 rows moved off the Gen IV catch-all onto `gm:ls2`), Typhoon 1992–93, Silverado HD ZR2 / ZR2 Bison (2024-on, gas and Duramax), 1994 SVT Lightning (fills the 1993–95 gap), Harley-Davidson F-150 2000–01 (5.4L) and 2002 (supercharged), F-150 Tremor 2021–23, Super Duty Tremor 2020–22, Power Wagon 2005–08 / 2010–13 / 2014–24, Li'l Red Express (new vehicle), Shelby Dakota 1989, Tacoma X-Runner 2005 and 2006–13, Titan XD Cummins 2017–19.
  - Corrected: the Colorado's base 237-hp 2.7L ends after 2024 (every 2025-on Colorado has the TurboMax); 2023 Colorado trim / engine pairings named. Sources cited for the previously unverified Silverado / Sierra HD L96, L8T and LML spans.
  - Folded in: Silverado SS and Intimidator SS → Silverado 1500; 454 SS → C/K 1500; Ram SRT-10 (and SRT-10 Quad Cab) → Dodge Ram 1500. `TRIM_CONSOLIDATE` now resolves chains (a trim of a trim) to the final vehicle.
  - Lineup: rows from a same-truck family member (C/K 1500 under Silverado, "Heavy Duty" under Ram 2500) no longer show that name as a trim.
  - About cards for Syclone, Typhoon, Li'l Red Express and SSR.
  - Left out on purpose: GMC Sierra C3 / early Sierra Denali (sources give 300, 325 and 345 hp for the 6.0L); 2009 Power Wagon (unclear whether it had the 345-hp or 383-hp 5.7L); Sierra HD AT4X (no source found); 2024-on F-150 Tremor engine choices.
- **96 (Silverado years; Lexus, Hyundai, Mercedes):**
  - Silverado / C/K half-ton now has a lineup for every model year 1988–2026. GMT900 (2007–13) rows moved off the Gen IV catch-all onto LY2 / Gen IV 5.3 / L92 and are cited to the generation engine table; added the 4.3 V6, VortecMAX 6.0 L76 and the two-mode Hybrid (new engine `gm:lfa60hybrid`). The 2014–18 6.2L rows were filed under the L87 and now sit on the L86. GMT400 and GMT800 gap years were added **unchecked** (from a Wikipedia table summary; they match neighbouring source-linked years). Silverado 2500HD gained Duramax LB7 / LLY (new engine `gm:lly`) / LBZ / LMM spans (cited) and unchecked 6.0L / 8.1L gas spans. About cards for Silverado 1500 and Silverado HD.
  - Lexus: IS 300, IS 250 (new engine `toyota:4grfse`), 2006–13 IS 350, GS 300 / 400 / 430 / 350 / 460, SC 300 / 400 / 430 (2002–05), ES 350 2007–18.
  - Hyundai: Genesis sedan 2009–16 (3.8 MPI / GDI, 4.6 and 5.0 Tau), Equus 2011–16, Sonata 2.0T 2011–14, Veloster Turbo 2013–17. Veloster N folded into Veloster; Genesis Coupe kept apart from the Genesis sedan in the year view (`LINEUP_APART`).
  - Mercedes: badge entries folded into their classes (C 300 / C 250 / C 350 / C 220 d / C 450 AMG → C-Class; E 350 / E 450 / E 550 / E 220 d → E-Class; S 500 / S 350 d / S 400 d / S 450 d → S-Class; G 400 d → G-Class; Mercedes-AMG "AMG GT" → GT). Added C 300 2015–21 (new engine `mercedes:m264` for 2019–21), S 550 / S 560 / S 580 / S 500, C 63 / C 63 S 2015–21, E 63 S 2018–23, AMG GT / GT S (2016) / GT C / GT R, CLA 250 (new `mercedes:m270`), CLA 45 (new `mercedes:m133`, then M139).
  - Not added (unconfirmed): E 450 hp, G 550, 2013–18 G 63, RX 350 by year, GS 300 1993–97, SC 430 2006–09, 2017-on AMG GT / GT S figures, GMC Sierra C3.
- **97 (gap audit: Hyundai, Lexus, Audi):**
  - Hyundai: Ioniq Plug-in and Ioniq Electric (new engine `hyundai:ioniqev`, 118 → 134 hp), Ioniq 5 2022–24 Standard Range and AWD, Sonata 2015–19 (2.4 / 2.0T / 1.6T Eco), Santa Fe 2013–20 (Sport 2.4 / 2.0T and 3.3 V6), Tucson 2016–21, Elantra base and N Line, Kona 2019–21. New engines `hyundai:theta24`, `hyundai:nu20`. The Palisade 3.8L was double-filed under the 2.5L turbo hybrid (dropped).
  - Lexus: LX 570 2008–21, GX 460 2011–23, LS 460 2013–17, LS 600h L, GS 350 / 200t / 300 / 450h 2013–20, RX 300 / 330 / 350 1999–2022, NX 200t / 300, RZ 300e / 350e / 450e / 550e.
  - Audi: S4 rows moved to the right engines (new `vw:a27tt`, `vw:v842` 4.2 V8, `vw:ea837` 3.0 supercharged V6; the RS 4 / 2013–15 RS 5 4.2 V8 moved off the 4.0 TFSI). S3 rows moved from the A3 key and the RS 5 row from the S5 key (a PW-level relabel before `TRIM_MERGED`, keeping `mOrig`). Added A4 2021–25, Q7 2017–25, SQ7, SQ5 2018–24, S5 2008–17 and 2025, R8 (new `vw:v10fsi`), TT RS, RS 3 2017–20, RS e-tron GT. Audi S / RS models stay separate vehicles (like BMW M).
  - New `LINEUP_NOT_SOLD`: years a model was not sold in the US are named in the year view (Audi S4, TT RS, RS 5, S3, RS 3, R8; Veloster 2018; GS 2012; SC 2001).
  - Not added (unconfirmed): e-tron GT base output, Q5 2.0T (252 vs 261 hp), SQ5 2014–17, A4 B8 / 2017–20, Ioniq 5 N torque, SC 430 2006–09.
- **98 (design pass, no data changes):**
  - `<style id="v98-design">` sits right after the last existing style block and **before the first script** (it must load before the scripts run, and after the older style layers so it wins). Google Fonts link in `<head>` loads Archivo / Instrument Sans / JetBrains Mono (system fallbacks if offline).
  - Darker tokens (`--bg #05070a`, `--surface #0a0e13`, `--surface-2/-3`). Fuel colours changed to the validated dark steps (gas `#3987e5`, diesel `#d95926`, hybrid `#199e70`, electric `#9085e9`): the old pastel set failed the lightness, chroma and normal-vision checks of the dataviz validator.
  - Wide screens (≥1180px): the detail panel docks to the right at full height (`--pw`, `--hdr` set from the header height), and main content pads to make room. Sticky panel header (`.p-head`) holds eyebrow, title, close and engine tags.
  - New coverage grid (`covHTML`): vehicle panels show engines × model years above the year view, engine panels show vehicles × years under the spec grid. Cells are coloured by fuel, hover for year / output, click to jump the year view; not-sold years are hatched; the label column is sticky; on phones the grid scrolls to the selected year.
  - Year view trim rows get a horsepower bar scaled to the strongest rating that year (`enhanceYear`, DOM post-processing driven by a MutationObserver on `.ly-body`).
  - Engine index is a dense one-line table on wide screens; Power bars have more fill.
  - The design script re-opens a panel opened by a deep link (it opens before the layer loads) so it gets the same treatment.
- **99 (Nissan, NISMO, Tundra):**
  - NISMO: Juke NISMO 2013–14 (197 hp) and 2015–17 (188 hp), Juke NISMO RS 2014–17 (215 hp manual FWD / 211 hp CVT AWD), 370Z NISMO 2009–14, Sentra NISMO through 2019 and SR Turbo, Sentra SE-R / Spec V 2002–06 and 2008–12, Frontier NISMO 2005–08 (first NISMO-badged Nissan in North America; PRO-4X replaced it for 2009), Armada NISMO 2026 (460 hp), Ariya NISMO (Japan / Europe only). There was no Xterra NISMO trim (only NISMO parts).
  - Nissan histories: Rogue 2008–26, Murano 2003–26, Pathfinder 1996–2012 (incl. the 2008–12 V8), Kicks 2025–26, LEAF 2011–25 and PLUS, Ariya 2023, Maxima 2007–08, Titan XD gas 2017–19, Infiniti Q50 3.7 2014–15 and Q50 Hybrid (new engine `nissan:vq35hev`). Armada and Frontier spans now cited.
  - Fixes: the v95 Titan XD Cummins row was keyed to the Cummins group (duplicate vehicle) and now uses `nissan:Nissan|Titan XD`. 370Z, G35 / G37 and 2013–16 Pathfinder rows moved off the generic `nissan:vq` onto VQ37VHR / VQ35HR / VQ35DE. Not-sold notes: Z 1997–2002, Armada 2016, Q60 2016.
  - Tundra: SR's 348-hp tune (2022–23), i-FORCE / i-FORCE MAX by trim, Capstone (2022-on) and TRD Pro (2023-on) hybrid-only, TRD Pro 2015–17 and 2019–21 (none for 2018), 2000–04 V8 cited.
  - About cards: GT-R, Z, Titan, Frontier, Tundra.
  - Not added (unconfirmed): 2018–24 Kicks 1.6L, 2013–19 Sentra 1.8L by year, Rogue Hybrid, Patrol NISMO, Tundra TRD supercharger 2010–14, SR 348-hp tune after 2023.
- **100 (Chevrolet SUVs and cars; Ford Transit, Expedition, Super Duty, Taurus):**
  - Chevrolet: Tahoe 2000–14 (GMT800 / GMT900), Suburban 2015–26 incl. Duramax, Traverse 2009–26 (incl. the 2018–19 RS 2.0T and the 2024-on 2.5L turbo), Equinox 2010–20 (new engines `gm:laf` 2.4L DI and `gm:lf1` 3.0L V6), Blazer 2019–22, Trax 2015–26, Malibu 2008–25 (new vehicle), Impala SS 2007–09 and 2014–20.
  - Tahoe fixes: the 2015–20 6.2L rows moved from L87 to L86 (as on the Silverado); the first-generation 3.0L Duramax (LM2) runs to 2024 and the 305-hp LZ0 starts in 2025 (was 2023), per GM's 2025 Tahoe / Suburban release.
  - Ford: Transit 2015–26 (3.7L, 3.5L EcoBoost, 3.5L PFDi; the 3.2L diesel's US rating was never certified, so it is not listed), Expedition 2007–14, F-250 / F-350 Super Duty 2005–10 spans (6.0L Power Stroke 325 / 570, V10 362 / 457, 5.4L 300 / 365), Taurus 2008–18 (3.5L and SHO).
  - Not-sold notes: Colorado 2013–14, Trax 2023.
  - Many spans are a source-linked first year plus an unchecked carry-over row (`u100`); the next verification pass should confirm those year by year.
  - Not added (unconfirmed): Fusion 2006–16 and Escape 2001–19 outputs, Equinox diesel, Express van, Tahoe 6.2L 2009–14, 2.0L EcoBoost Taurus.
- **101 (Chevrolet, Toyota, Nissan gap fill):**
  - Chevrolet: Corvette 1973–96 year by year (C3 L48 / L82 / LS4 454 / L81 / L83 from Corvette Action Center's powertrain table; C4 L83, L98 TPI incl. the 3.07-axle automatic coupe ratings, LT1, ZR-1 1990–92 and 1995), Chevelle SS 454 1970–71 (LS5, LS6), Bolt EV 2017–23 / Bolt EUV 2022–23 / 2027 Bolt, Volt 2011–19, Avalanche 2003 and 2007–13 (plus the 2500's 8.1L), 2021 Trailblazer crossover (1.2T / 1.3T). New engines `gm:l48`, `gm:l82c3`, `gm:ls4bb`, `gm:l81`, `gm:crossfire57` (5.7L L83 Cross-Fire), `gm:ls6bb` (454 LS6), `gm:bev2`, `gm:voltec`. The `gm:ls5c3` name no longer says "(C3)".
  - Toyota: Camry 1997–2006 (5S-FE, 1MZ-FE, 2AZ-FE, SE 3.3L), RAV4 1996–2025 gasoline (3S-FE, 1AZ-FE, 2AZ-FE, 2GR V6 2006–12, 2AR-FE, A25A), Highlander 2001–10, Sienna 1998–2000 and 2007–20, Avalon 1995–2017 (the 2005 280 → 2006 268 hp SAE re-rating), Corolla XRS 2005–06 and 2009, Corolla 1.8L 2009–19 incl. LE Eco, Venza 2009–14. New engines `toyota:1mzfe`, `toyota:3mzfe`, `toyota:3sfe`, `toyota:1azfe`.
  - Nissan: Versa 2007–26 (new `nissan:mr18de`, `nissan:hr16de`), Quest 1993–2017 (new `nissan:vg30e`), Sentra 2013–16, 2018–19 and 2021–26.
  - Fix: the 1994–95 Impala SS row sat on the 2014-on Gen V LT1 and now sits on the Gen II LT1 (`gm:lt1gen2`).
  - Not-sold notes: Corvette 1983, Trailblazer 2010–20, Bolt 2024–26, Quest 2003 and 2010. About cards: Chevelle, Bolt, Volt, Avalanche, Sienna.
  - 13 rows are unchecked carry-overs (`u101`), e.g. Corolla 2015–19, Versa 2013–19 / 2021–26, Bolt EV 2020–23, Avalon 2013–17.
  - **Found:** this build lineage has no 1953–69 Corvette rows and no 1964–69 Chevelle / SS 396 rows, although the batch log says batches 64 and 76 added them. They were probably lost when the 80th build was replaced. Re-add them with sources.
  - Not added (unconfirmed): Corolla 2003–08 1.8L (130 vs 132 hp; 126 hp after 2006), Sienna 3.3L 2004–06 (230 vs 233 hp), Highlander 2.7L 2009–16, Bolt EUV range, Camaro 1974–81.
- **102 (Ford trims and packages by year):**
  - Labels now say which trims got each engine, as "Trim / Trim · standard / optional note · engine". **The year view splits the part before the first " · " on " / " and drops anything after a comma there, so keep trim names short, slash-separated and comma-free; put prose after the first " · ".**
  - F-150: 1997–2004 (4.2 V6 only on base / XL / XLT Regular Cab and 4x2 SuperCab; 4.6 with the Lariat; 5.4 standard on the 1999 Sport 4x4; spans extended to 2004 for the old-body Heritage), 2004–08 (5.4 standard from FX4 / Lariat up, not on STX; 4.2 V6 2006–08 Regular Cab 4x2 XL with the only manual; 4.6 2004, 2006, 2007–08 at 248 hp), 2009–10 (4.6 two-valve and three-valve with their standard trims, new rows), 2011–14 (3.7 / 5.0 / 3.5 EcoBoost / 6.2 standard and optional trims from Wikipedia's 12th-gen table), 2015 (Jalopnik: XL 3.5 V6, Lariat 2.7, King Ranch 5.0), 2021 (KBB trim guide) and 2024 (TrueCar trim guide, incl. the 5.0-only Regular Cab short-bed XL). The 2021 / 2024 rows are added rows, because the existing ones for those years are snapshot rows built outside `POWER` that `fixRows` can't reach.
  - Ranger: 1983–2010 every US engine by year (new `ford:lima`, `ford:cologne2829`, `ford:vulcan30`, `ford:cologne40ohv`, `ford:rangerdiesel`), the 3.0 standard on 1993–97 STX / SuperCab 4x4, the 4.0 SOHC on FX4 / Edge 4x4, and the 2019-on 2.3 / 2024-on 2.7 trims.
  - Bronco (2.3 on all but Wildtrak / Raptor; 2.7 standard on Wildtrak through 2024; the 2.3 is the only engine with the manual), Bronco Sport (1.5 vs Badlands 2.0), Explorer 2020-on (Platinum 365 hp only for 2020–21, 400 hp 2022–25; hybrid 2020–24 added on `ford:police33hybrid`), Expedition 2018–24 trims cited, Maverick 2.0 (AWD, Tremor), Super Duty 2023–24 (high-output 6.7 500 / 1,200, 6.8 / 7.3 trim split) plus unchecked 2021–24 standard-output diesel rows.
  - Mustang: the 2018–23 GT row now ends at 2021 (2022–23 is 450 hp); added Mach 1 2022–23 (470 / 410), GT350 / GT350R 2016–19, Bullitt 2020 (unchecked).
  - Not added: Super Duty 6.7 for 2017 (440 / 925?) and 2019 (Wikipedia gave these only in summary; a search couldn't confirm them), 2015–20 F-150 trim matrix beyond 2015, 2022–23 F-150 trims, Explorer 1991–2001 4.0 OHV ratings.
- **103 (Nissan years and rare variants; C/K and Silverado 1500 / 2500):**
  - Nissan: new `Nissan|Hardbody` (D21: Z24i 1986–89, KA24E 1990–97, VG30E V6 1990–95), Frontier 1998–2003 (KA24DE, VG33E, supercharged SC incl. Desert Runner SC) and 2022-on trims, Maxima 1985–94 (incl. the 1992–94 SE's VE30DE), Altima 1993–2001, 2006 SE-R and 2007–11 Hybrid, 300ZX Z31 1984–89 on the Z (incl. Turbo and the 1984 50th Anniversary Edition), 370Z 40th / Heritage / 50th / NISMO Tech editions (unchecked, same ratings), 200SX SE-R on the Sentra, Pathfinder 1987–2004 and Midnight / Rock Creek editions, Murano CrossCabriolet, Rogue Hybrid, Rogue Sport (new vehicle). New engines `nissan:z24`, `ve30de`, `vg30et`, `altimahev`, `mr20hev`.
  - Folded into base vehicles (`TRIM_CONSOLIDATE`): the five Skyline GT-R editions → `Skyline GT-R`; Fairlady Z Version NISMO Type 380RS → 350Z; 370Z → Z; Silverado 1500HD → Silverado 1500.
  - Fix: the supercharged VG33ER makes 246 lb-ft with the manual and 231 with the automatic. The 2004 Frontier snapshot rows had the labels swapped; this is corrected by a `PW` pass placed just before the final init line (snapshot rows are added after `POWER`, so `fixRows` can't reach them). The Xterra row now says manual.
  - Not-sold note: Z 2021–22.
  - Chevrolet: new `Chevrolet|C/K 2500` 1988–2000 (every gas and diesel engine, split light duty under / heavy duty over 8,500 lb GVWR), Silverado 2500HD 2007–10 gas (new `gm:ly6`; the 8.1L ended with the GMT800), 2001–07 gas rows cited, 1500HD 2001 / 2003 / 2005–06, the 2004–07 Hybrid (PHT, unchecked rating), 2019-on 1500 trims (4.3 WT / Custom 2019–21, 2.7 LT / RST, L82 WT / Custom, L84 LT and up, 6.2 upper trims and 4WD-only in 2019) and the 2022 Limited 2.7 at 348 lb-ft. New engines `gm:l29`, `lh6`, `l65`, `ly6`.
  - Year-view label tip: commas inside parentheses survive the trim parser, so "Light duty (under 8,500 lb GVWR)" works but "Under 8,500 lb" shows as "Under 8".
  - Not added: 1999–2000 light-duty Silverado 2500 ratings, the C/K 4.3 for 1993–95 (conflicting table rows), 2020-on HD trim matrix, 2024 Carhartt edition, Pathfinder Hybrid output, Altima Coupe, 200SX Turbo / SE V6.
- **104 (Infiniti history; Titan, Sierra and Silverado trims):**
  - Infiniti, filed under today's model names: Q45 1990–2006 (all three generations; new `nissan:vh45de`, `vh41de`, `vk45de`), J30, I30 (1996–99), G35 sedan on `Q50` and G35 coupe on `Q60` (2003–07, manual Rev-Up 298 hp vs automatic 280), G37 convertible and IPL G Coupe (unchecked), M35 / M45 / M37 / M56 and Q70 3.7 / 5.6 on `Q70`, QX4 (new vehicle), QX56 2004–10 on `QX80`, FX35 / FX37 / FX50 and QX70 on `QX70` (new `vk50ve`), EX35 / EX37 / QX50 3.7 and the 2019–25 VC-Turbo QX50 on `QX50`, JX35 / QX60 including the 2014–17 Hybrid (new `qr25schev`).
  - Titan: trim rows by generation (XE / SE / LE; PRO-4X from 2008; S / SV / SL from 2011; S / SV / PRO-4X / SL / Platinum Reserve 2017–24; regular cab 2017–19 only).
  - GMC Sierra 1500: 2001 C3 (AWD, 325-hp LQ4), 2002 Denali, 2007 and 2009–13 Denali 6.2 (first half-ton with the 6.2), 2014–18 trim split (4.3 Sierra / SLE; 5.3 standard on SLT and Denali; 6.2 optional on those), 2016 All Terrain X (365 hp). Sierra HD 2017 All Terrain X.
  - Silverado 1500 trims by year: 2007 (Work Truck 4.3 / 4.8 crew cab, 1LT 4.8, 2LT and LTZ 5.3, 6.0 optional with Z71 or 20-inch wheels), 2010 (LS 2WD-only 4.8, XFE 5.3, LT / LTZ 6.2), 2013 (same structure, XFE 2WD crew cab), 2014 and 2016 (4.3 on all but LTZ / High Country, 5.3 standard on those, 6.2 optional on them), 2024 (TrueCar trim guide: Custom 2.7-only, High Country 5.3, RST / LT Trail Boss / ZR2 6.2, diesel from LT up, Trail Boss and ZR2 4WD-only).
  - Not added: 2003–06 Sierra Denali (LQ4 vs LQ9 conflict), FX35 / FX45 2003–08 and I30 2000–01 / I35 (no verbatim figures), Titan flex-fuel ratings, Midnight / Rally / Special Ops engines, 1999–2006 Silverado 1500 by trim (sources failed), 2026 trims (source blocked).
- **105 (Tahoe / Yukon history; GMT800 and HD trims; Armada / QX80 trims):**
  - Tahoe 1995–2014 from Wikipedia's engine tables: two-door 4WD 6.5L diesel (1995–99), exact 4.8 / 5.3 ratings by year (replacing the v100 range rows), 2000 Limited / Z71 5.7L, GMT900 4.8 2WD-only (295 / 305), 5.3 at 320 / 340 (326 / 348 on E85), LTZ 6.2 (395 option 2008–09, 405 standard on 4WD LTZ 2010–11; the article conflicts with itself), Hybrid 2008–13.
  - GMC Yukon 1992–2014: every engine now has its own rows (5.7 TBI, 6.5 diesel, Vortec 5.7, 4.8 / 5.3, Denali 6.0 LQ4 320 → 335 hp, Denali 6.2 380 hp 2007–08 then 403, Hybrid). Adding Denali rows means the year view no longer borrows the Tahoe's lineup for those years (`LINEUP_TWINS` only fills empty years), so the regular engines were added too.
  - Silverado 1500 GMT800 trims: 2000–01 (Consumer Guide: 4.3 on regular cabs and 2WD extended short beds, 4.8 on 4WD extended and long beds, 5.3 standard on extended-cab LT), 2003 and 2006 (KBB spec tables by cab and bed), 2004 (5.3 on the new light-duty Crew Cab).
  - Silverado HD trims: 2014 (6.0 WT / LT, Duramax LT / LTZ crew cab), 2015–19 (High Country new for 2015), 2021 (Custom new for 2020; diesel on LT / LTZ / High Country), 2024 (ZR2 new). Sierra HD 2024: gas standard, Duramax standard on Denali Ultimate.
  - Armada trims by generation (SE / Off-Road → SV / SL / Platinum 2011 → Midnight 2021 → PRO-4X 2025) and QX80 (Limited 2015; Pure / Luxe / Sensory / Autograph 2025).
  - **Lesson:** batch code shares one scope, so a `const` name reused from an earlier batch (here `SV`) throws and stops the whole page. Use batch-specific names (`SV5`, `TA5`…) and test for page errors after every patch.
  - Not added: 1992–99 Suburban ratings (none printed), 2000–14 Suburban (summary only), 2015–20 Yukon Denali, Sierra 1500 GMT800 trims (sources failed), Armada NISMO tune.
- **106 (Corvette 1953–2027; Mercedes-AMG GT and SLS; Colorado, Expedition, Navigator):**
  - Corvette now has a rating for every model year 1953–2027 (no 1983 car). From Corvette Action Center: C1 1953–62 (Blue Flame six; 265; every 283 carburettor and fuel-injection option including the 1957 283-hp fuelie; 1962 327s), C2 1963–67 (L75 / L76 / L79 / L84 327s, 1965 L78 396, 1966–67 L36 / L68 / L71 / L72 / L88 / L89 427s), 1968–69 and 1971 (gross with net in the label). New engines `gm:bluef`, `sbc265`, `sbc283`, `sbc327`; `gm:l72` renamed to cover every 427. This supersedes the section-7 "left out" notes on the 1957 fuel-injected 283 and 1967 L71.
  - Corvette C5–C8 trims and editions: C6 Grand Sport (hand-built LS3, manual coupes), C7 Premiere Edition and Z06 Z07, C8 1LT / 2LT / 3LT with standard vs NPP exhaust and the Z06 Z07 package.
  - Mercedes-AMG GT: GT R 2017, GT R Pro, GT C Edition 50, facelift GT / GT S and 2021 GT (unchecked, summary table), C192 GT 43 / GT 55 / GT 63 S E Performance (805 hp, mbusa), new `Mercedes-AMG|GT 4-Door` (GT 53, GT 63, GT 63 S, GT 63 S E Performance 831 hp), new engine `mercedes:m177phev`. New `Mercedes-AMG|SLS AMG` (coupe / roadster 563 hp, GT 583 hp); the Black Series key folds into it.
  - Colorado / Canyon 2015–22 trim rows (2.5 base / WT, 3.6 WT to ZR2, 2.8 Duramax LT / Z71 / ZR2); Expedition 1997–2002 (4.6 standard, 5.4 optional; the old 1997–2004 5.4 row now ends 1998); Navigator 1998–2006 (5.4 SOHC, then the 300-hp InTech DOHC from mid-1999, new `ford:intech54`, then the three-valve); Ram 3500 Limited / Longhorn Cummins-only note.
  - Not added: 1964 and 1966–67 C2 base-engine and torque figures (Corvette Action Center prints those tables as images), 1970 LS7 (never sold), AMG GT Track Series and GT 63 Pro (conflicting / not US), SLS Electric Drive (Europe only), Navigator 2007–17, Suburban 2000–20 (summary only).
- **107 (Silverado 1500 / C/K 1500 audit):** a year-by-year dump of every 1988–2027 row (`audit.js` pattern: filter `PW` by key, list engines per year with ✓ status) found and fixed:
  - C/K 1500 had no 5.7L TBI for 1988–93 (added at the light-duty 210 hp / 300 lb-ft) and no 6.2L diesel (LH6 1988–89 manual / automatic and 1991–93). The 1992 190-hp row is the over-8,500-lb rating; noted.
  - Two C/K rows sat on the Silverado 1500 key: the 1992–95 5.7L TBI (wrong 1992–93 rating, dropped) and the 1996–98 Vortec 5.7L (moved to the C/K 1500, which lacked 1997–98).
  - Overlapping GMT800 spans: the unsourced 2000–06 270-hp 4.8L row now ends 2003 (285 hp from 2004; 2005 added unchecked) and the 2004–06 330 lb-ft 5.3L row ends 2005 (335 lb-ft for 2006).
  - eAssist 5.3L added for 2016 (LT crew cab, California only) and 2017 (more states); 2018 relabelled nationwide LT / LTZ.
  - 2027 fifth generation: new `gm:sbc57g6` (5.7L, 402 / 428) and `gm:sbc66g6` (6.6L, 481 / 501) from GM's announcement, the 3.0L Duramax now on the Work Truck, the 2.7L relabelled. GM has not published a trim-by-engine table yet; revisit once order guides are out.
  - Unsourced 1988–98 C/K rows that match the C/K powertrain table are now cited.
- **108 (UI: one "Engines & ratings" section in vehicle panels):** the user noticed the year view and the old "Horsepower & torque" list showed the same ratings twice (the year select drove both). A final layer (`<style id="v108-merged-ratings">` and `<script id="v108-merged-ratings-js">`, just before `</body>`) wraps `showPanel` for vehicles that have a year view:
  - Renames "Year by year" to "Engines & ratings" and adds a "Selected year" / "All years" toggle (remembered in `aft.lineupMode`).
  - Selected year: each engine card gets a "Details & sources" drop-down with that engine's `prHTML` rows for the year (source link, ✓ status, notes), rebuilt by a MutationObserver whenever the year changes.
  - All years: every rating grouped by engine (respecting the related-models checkbox), rows covering the selected year highlighted, plus the SAE-gross warning and the Power-tab link.
  - Removes `.panel-power` from those vehicle panels; engine panels and vehicles without a year view keep the old list. `applySpotterYear` falls back to the year select when there is no `select[data-pw]`.
  - Also fixed: the v106 Ram 3500 Limited row used `stellantis:c67`; the engine is `cummins:c67` (vehicle string `stellantis:Ram|3500`). Watch for the "power: no engine" console warning after each patch.
- **109 (Porsche, Acura and BMW history; 252 ratings, 29 new engines):**
  - Porsche: 911 1965–71 (2.0 / 2.2, DIN PS converted; `vw:p911early`), 911 SC and 3.2 Carrera US (`vw:p911sc`), 930 Turbo US 1976–79 and 1986–89 (`vw:p930t`), 964 / 993 incl. RS America, Turbo 3.6 and the US 993 Turbo S at 424 hp SAE (`vw:m64`); new `Porsche|Boxster` and `Porsche|Cayman` keys for 1997–2016 (986 / 987 / 981, PS-converted hp); new 924 / 928 / 944 / 968 vehicles with US ratings where printed (`p924i4`, `p944i4`, `p928v8`); Cayenne 2003–2010 (`pcayv8`, `vr636`), Macan 2015–24, Panamera 2010–16, Taycan 2020–24 (overboost figures); Cayenne 2011–23 and Panamera 2017–24 unchecked (summary tables).
  - Acura: Legend 1986–95, TL 1996–2003 (incl. 2.5 five-cylinder), RL 2002–12, CL 1997–2003 (incl. Type S), Integra 1988–93 (incl. GS-R), MDX 2001–20, RDX 2007–12 turbo and 2016–18 V6, ZDX 2010–13, TLX 2015–25, ILX 2013–22 (incl. Hybrid), TSX 2009–14 (incl. V6). New engines `honda:c27a`, `c35a`, `g25a`, `d16a1`, `b17a1`, `f22b`, `k23t`, `r20a`, `lea15ima`. Vigor has no printed rating (vehicle linked via `g25a`).
  - BMW: 3 Series E30–G20 (kW-converted where Wikipedia's 'hp' column is really PS), 4 Series F32 / G22, 5 Series E34–G30 (incl. 530e / 545e), X1, X3 / X3 M, X5 2007–18 (incl. 35d diesels and 40e), X6 (incl. ActiveHybrid), Z3 (incl. US-only 2.3 / 2.5i) and Z3 M 1998–2000, Z4 E85 / E89, i3 (EV and REx with EPA ranges), i8, iX. New engines `bmw:m20`, `m30`, `m50`, `m42`, `m60`, `n54`, `m57`, `n57`, `x6ah`, `i3rex`, `i8hev`.
  - Not added: 7 / 6 / 8 Series (generation pages not fetched yet), 1972–77 911 (summary only), MDX 2003, RDX 2013–15, US E30 318i / 318is details, E36 318i 1996–98, 335d.
- **Batch 110 (v110)**: Camaro base engines and trims for 1967–2002 (230 / 250 sixes, 307 / 327 / 350, 305 LG3 / LG4, L69 H.O., Iron Duke, 2.8 / 3.1 V6, L03, LB9 TPI by year, 3.4 / 3.8 V6), rare Camaros (1967–68 Yenko Super Camaro, 1969 Yenko/SC 427 with 201 built, ZL1 now sourced, 1970 L78 SS 396, 1972 last SS 396, 1983 L69, 1993 Pace Car, 1997 30th Anniversary LT4 SS, 2017 50th Anniversary / 1LE, ZL1 1LE, 2024 Collector's Edition) and a Camaro About card. Silverado 1500 per-year trim rows for 2011–12, 2015, 2017, 2019, 2022–23, 2025 (Carvana) and 2026 (carry-over, noted), plus 2016 Realtree, 2026 Stars & Steel (1500 and HD) and the 2026 Callaway–Waldoch Launch Edition; Intimidator SS label now gives 1,333 planned / about 933 built. New engines: `sbc307`, `sbc305c`, `l69`, `ironduke`, `gm60v6`, `l32v6`; `atlas14` now covers the 230 six. New `NAME_GUIDES` entries "Rare and special Camaros" and "Rare and special Silverados" list the editions whose ratings aren't documented (Rally, Midnight, Special Ops, Transformers, Neiman Marcus, COPO race cars, 1LE counts, etc.). Year view change: `lyGenericHead()` hides an engine-only row (label head is just an engine name / code, e.g. "6.2L V8", "LZ0") when a trim-labelled row with the same rating covers that year; it still shows in "Details & sources" and "All years". About 32 of the new rows are unchecked (Wikipedia tables read only in summary; the HD Stars & Steel rating from a search summary).
- **Batch 111 (v111)**: Toyota Tacoma configurations by year, 1995–2026: first generation by cab / drive with PreRunner, S-Runner, TRD Off-Road, Double Cab and the TRD 3.4 supercharger kit; second generation 2.7 / 4.0 by year (2009, 2010, 2011–13, 2014, 2015 from Carvana, Cars.com and Toyota's 2015 release; 2005–08 unchecked), X-Runner, 2008 Ironman, Baja Series and the 2015 TRD Pro; third generation per-year grade lists for the 3.5L (2016–23) and one 2016–23 2.7L SR / SR5 row, with 2021 Nightshade and 2023 Trail Special Edition; fourth generation 2024–26 grade-by-engine rows (2026 repeats 2025, noted). Tacoma About card and "Rare and special Tacomas" guide. Year view: `lyGenericHead()` also treats a head made only of words from the engine's name / code (e.g. "Hybrid", "i-FORCE MAX hybrid") as engine-only. PW-level relabels (before the final init line) tidy the 2024 snapshot rows and widen the 2005 X-Runner row to all V6 configurations. Toyota's dealer sourcebook PDFs (engage.toyota.com) could not be read — their text layer is glyph-encoded; rendering pages as images would be the way to use them.
- **Batch 112 (v112)**: Mustang 1999–2014. New engines `essex38` (3.8 / 3.9 Essex V6 — the 1999–2004 base engine was missing), `tuner46sc` (Saleen / Roush supercharged 4.6), `saleen50` (Saleen 302), `shelby54` and `shelby58` (Shelby Super Snake / Shelby 1000). Per-year trim rows: V6 (1999–2014 incl. Pony, Appearance and Performance packages), GT (35th Anniversary, Spring Feature, Centennial, 40th Anniversary, GT/CS, 45th Anniversary, Iacocca Silver, Track Pack), 1999 Cobra (8,095; recall), 2001 Cobra, 2004 Mach 1, 2003–04 Terminator specials (10th Anniversary, Mystichrome), GT500 / GT500KR counts, Boss 302 Laguna Seca. Shelby GT-H / GT / GT-C / Terlingua / Super Snake / Prudhomme / Shelby 1000 / GT350 (2011–14) and Saleen S281 / S302 / H302 and Roush 380R–5XR rows use basis "Builder claim (aftermarket / tuner)". New "Rare and special Mustangs, 1999–2014" guide.
- **Batch 113 (v113)**: Ford Focus 2000–2018 by year — new engines `focus20spi`, `zetec20`, `duratec2023`, `gdi20` (and `eb10fiesta` now covers the Focus 1.0); first-generation trims per year (LX / SE / ZTS / ZX3 / ZX5 / ZTW, Kona, S2), 2.3 PZEV, SVT Focus with yearly ZX3 / ZX5 counts, 2005–07 ZX3 / ZX4 / ZX5 / ZXW and ZX4 ST, 2008–11 S / SE / SEL / SES, 2012–18 S / SE / SEL / Titanium / SFE, 1.0 EcoBoost SE, ST, RS (2018 Limited Edition) and Focus Electric; Focus About card and "Focus variants and special editions" guide. Mustang 2015–26 trim rows (EcoBoost / Premium / Performance Pack / HPP, GT / Premium / Performance Pack, 50 Year Limited Edition, Bullitt, Mach 1, GT500 Carbon Fiber Track Pack, Shelby American GT, Dark Horse, GTD) and the new 2026 Dark Horse SC (795 hp); the Mustang rare-edition guide now runs to 2026.
- **Batch 114 (v114)**: Chevelle 1964–77 by year (was only three 1970–71 rows): 194 / 230 / 250 sixes (`atlas14` renamed), 283, 327 (incl. 1965 L79), 307, 350, new `sbc400`, 305, 396 L35 / L34 / L78 by year, 1965 Z-16, 1969 COPO 9562, 402 (1970–72), 454 LS5 / LS6 / LS4 to 1975, with series per year (300, 300 Deluxe, Malibu, Malibu SS, SS 396 series / option, SS 454, Concours, Heavy Chevy, Deluxe, Laguna, Laguna Type S-3, Malibu Classic, Chevelle SE) and counts; "Rare and special Chevelles" guide. New trim-explanation feature (see the note at the top) with 157 entries for the Chevelle, Camaro, Silverado 1500, Tacoma, Mustang and Focus.
- **Batch 115 (v115)**: Mercedes-Benz US model history by class — C (W202–W206), E (W210–W214), S (W140–W223 incl. Maybach), CL, SL (R129–R231), SLK / SLC, SLR McLaren, CLK, CLE, CLS, G, GLK, GLC, GLA, GLB, A, ML / GLE, GL / GLS — with new engines `m104`, `m111`, `m112`, `m112k`, `m113na` (4.3 / 5.0; `m113` stays the 5.4 AMG), `m119`, `m120`, `m137`, `m152`, `m155`, `m271k`, `m27225`, `m27235`, `m273`, `om606`, `om648`, `om651`. The separate `Mercedes-AMG|…` vehicles (C 63, C 43, C 55, E 63, E 55, S 63, S 55, CL 65, CLA 45, CLE 53, CLK 55, CLS 63, ML 55, G 55, G 63, GLA 45, SL) and `Mercedes-Maybach|S 680` are now folded into their classes via TRIM_CONSOLIDATE; AMG GT, GT 4-Door and SLS stay separate. Ratings come from Carvana's US spec pages where available and otherwise from Wikipedia tables (meta basis says the market is not always stated). New About cards for 11 Mercedes vehicles, a "Mercedes-Benz model names, decoded" guide (all Mercedes-Benz keys) and Mercedes trim explanations. GMC Sierra 1500 per-year trim rows for 2010, 2012, 2015, 2017, 2019, 2021, 2022 and 2025 (Carvana) plus the 2001 C3, with Sierra trim explanations (Pro, SLE, SLT, Denali, Denali Ultimate, Elevation, AT4, AT4X, XFE, Hybrid, C3). Not done: Sierra 1999–2009 per-year trims (Carvana pages 404), SL R232 (2022-on), C-Class C350e / GLC 2023-on details, GLE / GLS coupes.
- **Batch 116 (v116)**: Owner feedback: merged AMG rows read "AMG E 63 · E 63 S …", hiding which version was which. A PW-level relabel map (before the final init line, tagged `// v116`) now gives every merged Mercedes-AMG / Maybach row and the older spaced snapshot labels ("C 250", "S 550", "E 350 …") a clear head such as "E63 AMG S-Model 4MATIC", "AMG E 63 S 4MATIC+ (sedan and wagon)", "C63 AMG with AMG Development Package (P31)". E-Class: 2013 E63 rows, 2025–26 E350 / E450 rows, a full `TRIM_INFO` rewrite (31 entries covering every E-Class model, body style and AMG version by era) and an "E-Class versions, decoded" guide (added with `NAME_GUIDES.unshift` so it wins over the general Mercedes guide for that key). When merging future AMG-style sub-models, write the version name into the head of the label rather than relying on the consolidation prefix.
- **Batch 117 (v117)**: Mercedes C-Class (33 entries) and S-Class (32) trim explanations rewritten for every name in the year view, plus "C-Class versions, decoded" and "S-Class versions, decoded" guides (unshift). SL R232 (AMG SL 43 / 55 / 63 / 63 S E Performance) and SL explanations; GLC 2024-on (GLC300, AMG GLC 43, GLC350e, AMG GLC 63 S E Performance); GLE 2024 refresh (GLE350 / 450 / 580 US ratings; the 2020 rows now end at 2023); C350e (2016–18); S500 re-rated 442 hp for 2024 (older row ends 2023 via a PW-level fix) and S580e to Carvana's US 510 hp / 694 lb-ft. Gaps: 2013 C63 AMG standard-tune row, Maybach SL 680 and GLE450e ratings (not printed), GLE / GLC coupe year detail.
- **Batch 118 (v118)**: Durango per-year trim rows 1998–2026 (Wikipedia for 1998–2009, Carvana 2011–25), new `magnum59sc` (Shelby S.P.360), the 2026 R/T 392; PW-level fixes end the old SRT 392 and unlabelled 293-hp V6 rows at 2024. Trim explanations (99 entries) for the Durango, Charger, Challenger, Dodge-era Ram 1500, Viper, Dakota, Dart (2013–16) and Grand Caravan (plus a 2011–20 Grand Caravan trim row); About cards for those seven; "Durango versions, decoded" guide. Gaps: Dodge-era Ram 1500 and Dakota per-year trim rows, Viper's 2017 editions (GTC / TA 2.0 / VooDoo not described by the sources read), classic Charger / Challenger trims.
- **Batch 119 (v119)**: Cadillac Escalade per-year rows 1999–2025 for every generation (Wikipedia plus Carvana 2010/13/15/18/21/23/25): 5.7 L31, 5.3 LM7 (rear drive), 6.0 LQ9 (AWD / ESV / EXT), 6.2 (2007–14), Hybrid 2009–13, L86 2015–20, L87 2021–25, 3.0 diesel "600D" 2021–24, Escalade-V 2023-on; Escalade IQ / IQL standard and Velocity Max rows. LM7 / LQ9 torque is borrowed from the same engines' Tahoe / Silverado SS ratings (noted per row) because the Escalade article prints hp only; the 1999–2000 L31 row has no torque. PW fixes move the unlabelled L87 and old "Escalade-V" rows to 2026 only. Escalade TRIM_INFO (21 entries), About card and "Escalade versions, decoded" guide. Dodge Ram 1500: Hemi Sport, SRT-10 VCA / Yellow Fever / Commemorative / Night Runner, 2009 ST / SLT / Laramie rows (and Ram-brand 2010). Dakota per-year rows 1997–2011 plus guide. Classic Charger / Challenger trim explanations are prepended (so the classic R/T, SE, Rallye win in their years); Challenger 1970–73 base / SE / Rallye rows; Super Bee and Coronet R/T heads relabelled with trims; Charger 500 consolidated into the Charger. New B82SRC keys end in 119 or are cesc / cram / cdak. Gaps: Ram 1500 1994–2008 base trims (WS / ST / SLT / Laramie SLT; the sources read name none), Dakota 2nd-gen cab-by-trim detail, Escalade 2026 trims, ESV-specific gen 2–4 trims.
- **Batch 120 (v120)**: Escalade gaps — v119 rows re-sourced in place (1999 L31 now 255 / 330 from Cars.com; LM7 / LQ9 torque from MotorWeek and Cars.com instead of borrowed), the LQ9 row split into 2002 (EXT launch), 2003 (ESV launch) and 2004–06; ESV / EXT trims for 2008 (Platinum debut, ESV 2WD), 2011 EXT, 2012 / 2016 / 2019 ESV; 2019 Sport Edition; 2026 Base / Luxury / Sport / Platinum Luxury / Platinum Sport / V-Series (PW splice removes the v119 2026 placeholders); Escalade IQ 2025 (Luxury 1 / Sport 1 / Luxury 2 / Sport 2) and 2026 trims. The 2019 Noir package is in the guide only (GM Authority read partly in summary; an unchecked row would be dropped as a duplicate). Dodge Ram 1500 from Cars.com: Work Special 1995–2001, 2001 V8 split, 2002 SLT 5.9, 2003 / 2005 / 2007 / 2008 ST / SLT / Laramie / SXT / Mega Cab rows, 1997 SS/T torque 335 (PW fix), "Dodge Ram 1500 versions, decoded" guide; Cars.com's odd 2007–08 HEMI figures are DIFFER notes. Dakota: new `amc25` engine (2.5L four, 1997–2002), 2008 trims (ST / SXT / SLT / Sport / TRX4 / Big Horn / Lone Star / Laramie; 302 hp per the road test, DIFFER vs 290 / 310), "Base" = Cars.com's name for the ST. Use `TRIM_INFO[k]=[...].concat(TRIM_INFO[k])` when a new same-name entry must win in its years.
- **Batch 121 (v121)**: Tahoe (1995–99, 2010, 2012, 2015–26), Suburban (2000–26 incl. 2500 8.1L, 2006 LTZ, 75th Anniversary, Premier Plus) and Yukon (1998 Denali, 2012, 2015–26 incl. SLT Standard Edition, AT4, Denali Ultimate, Elevation, AT4 Ultimate) trim rows from Wikipedia + Carvana, with TRIM_INFO, About cards and two guides; PW splices remove the unlabelled 2021–26 placeholders and the Tahoe 1996–2000 255/330 row; Yukon L31 row ends 1997, Tahoe L86 row starts 2018. Reconciliation: Chrysler's 2005 Dakota spec sheet (PDF text via pdftotext) — 3.7 standard on every trim, 4.7 optional on all, High Output 250+/300+ estimated for 2005 (260 from 2006, PW y1 fix); 2008 Dakota 302/329 kept, Wikipedia's 310 row spliced; Dakota 5.2 now 230/300 and 2.5 145 lb-ft (Cars.com / Edmunds); SRT-10 500/525 confirmed by Chrysler's engineering sheet; Ram 2007–08 HEMI 345 per Cars.com What's New. Correction: the 2008 SXT was a Ram 2500/3500 model — removed from the 1500. Ram 1998 / 2004 / 2006 / 2008 rows; Escalade Onyx (2021) and Noir (2019, now fully read) rows. **Bug fixed:** rows re-sourced in place must use B82SRC keys that exist at that point in the file — v120's Escalade 1999–2006 rows had silently lost their sources; v121 inserts an early `Object.assign(B82SRC, …)` before Batch 119. Check with a PW scan for `note` matching the version and `!source`.
- **Batch 122 (v122)**: Jeep Wrangler YJ / TJ / JK / JL per-year trim rows 1987–2026 (Wikipedia generation articles + Carvana 2011–26), Grand Cherokee ZJ / WJ / WK / WK2 / WL rows 1993–2026 (Wikipedia + Carvana 2011–26) and a new `Jeep|Grand Cherokee L` key (shows as the "L" variant on the Grand Cherokee page). New engines `amc42` (4.2 six, YJ), `pt24` (2.4 four, TJ), `crd30` (OM642 3.0 CRD). PW fixes splice the old 4.2-as-4.0 YJ row, the Rubicon 392 2021–25 placeholder, the Grand Cherokee `magnum` rows, the unsourced 2014–21 SRT and blank 2022 HEMI rows, and rename generation-label placeholders ("JK generation", "WK2 generation", …) to plain engine labels so lyGenericHead hides them where trim rows exist. Ram 1500 2011 / 13 / 15 / 17 / 19 / 21 / 23 / 25 / 26 and 1500 Classic 2020–23 trim rows (many pairings read from Carvana's shared engine list, noted per row). TRIM_INFO: Wrangler 55, Grand Cherokee 31, Ram 1500 23, 1500 Classic 4; About cards and guides for Wrangler, Grand Cherokee and Ram 1500. Gaps: Grand Cherokee 2012–13 / 2015 / 2017 / 2019 / 2021 and Wrangler 2017 / 2020 / 2023 per-year trims; ZJ 5.2 rating conflict (220 vs 225) left as DIFFER.
- **Batch 123 (v123)**: Jeep Gladiator 2020–26 per-year rows, TRIM_INFO (20), About card and guide (blank placeholder spliced). Wrangler 2017 / 2020 / 2023 and Grand Cherokee 2012 / 2013 / 2015 / 2017 / 2019 / 2021 gap-year rows from Carvana. Ford F-150: 1992–96 trim rows (Custom, Special, Nite, Eddie Bauer; ratings from existing rows), 2022 / 2023 / 2025 / 2026 trim rows, 31 TRIM_INFO entries covering every trim and edition 1992–2026 from Wikipedia's six generation articles, and an "F-150 versions, decoded" guide; PW relabels of the placeholder labels "Combined gas + electric", "Standard output", "Raptor (high output)" and blank 5.0L rows. GMC Sierra 1500 1999 / 2003 / 2005 / 2007–09 trim rows from Cars.com (torque borrowed from the Silverado's same engines, noted) plus SL / SLE / SLE1 / SLE2 / All Terrain / Denali entries prepended for those years. Gaps: Sierra 2000–02, 2004, 2006; Sierra hybrid trim; F-150 1997–2008 engine-to-trim rows mostly already present.
- **Batch 124 (v124)**: NEW FEATURE — "Terms & badges explained" glossary on vehicle pages (user request: explain configurations / trims / badges / brand terms for every vehicle from now on). Data: `GLOSSARY[vehicleKey] = [{c:category, t:term, d:meaning, s:B82SRC key}]`; `GLOSSARY_SHARE[key] = otherKey` reuses another vehicle's list (Dodge Ram 1500 and Ram 1500 Classic share the Ram 1500's). Categories render in the order of `GL_ORDER` (Cabs, beds & bodies → Engines & powertrain → Drivetrain & chassis → Trim names → Packages & badges → Features → Naming & history). `glossaryHTML()` is defined in the Batch 124 block and inserted after `nameGuideHTML` in the vehicle panel; CSS in `<style id="v124-glossary">`. Glossaries so far: Ram 1500 (37 terms: cab names, bed lengths, 1500/2500/3500, HEMI, MDS, VVT, eTorque, Pentastar, EcoDiesel, Hurricane SO/HO, Hellcat, trims, Night Edition, G/T, Built to Serve, RamBox, multifunction tailgate, Uconnect, brand split, Classic), Tundra (13), 4Runner (10). Also Tundra trim rows 2000–2026 and 4Runner trim rows 1985–2026 with TRIM_INFO (Tundra 19, 4Runner 20), 4Runner About card, two guides. Level 1 / Level 2 equipment groups were left out (not verified).
- **Still unverified (about 235 rows; the new GMT400 / GMT800 Silverado gap-fillers are among them)**: view them with `PW.filter(r=>!verified(r))`. Left unverified on purpose: Tacoma 2RZ / 3RZ spans, 1996–2002 4Runner 5VZ 183 hp, 2005 Sequoia 282 hp, LX 470 2003–05, 2007–09 Tundra 4.0, FJ60 2F, the 22R-E (no rating listed because sources range from 105 to 116 hp), and the 1992–93 F-150 5.8.

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

- Re-add the 1953–69 Corvette (Blue Flame six, 265 / 283 / 327 / 427) and 1964–69 Chevelle SS 396 with sources (see the v101 note).
- Toyota: Supra Mk3 1986½–92 by year, Celica 1986–2004, Previa (supercharged), Echo / Yaris, Solara. Nissan: D21 Hardbody, Infiniti G35 / FX / QX56 / M45 / Q45. Chevrolet: Camaro 1974–81, Cavalier, Cobalt, Spark, Express.
- Older Silverado/Sierra HD gas engines (6.0L Vortec).
- 1980s–90s Ford F-250/F-350: 7.5L 460, 6.9/7.3 IDI diesels.
- Toyota T100, Chevy S-10 and Colorado history, GMC Sierra Denali generations.
- Panther-platform luxury cars: Lincoln Town Car, Mercury Grand Marquis.
- 1980s F-150, Ranger and Explorer gaps.
- Audi RS/quattro history, Porsche 911 generations, Honda Civic Type R and Integra Type R, Mitsubishi Evo and 3000GT VR-4.
- About 130 rows still have no trim label but belong to single-size engines. Adding real trim or package names, for example which Ram 1500 trims offered the 5.7 Hemi each year, would improve the details card.
- Periodically re-verify a sample of older rows, especially descriptions written early in the project.
