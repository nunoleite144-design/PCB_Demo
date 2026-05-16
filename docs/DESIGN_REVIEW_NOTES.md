# ICARUS 1U Solar Panel PCB v0.2 — Design Review Notes

## Purpose
This note explains the engineering intent of the current KiCad package and separates what is already reviewable from what still requires source-controlled confirmation before fabrication.

## Current design intent
The board is a **1U solar-panel carrier PCB** for one CubeSat face, built around:
- **2 × TJ3xSCA-4080 / TJ30SCA-4080 CIC solar cells**
- **outward-facing cell tabs**
- **one 2-cell series string**
- **panel-output connector region**
- **routing and test access sufficient for design discussion**

## Why outward-facing tabs
The orientation keeps the external tab side of each cell at the outer edge of the panel and avoids crowding the centerline between the cells. For a connector-only panel PCB, this is the cleanest review baseline and keeps the layout visually aligned with common commercial / academic body-mounted solar-panel practice.

## Mechanical assumptions used
- PCB carrier envelope: **95.0 mm × 95.0 mm**
- Cell body reference: **80.15 mm × 40.15 mm**
- Two cells stacked vertically
- Four provisional M3-clearance holes

These choices are suitable for **design review**, but the final outline and hole positions must be checked against the ICARUS structure ICD and the actual side-face constraints of the CubeSat.

## Electrical assumptions used
Named nets:
- `PANEL_POS`
- `PANEL_NEG`
- `SERIES_MID`

The intent is a **2S topology** with a panel-output connector region placed near the lower edge of the board. The board visually communicates:
- cell orientation,
- series connection intent,
- positive / negative panel rails,
- the fact that the connector footprint is not yet frozen.

## Critical items still open
### 1. Cell interface confirmation
The available solar-cell datasheet confirms the overall CIC dimensions and shows the external interconnector geometry, but it does not fully remove ambiguity about the exact assembly/contact implementation to use on the PCB side. Before fabrication, the team should obtain or confirm:
- exact cell contact polarity,
- exact top/bottom contact mapping,
- recommended solder / conductive epoxy landing geometry,
- any supplier assembly notes for board-side pads and tolerances.

### 2. Connector / harness confirmation
The current connector footprint is intentionally a **placeholder**. The final release should use the actual panel-side connector family, orientation and pin mapping once confirmed against the EPS harness strategy.

### 3. EPS input-voltage margin
With two cells in series, the panel operating concept should be checked against the EPS solar-input voltage limit under cold-case open-circuit conditions. This is a system-level validation item and should be explicitly closed before freezing the hardware.

### 4. Final PCB envelope
The 95 × 95 mm board outline is a clean review baseline, not a structure-locked mechanical release. The final shape should be updated once the mechanical team confirms:
- available panel area,
- rail keep-outs,
- connector-access requirements,
- any face-specific cutouts or constraints.

## What is already ready to show
This repo is ready to show as a **professional design-review package** because it provides:
- a clear KiCad board file,
- structured footprints,
- documented assumptions,
- explicit open items,
- an engineering rationale instead of unsupported guesses.

## Recommended wording in a team review
> “This is the first structured KiCad baseline for the 1U solar-panel PCB: two TJ3xSCA-4080 cells in a 2S arrangement, tabs outward, with the board topology and routing concept already laid out. I kept the connector and cell-contact details explicitly marked as to-be-confirmed before fabrication rather than freezing an unsafe assumption.”
