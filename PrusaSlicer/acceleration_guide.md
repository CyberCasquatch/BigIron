# Slicer Acceleration Settings Guide

## Overview
Acceleration settings in PrusaSlicer override Klipper's defaults for specific move types. They must not exceed your Klipper `max_accel` value — if they do, Klipper silently caps them.

Big Iron `max_accel`: 3000 mm/s²

## General Principles

- **Lower acceleration** = more reliable, less ringing, better quality, slower prints
- **Higher acceleration** = faster prints, more ringing risk, more step loss risk
- Travel acceleration matters most for layer shifting — this is the move most likely to cause a step loss at a geometry transition
- External perimeter acceleration most affects surface quality and ringing artefacts
- Always set External perimeters acceleration explicitly — leaving it at 0 means it inherits Default which may be too high

## Settings by Filament Type

### PLA
| Setting | Conservative | Aggressive |
|---------|-------------|------------|
| External perimeters | 1000 | 1500 |
| Perimeters | 2000 | 3000 |
| Infill | 3000 | 3000 |
| Travel | 1500 | 2500 |
| Default | 2000 | 3000 |

### PETG
Print slower than PLA — PETG strings badly at high speeds.
| Setting | Value |
|---------|-------|
| External perimeters | 800 |
| Perimeters | 1500 |
| Infill | 2000 |
| Travel | 1500 |
| Default | 1500 |

### ABS / ASA
Similar to PLA but chamber temp management matters more than speed.
| Setting | Value |
|---------|-------|
| External perimeters | 1000 |
| Perimeters | 2000 |
| Infill | 3000 |
| Travel | 1500 |
| Default | 2000 |

> For ABS/ASA: update PRINT_START slicer gcode to pass CHAMBER parameter and increase heat soak time (300 seconds minimum).

## Travel Speed
Travel speed is separate from travel acceleration.

| Scenario | Recommended Travel Speed |
|----------|--------------------------|
| Troubleshooting / baseline | 150 mm/s |
| Stable known-good setup | 200-250 mm/s |
| Maximum (Voron 2.4 350) | 300 mm/s |

Reduce travel speed first if you see layer shifts at specific geometry transitions.

## Printer Type Comparison

| Printer Type | Max Practical Accel | Notes |
|-------------|--------------------|----|
| Voron 2.4 (CoreXY) | 3000-5000 mm/s² | With input shaper tuned |
| Voron Trident (CoreXY) | 3000-5000 mm/s² | Similar to 2.4 |
| Prusa MK4 (bed slinger) | 1000-2000 mm/s² | Bed mass limits Y accel |
| Ender 3 (bed slinger) | 500-1000 mm/s² | Very limited by bed mass |
| Bambu X1 (CoreXY) | 10000+ mm/s² | Built-in vibration compensation |

Bed slinger printers (bed moves in Y) need much lower Y acceleration than X — the moving bed mass amplifies Y resonance significantly.

## After Changing Accelerations
Always run `SHAPER_CALIBRATE` after significant hardware changes. Input shaper results directly affect what accelerations are safe — the recommended max_accel from shaper results is the ceiling, not a target.
