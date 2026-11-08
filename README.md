# Mazda 13B Turbo — Series 4 & Series 5 RX-7 Technical Reference

A single-page technical reference for the 13B turbo twin-rotor used in the 1986–1991 Mazda RX-7 FC (FC3S) Turbo II.

**Live site:** https://ruddyrid.github.io/20261107-rx7-13bt-tech/

## What it covers

- **Series 4 vs Series 5** specifications: 8.5:1 vs 9.0:1 compression, 5.5 vs 7.5 psi stock boost, 182 vs 200 hp, turbo and oil-metering changes.
- **Power and torque** comparison with a representative power curve.
- **Blueprint geometry** for the 13B: 105 mm generating radius, 15 mm eccentricity, 80 mm rotor width, 654 cc per rotor, and the seal package.
- **Torque values** for the rotor stack, engine, turbo, driveline, and ignition, with click-to-copy values and SAE/metric switching.
- **Rebuild sequence** in eight stages, with a progress checklist, tension-bolt pattern, and stack order.
- **Reference** for leading/trailing ignition, spark plugs, fluid capacities, compression testing, and rotary-specific troubleshooting.

## Data notes

- Power and torque figures are US SAE net unless noted. JDM ratings were 185 PS (Series 4), 205 PS (Series 5), and 215 PS for the Infini IV.
- Boost figures are the commonly quoted factory wastegate settings and vary with altitude, intake temperature, and ECU calibration. The 1989 FSM lists a 57 kPa (8.25 psi) boost-pressure spec.
- **Series 4 turbo:** dual-scroll Hitachi with a vacuum-operated quick-spool (switching) valve and actuator. The FSM checks that the actuator rod starts moving above about 2,700 rpm.
- **Series 5 turbo:** twin-scroll Hitachi fed by a divided exhaust manifold that biases each rotor's pulses into its own scroll. Boost is trimmed by a wastegate and a duty-cycle solenoid; there is no switching valve.
- Torque values, clearances, idle speed, and fluid capacities are taken from the Mazda 1988 FSM (covers 1986–88) and 1989 FSM (covers 1989–91). Where the two manuals differ, the page shows a range spanning both. Always verify against the FSM for your model year before assembly.
- Fuel tank capacity changed with the series: 63 L / 16.6 US gal on the Series 4 and 70 L / 18.5 US gal on the Series 5.
- Idle speed is 725–775 rpm with the BAC valve.
- Compression testing follows the factory procedure: 85 psi minimum per face at ~250 rpm, with no more than 21 psi spread between chambers. A healthy engine typically reads 100–120 psi.
- The blueprint is a schematic, not a dimensioned drawing.

## Local preview

The page is a single static `index.html` with no build step:

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

## Deployment

GitHub Pages serves the `main` branch from the repository root. Push to `main` and the site rebuilds automatically.

## Sources

Primary references are the Mazda factory service manuals:

- [1988 RX-7 FSM — Technical Data](https://foxed.ca/rx7manual/manuals/1988FSM/30_TECHNICAL_DATA.pdf) (Series 4)
- [1988 RX-7 FSM — Turbo fuel and emission control](https://foxed.ca/rx7manual/manuals/1988FSM/04b_FUEL_AND_EMISSION_CONTROL_SYSTEMS_TURBO.pdf)
- [1989 RX-7 FSM — Technical Data](https://foxed.ca/rx7manual/manuals/1989FSM/30_Technical_Data.pdf) (Series 5)
- [1989 RX-7 FSM — Turbo fuel and emission control](https://foxed.ca/rx7manual/manuals/1989FSM/04B_Fuel_and_emission_control_systems_turbo.pdf)

Secondary references include the Atkins Rotary 13B spec sheet, Haltech's rotary tuning reference, RX7.org specifications, the RX7Club fluid guide, and Mazda's rotary technology background.
