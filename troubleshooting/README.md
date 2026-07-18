# Troubleshooting & Configuration Notes
Documentation of issues encountered, fixes applied, and configuration decisions for Big Iron (Voron 2.4r2 Pro+ 350mm).
## Troubleshooting Guides
| Guide | Description |
|-------|-------------|
| [Layer Shifting](layer_shifting.md) | Diagnosing and fixing X/Y layer shifts — belts, motor current, slicer settings |
| [Manta M8P Cooling](manta_cooling.md) | Controller board cooling, fan mount, config for temperature-controlled fans |
| [Belt Tensioning](belt_tensioning.md) | How to measure and set belt tension, target frequencies |
| [Stringing and Artifacts](StringingandArtifacts.md) | Diagnosing PLA/PETG stringing and perimeter blobs vs. a Prusa MK4S baseline — pressure advance calibration, retraction, and seam settings |
## Quick Reference — What Fixed What
### May 2026 Layer Shifting Investigation
**Symptom:** Chaotic X-axis layer shifting on every print  
**Root causes found (in order of impact):**
1. Motor current too low (0.8A on 2.0A rated motors) → raised to 1.0A with active cooling
2. Slicer travel acceleration too high (3000 mm/s²) → reduced to 1500 mm/s²
3. Input shaper not producing optimal results at 0.8A motor current
4. Duplicate sensor definitions in printer.cfg causing config errors
**What was ruled out:**
- Belt tension (both correct at 110 Hz)
- Grub screws (all tight)
- CAN bus errors (zero errors confirmed)
- Tap probe mechanism (moving freely)
- Rail lubrication (recently done)

### July 2026 Stringing & Seam Artifact Investigation
**Symptom:** PETG (and PLA) stringing badly on the Voron while the same filament printed clean on a Prusa MK4S; small blob artifacts sticking out along perimeters/seams on both materials  
**Root causes found (in order of impact):**
1. Pressure advance never calibrated on Klipper (Prusa's firmware ships with Linear Advance tuned per filament out of the box; Klipper does nothing until manually tuned) → calibrated via Ellis' PA tool, PLA landed at `0.030`
2. Retraction speed too slow for a direct-drive setup (35 mm/s) → raised to 45 mm/s
3. Retraction length undersized for remaining wisps (0.5mm) → raised to 0.7mm
4. Seam position (`Rear`) stacking a visible blob at a fixed point every layer → changed to `Nearest`
**What was ruled out:**
- Filament itself — same spool printed clean on the Prusa MK4S
- `PRINT_START` macro conflicts — checked for hardcoded `SET_PRESSURE_ADVANCE`/`pressure_advance` overrides, found none
**Still open (PETG):**
- PA tuning value for PETG
- Nozzle temp step-down test (235°C → 230°C)
- Filament drying (65°C, 4–6 hrs)
