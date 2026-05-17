# Belt Tensioning

## Tools
- Phone with Gates Carbon Drive app (free, iOS and Android)
- OR any frequency analyser app

## Target Tension
Voron 2.4 350mm build:
- A belt: ~110 Hz
- B belt: ~110 Hz

## How to Measure
1. Open Gates Carbon Drive app
2. Hold phone close to belt (within a few cm)
3. Pluck the belt like a guitar string on the longest straight section
4. Read the frequency

## How to Adjust
Belts are tensioned via the tensioner screws at the rear of the printer on each A/B motor mount.

- Clockwise = tighten = higher frequency
- Counter-clockwise = loosen = lower frequency

Adjust in small increments and re-measure after each adjustment.

## Notes
- Both belts should be as close to equal tension as possible
- Overtightened belts (significantly above 110 Hz) cause abnormally high X resonance frequency in input shaper results and can cause step losses under direction changes
- Under-tensioned belts cause inconsistent positioning and ringing artefacts
- After any belt adjustment, re-run `SHAPER_CALIBRATE` as resonance profile will change
- Re-check tension after first 20-30 hours of printing on a new build — belts stretch slightly during break-in

## Big Iron Belt History
| Date | A Belt | B Belt | Notes |
|------|--------|--------|-------|
| May 2026 | 110 Hz | 110 Hz | Checked during layer shift troubleshooting |
