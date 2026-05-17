# Manta M8P Controller Cooling

## Why This Matters
TMC2209 drivers on the Manta M8P run hot at motor currents above 0.8A. Without active cooling directly over the driver chips, the OTPW (over temperature pre-warning) flag triggers and the printer becomes unreliable.

Skirt fans 10cm away are NOT sufficient. Drivers need direct airflow.

## Hardware Used

### Fan Mount
**M8P Pavan** by bud3d — DIN rail mountable, no need to remove board from DIN rails.
- Two 5010 fans for stepper drivers
- One 4010 fan for CB1
- GitHub: https://github.com/bud3d/m8p-Pavan

![M8P Pavan installed](images/m8pPavan.jpeg)

Chose this over other mounts specifically because it doesn't require pulling the board off the DIN rails.

### Fans
- 2x 5010 24V fans connected to PF7 and PA4 on Manta M8P

## Klipper Config

```ini
## MCU temperature-controlled fan - stepper driver cooling
[temperature_fan MCU]
pin: PF7
sensor_type: temperature_mcu
sensor_mcu: mcu
min_temp: 0
max_temp: 85
target_temp: 45.0
min_speed: 0.2
max_speed: 1.0
control: watermark
kick_start_time: 0.5

## Second controller fan - runs when steppers active
[controller_fan controller_fan_2]
pin: PA4
kick_start_time: 0.5
fan_speed: 1.0
idle_timeout: 60
stepper: stepper_x, stepper_y, stepper_z
```

> ⚠️ You cannot have both `[temperature_sensor MCU]` and `[temperature_fan MCU]` active simultaneously — they conflict on the same pin. Remove or comment out `[temperature_sensor MCU]` when using `temperature_fan`.

## Motor Current With Cooling

| Cooling | Safe run_current |
|---------|-----------------|
| No active cooling | 0.8-0.9A |
| Active cooling (fans above board) | 1.0-1.1A |

Monitor MCU temperature in Mainsail. Target: under 60°C during printing.

## OTPW Warning
If you see this in Mainsail:
```
The stepper driver 'stepper_y' has triggered the OTPW flag
```
Immediately reduce `run_current` by 0.1A and check cooling. The driver will throttle itself before full shutdown but inconsistent current causes layer shifting.
