# Troubleshooting & Configuration Notes

Documentation of issues encountered, fixes applied, and configuration decisions for Big Iron (Voron 2.4r2 Pro+ 350mm).

## Troubleshooting Guides

| Guide | Description |
|-------|-------------|
| [Layer Shifting](layer_shifting.md) | Diagnosing and fixing X/Y layer shifts — belts, motor current, slicer settings |
| [Manta M8P Cooling](manta_cooling.md) | Controller board cooling, fan mount, config for temperature-controlled fans |
| [Belt Tensioning](belt_tensioning.md) | How to measure and set belt tension, target frequencies |

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
