# PrusaSlicer PLA Profile — Big Iron (Voron 2.4 350mm)

Last updated: May 2026  
Slicer version: PrusaSlicer  
Printer: Voron 2.4r2 Pro+ 350mm (Formbot kit)

## Print Settings

### Speeds
| Setting | Value |
|---------|-------|
| Perimeters | 100 mm/s |
| Small perimeters | 40 mm/s |
| External perimeters | 40 mm/s |
| Infill | 125 mm/s |
| Solid infill | 120 mm/s |
| Top solid infill | 50 mm/s |
| Bridges | 60 mm/s |
| Gap fill | 40 mm/s |
| Travel | 150 mm/s |
| First layer speed | 30 mm/s |

> Note: Travel reduced from 300 to 150 mm/s to prevent layer shifting at geometry transitions.

### Acceleration
| Setting | Value |
|---------|-------|
| External perimeters | 1000 mm/s² |
| Perimeters | 2000 mm/s² |
| Infill | 3000 mm/s² |
| Travel | 1500 mm/s² |
| First layer | 1000 mm/s² |
| Default | 2000 mm/s² |

> Note: These are conservative stable values. Max Klipper accel is 3000 mm/s². Infill was previously 4000 which exceeded Klipper's limit.

### Output
| Setting | Value |
|---------|-------|
| Label objects | Firmware-specific |
| Verbose G-code | Off |

> Label objects must be Firmware-specific for Klipper adaptive bed mesh to work.

## Filament Settings (Generic PLA)
| Setting | Value |
|---------|-------|
| Nozzle temp | 215°C |
| Bed temp | 60°C |
| First layer nozzle | 220°C |
| First layer bed | 60°C |
| Cooling | Enabled |
| Min fan speed | 30% |
| Max fan speed | 100% |

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
- Seam position: set to Rear or Nearest — avoid Aligned which stacks seams and creates weak points
- Adaptive bed mesh requires `[exclude_object]` in printer.cfg and Label objects set to Firmware-specific
- These settings work well for standard PLA. For PLA+, increase nozzle temp by 5-10°C
