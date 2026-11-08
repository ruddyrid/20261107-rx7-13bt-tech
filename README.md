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
- Boost figures are factory wastegate settings and vary with altitude, intake temperature, and ECU calibration.
- Torque values and clearances are nominal 13B ranges collected from factory and Atkins Rotary reference material. Always verify against the Mazda factory service manual for your model year before assembly.
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

Links to the primary references are included at the bottom of the page, including the Atkins Rotary 13B spec sheet, Haltech's rotary tuning reference, RX7.org specifications, the RX7Club fluid guide, and Mazda's rotary technology background.
