# ICARUS 1U Solar Panel PCB — KiCad design-review package v0.2

This repository contains a **professional design-review draft** of a 1U body-mounted solar-panel PCB for the Porto Space Team / ICARUS CubeSat EPS workstream.

## Scope
The board is designed around:
- **2 × TJ3xSCA-4080 / TJ30SCA-4080 CIC solar cells**
- **Outward-facing tab orientation**
- **2-cell series string topology**
- **Panel-output connector area**
- **Backside routing and test access**

This is **not yet a fabrication release**. It is a review-ready engineering baseline to discuss with the team lead and to converge on the final connector/harness and vendor-specific cell-contact details.

## Repository layout
```text
hardware/
  ICARUS_1U_Solar_Panel_PCB_v0p2.kicad_pcb
  ICARUS_1U_Solar_Panel_PCB_v0p2.kicad_pro
  footprints/
    ICARUS_Solar.pretty/
      TJ3xSCA_4080_CIC.kicad_mod
    ICARUS_Connector.pretty/
      SOLAR_OUT_4P_1p25_PLACEHOLDER.kicad_mod

docs/
  DESIGN_REVIEW_NOTES.md
```

## Design summary

### Mechanical baseline
- Carrier PCB envelope: **95.0 mm × 95.0 mm**
- Solar-cell body reference: **80.15 mm × 40.15 mm**
- Two cells stacked vertically with outward tabs
- Four provisional M3-clearance mounting holes
- Top-side silkscreen / fab information intended for design review

The board envelope and mounting holes must still be aligned with the final ICARUS structure mechanical ICD before release.

### Electrical baseline
Named nets:
- `PANEL_POS`
- `PANEL_NEG`
- `SERIES_MID`

Intended string:
1. Upper cell and lower cell form a **2S string**
2. The design routes the string to a panel-output connector area
3. Backside test pads are provided for first electrical bring-up

### Cell-contact assumption
The footprint uses a **review assumption** for CIC assembly:
- outward tab side represented by tab-capture pads
- rear-contact pads represented beneath the cell body

This must be checked against the exact supplier assembly guide / contact polarity drawing before fabrication.

### Connector status
The present connector footprint is deliberately labelled as a **placeholder**:
- `SOLAR_OUT_4P_1p25_PLACEHOLDER`

Reason: the EPS documentation identifies the solar input family but does **not** provide enough panel-side harness/pinout detail in the material currently available to lock the panel connector with zero ambiguity.

## Key engineering open items before fabrication
1. Confirm **panel-side connector family, orientation and exact pinout**
2. Confirm **cell front/rear contact polarity and assembly method** with the supplier
3. Confirm **final PCB outline, mounting-hole coordinates and any face-specific cut-outs**
4. Confirm whether the selected EPS accepts the **cold-case open-circuit voltage** of the 2S string with sufficient margin
5. Run ERC/DRC and mechanical interference checks in the team’s final KiCad environment

## What is already solid enough for review
- Cell placement concept
- Outward tab orientation
- 2S string architecture
- Board zoning and clear communication of electrical intent
- Connector placeholder philosophy instead of guessing a production footprint

## Revision note
- **v0.2** — repository packaging and presentation cleanup for team review; same engineering concept as the initial draft, with clearer structure and documentation.
