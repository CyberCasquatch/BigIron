# Voron 2.4 Stringing & Seam Artifact Troubleshooting

**Printer:** Voron 2.4r2 Pro+ 350mm (Formbot kit)
**Slicer:** PrusaSlicer
**Date started:** July 2026

## The Problem

I run a Voron 2.4 and a Prusa MK4S side by side, using the same filament types on both. PETG that printed extremely stringy on the Voron printed clean on the Prusa with no changes to the filament itself. The same PETG spool, same room, same day — just a different printer. On top of the stringing, I was also seeing small blob-like artifacts sticking out along the perimeters, on both PLA and PETG.

Since the exact same filament behaved differently on two printers, it ruled out the filament itself (moisture aside) and pointed at a printer/firmware/slicer settings gap between the two machines.

![All Tests](/troubleshooting/images/PA_PLA/1.PNG)


## The Diagnosis

The Prusa MK4S ships with Linear Advance already tuned per filament profile out of the box. Klipper (the Voron's firmware) has the equivalent feature — **pressure advance** — but it does nothing until it's calibrated manually. Without it, the hotend builds up melt pressure during fast segments and releases it as ooze during travel moves and direction changes, and dumps it as a blob at corners/seams. That matches both symptoms:

- **Stringing** — pressure not relieved before travel moves, so retraction is fighting pressure it doesn't know is there.
- **Perimeter/seam artifacts** — classic pressure advance "zits," where built-up pressure dumps as a bump at a corner or seam.

The Voron profile was also running meaningfully faster than a typical stock Prusa PETG profile (80 mm/s perimeters vs. Prusa's more common ~45 mm/s), which increases melt pressure further and makes an untuned PA situation much more visible.

## The Fix — Step by Step

### 1. Calibrate Pressure Advance (Klipper)

![Before PA](/troubleshooting/images/PA_PLA/1.png)
![Before PA](/troubleshooting/images/PA_PLA/1_2.png)
Used [Andrew Ellis' pressure advance calibration tool](https://ellis3dp.com/Print-Tuning-Guide/articles/precise_pressure_advance.html) — a web tool that generates a G-code test pattern directly (no slicer needed), based on printer's real max speed/accel and print temps.

Steps:
1. Input nozzle temp, bed temp, and actual print speed/accel (matters — PA is somewhat speed-dependent).
2. Load the generated G-code directly onto the printer, bypassing the slicer.
3. Print it and inspect corners across the test lines for the cleanest corner with least blobbing/gapping.

**Result for PLA: `pressure_advance = 0.030`**

PETG PA value: _TBD — will add once tested._

> 📷 ![PA calibration test print](/troubleshooting/images/PA_PLA/EllisPATestPLA.png)

### 2. Applying PA in the slicer (not printer.cfg)

Wanted this to work like Prusa does — Prusa injects `M900 K<value>` per filament profile in **Filament Start G-code**, so PLA/PETG each carry their own value automatically. Klipper's equivalent command is `SET_PRESSURE_ADVANCE`.

**Where it lives:** PrusaSlicer → Filament Settings → Custom G-code → **Filament Start G-code**, per filament profile (not the printer's global start G-code). This way switching filament profiles automatically applies the right PA value, same as Prusa's per-filament `M900 K`.

```
SET_PRESSURE_ADVANCE ADVANCE=0.030
```

**Important:** Keep this on its own line, separate from the printer's `PRINT_START` call. If run together on one line it gets parsed as arguments to `PRINT_START` instead of executing as its own command.

Printer profile custom G-code (unchanged):
```
PRINT_START EXTRUDER=[first_layer_temperature[initial_tool]] BED=[first_layer_bed_temperature] CHAMBER=[chamber_temperature]
```

Filament profile custom G-code (added):
```
SET_PRESSURE_ADVANCE ADVANCE=0.030
```
![After PA](/troubleshooting/images/PA_PLA/2.png)

### 3. Checked `PRINT_START` macro for conflicts

Before trusting the above, checked my `PRINT_START` macro in `printer.cfg` for any hardcoded `SET_PRESSURE_ADVANCE`, `pressure_advance`, or `ADVANCE=` references that could silently override the slicer's value (e.g. a reset after a purge line).

**Result: clean.** My `PRINT_START` handles heating, homing, QGL, bed mesh, and a purge line — no PA references anywhere in it. Confirmed the slicer's filament G-code line runs safely after `PRINT_START completes` with nothing downstream overwriting it.

> 📝 Note: `PRINT_START` doesn't accept a `CHAMBER` param even though the slicer's printer custom G-code passes one — harmless (Klipper macros silently ignore unused params), just noted as slicer G-code that could be cleaned up later.

### 4. Verifying PA is actually applied

Confirmed via the **web UI** (Mainsail/Fluidd) rather than the console, since there's no plain `PRESSURE_ADVANCE` query command in Klipper (learned this the hard way — it returns `unknown command`).

- Mainsail/Fluidd → Extruder panel → expand the settings/gear icon → live **Pressure Advance** field shows the currently active value in real time.
- Confirmed it matched `0.030` from the Ellis tower, and that it reflected properly after `PRINT_START complete` rather than being a stale config default.

### 5. Retraction speed

- Location: Filament Settings → Advanced → Retraction speed
- Was: `35 mm/s` — slow for a direct-drive Klipper toolhead
- Changed to: `45 mm/s` (first pass)
- **Result: noticeable improvement**, though some fine wisps remained.

![After Retraction Speed Change](/troubleshooting/images/PA_PLA/3.png)
![After Retraction Speed Change](/troubleshooting/images/PA_PLA/3_2.png)

### 6. Retraction length 

- Location: Filament Settings → Advanced → Retraction length
- Was: `0.5mm`
- Changed to: `0.7mm`
- Watch for: tiny under-extrusion gaps right after each retraction if pushed too far (back off toward 0.5–0.7mm range if seen).

### 7. Seam position

- Location: Print Settings → Seam → Seam position
- Was: `Rear`
- Changed to: `Nearest` (scarf seam is also an option on PrusaSlicer 2.7+, feathers the seam into a slope instead of a hard vertical stack — worth trying if artifacts persist)
- Zero-cost change, no calibration print needed — applied alongside the retraction length test.
- Confirmed set to `Nearest`.

This is after the retraction length change and seam change:
> 📷 ![After Retraction Length Change](/troubleshooting/images/PA_PLA/4.png)

### 8. Nozzle temp (queued, not yet tested)

- PETG was running 235°C / 240°C first layer — on the hot end for PETG, especially given Voron's typical Dragon/Rapido-style hotends vs. Prusa's nextruder.
- Plan: test a temperature tower, or step down manually (235 → 230 → 225°C), watching for the stringing/adhesion tradeoff.

### 9. Filament drying (queued, PETG-specific)

- PETG is hygroscopic — wet filament strings and pops regardless of slicer settings.
- Plan: dry at 65°C for 4–6 hours before the PETG retest, done in parallel with settings changes rather than as a sequential step, so it doesn't muddy results.

### 10. Coasting (in reserve, not yet needed)

- If wisps persist after retraction length + seam changes: Filament Settings → Advanced → Coasting, small value (0.1–0.2mm). Stops extrusion slightly before the end of a perimeter, letting residual pressure finish the line instead of pushing fresh plastic — same underlying mechanism as PA, applied at the end of a line specifically.

## Status For PLA

- [x] PA tuned and applied for PLA (`0.030`)
- [x] Confirmed `PRINT_START` macro has no PA conflicts
- [x] Confirmed PA is live via Mainsail/Fluidd
- [x] Retraction speed increased (35 → 45 mm/s) — improvement seen
- [x] Retraction length test (0.5 → 0.7mm)
- [x] Seam position change (Rear → Nearest)

## Status For PETG      
- [ ] PETG: PA tuning
- [ ] PETG: temp tower / temp step-down test
- [ ] PETG: filament drying
- [ ] Coasting, if needed

Other testing/fixes - see Prusa print (red, shmood) vs Voron print (yellow, rough)

> 📷 ![After Retraction Length Change](/troubleshooting/images/PA_PLA/OuterExample1.png)
> 📷 ![After Retraction Length Change](/troubleshooting/images/PA_PLA/OuterExample2.png)


## References

- [Andrew Ellis' Pressure Advance Calibration Tool](https://ellis3dp.com/Print-Tuning-Guide/articles/precise_pressure_advance.html)
- [Klipper TUNING_TOWER documentation](https://www.klipper3d.org/G-Codes.html#tuning_tower)
- Formbot Voron 2.4 350mm kit — `printer.cfg` custom `PRINT_START` macro
