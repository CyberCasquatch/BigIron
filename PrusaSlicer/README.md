# Slicer Configuration

PrusaSlicer profiles and reference settings for Big Iron.

## Profiles

| Profile | Description |
|---------|-------------|
| [PLA](slicer_config_pla.md) | Generic PLA — speeds, accelerations, temperatures, start/end gcode |
| [Acceleration Guide](acceleration_guide.md) | Acceleration settings by filament type and printer type |

## Slicer Start G-code

```
PRINT_START EXTRUDER=[first_layer_temperature[initial_tool]] BED=[first_layer_bed_temperature] CHAMBER=[chamber_temperature]
```

## Required Slicer Settings for Klipper
- **Label objects**: Firmware-specific (required for adaptive bed mesh and object exclusion)
- **Verbose G-code**: Off

## Notes
- Always save profiles with descriptive names: `Voron 2.4 - PLA 0.2mm`
- Conservative acceleration values are the safe baseline — tune up gradually once printing is stable
- Travel speed and travel acceleration are the first things to reduce when troubleshooting layer shifts
