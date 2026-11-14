# Automaker Family Tree: handoff notes for the next AI model

Last updated: 2026-11-15. The current build is the 80th published version.

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
