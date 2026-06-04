# PrusaSlicer PETG Profile — Big Iron (Voron 2.4 350mm)

Last updated: June 2026  
Slicer version: PrusaSlicer  
Printer: Voron 2.4r2 Pro+ 350mm (Formbot kit)

## Print Settings

### Speeds

| Setting             | Value    |
| ------------------- | -------- |
| Perimeters          | 80 mm/s  |
| Small perimeters    | 30 mm/s  |
| External perimeters | 35 mm/s  |
| Infill              | 100 mm/s |
| Solid infill        | 80 mm/s  |
| Top solid infill    | 40 mm/s  |
| Bridges             | 40 mm/s  |
| Gap fill            | 30 mm/s  |
| Travel              | 150 mm/s |
| First layer speed   | 25 mm/s  |

> Note: PETG is more viscous and stringy than PLA — slower speeds reduce stringing and improve layer adhesion. Bridge speed in particular should be kept low to prevent sagging.

### Acceleration

| Setting             | Value      |
| ------------------- | ---------- |
| External perimeters | 800 mm/s²  |
| Perimeters          | 1500 mm/s² |
| Infill              | 2500 mm/s² |
| Travel              | 1500 mm/s² |
| First layer         | 800 mm/s²  |
| Default             | 1500 mm/s² |

> Note: Conservative accels for PETG — the material needs time to bond between layers. Pushing accels too high tends to cause ringing artifacts in PETG more than PLA.

### Output

| Setting        | Value             |
| -------------- | ----------------- |
| Label objects  | Firmware-specific |
| Verbose G-code | Off               |

> Label objects must be Firmware-specific for Klipper adaptive bed mesh to work.

## Filament Settings (Generic PETG)

| Setting            | Value    |
| ------------------ | -------- |
| Nozzle temp        | 235°C    |
| Bed temp           | 85°C     |
| First layer nozzle | 240°C    |
| First layer bed    | 90°C     |
| Cooling            | Enabled  |
| Min fan speed      | 20%      |
| Max fan speed      | 50%      |

> Note: PETG needs significantly less cooling than PLA — too much fan will cause delamination and poor layer bonding. Keep max fan at 50% or lower. The higher bed temp (85–90°C) is important for good first layer adhesion.

## Retraction Settings

| Setting              | Value   |
| -------------------- | ------- |
| Retraction length    | 0.5 mm  |
| Retraction speed     | 35 mm/s |
| Extra restart length | 0 mm    |

> Note: PETG strings more than PLA but is also sensitive to over-retraction (can cause blobs and clogs). Keep retraction short — 0.5 mm is a good starting point for a direct drive setup. Do **not** go above 1 mm.

## Other Settings

| Setting               | Value    |
| --------------------- | -------- |
| Seam position         | Rear     |
| Z offset (vs PLA)     | +0.02 mm |
| Elephant foot comp.   | 0.1 mm   |

> Note: PETG squishes more on the first layer than PLA, so you may need to raise your Z offset slightly vs your PLA setting to avoid the nozzle dragging.

## Printer G-code

### Start G-code

```
PRINT_START EXTRUDER=[first_layer_temperature[initial_tool]] BED=[first_layer_bed_temperature] CHAMBER=[chamber_temperature]
```

### End G-code

```
PRINT_END
```

## Notes

- **Enclosure:** PETG generally does NOT need an enclosed chamber — in fact, a fully sealed hot enclosure can cause over-heating and stringing. Crack the door or leave panels off if temps climb above ~40°C inside.
- **Bed surface:** PETG sticks *very* aggressively to PEI — use a PEI textured sheet and make sure your Z offset isn't too squished, or prints can be very hard to remove. A very light wipe with IPA before printing helps prevent over-adhesion.
- **Moisture:** PETG is hygroscopic. If you see lots of popping/bubbling or poor surface quality, dry your filament at 65°C for 4–6 hours before printing.
- **Stringing:** If you're getting fine strings, try increasing travel speed, enabling combing (set to "Within infill"), or nudging temp down by 5°C.
- Adaptive bed mesh requires `[exclude_object]` in printer.cfg and Label objects set to Firmware-specific.
