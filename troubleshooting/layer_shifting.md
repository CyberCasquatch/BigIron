# Layer Shifting Troubleshooting

## Symptoms
- Chaotic or consistent X-axis layer shifts
- Shifts occurring at random heights OR at specific repeatable heights

## Diagnosis Checklist

Work through these in order before changing config.

- [ ] A/B belt tension — target ~110 Hz when plucked (use Gates Carbon Drive app)
- [ ] Grub screws on all motor pulleys
- [ ] Z belt tension
- [ ] X rail smooth movement — disable motors with `M84` and slide toolhead manually across full X range. Should glide with no resistance or tight spots
- [ ] CAN bus errors — run `ip -s link show can0` in SSH. Any non-zero RX/TX errors are a red flag
- [ ] Input shaper — run `SHAPER_CALIBRATE` if not recently done. Results saved to `[input_shaper]` in printer.cfg

## Common Causes and Fixes

### Motor current too low
Symptoms: chaotic shifting, worse at higher speeds or accelerations

Check motor model number. For Moons MS17HD6P420I-04 (2.0A rated motors):
- Default 0.8A = 40% of rated — too conservative
- Recommended: 1.0A with active board cooling, 0.9A without

```ini
[tmc2209 stepper_x]
run_current: 1.0

[tmc2209 stepper_y]
run_current: 1.0
```

> ⚠️ Increasing motor current increases driver heat. See [manta_cooling.md](manta_cooling.md) before going above 0.9A.

### Slicer acceleration too high
Symptoms: shift at same height every print, clean line all the way around

If shift is at a repeatable height, check gcode preview in PrusaSlicer at that layer. Look for geometry transitions or fast travel moves.

Working PLA acceleration settings (conservative/stable):
- Travel speed: 150 mm/s
- Travel acceleration: 1500 mm/s²
- Infill acceleration: 3000 mm/s²
- External perimeters acceleration: 1000 mm/s²
- Perimeters acceleration: 2000 mm/s²

See [slicer_config_pla.md](../slicer/slicer_config_pla.md) for full profile.

### Input shaper not applied
Run `SHAPER_CALIBRATE` and save results. Without this, resonance causes unpredictable shifting especially at direction changes.

Big Iron results (May 2026):
- X: 3hump_ei at 92.8 Hz (unusually high — monitor)
- Y: ei at 42.6 Hz
- Safe max_accel: 3000 mm/s²

### Stealthchop enabled
Should be disabled on all motors for Voron. Verify:
```ini
stealthchop_threshold: 0
```

## Notes
- Pure X-axis shifting on CoreXY points to both A and B motors losing steps simultaneously
- X resonance at 92.8 Hz is abnormally high for a 350mm build (normal is 40-60 Hz). Monitor for changes after hardware adjustments
- After fixing motor current, re-run input shaper as the resonance profile may change
