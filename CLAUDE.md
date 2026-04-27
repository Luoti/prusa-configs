# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal PrusaSlicer config bundle for a Voron V2 350mm printer running Klipper. The entire config lives in a single file (`Prusa_slicer_config.ini`) imported into PrusaSlicer via *File → Import → Import Config Bundle*.

## File structure

`Prusa_slicer_config.ini` contains four types of sections in order:

1. **`[print:*]`** — print profiles (speeds, accelerations, layer heights, extrusion widths)
2. **`[filament:*]`** — filament profiles (temperatures, fan speeds, pressure advance)
3. **`[printer:*]`** — printer hardware profiles (machine limits, start/end gcode)
4. **`[physical_printer:*]`** — network connection to the printer (IP, API key)

## Inheritance model

PrusaSlicer supports profile inheritance. Base/abstract profiles use `*asterisk*` names (e.g. `*Base filament*`, `*Voron_v2_350 master*`) and child profiles declare `inherits = ParentName`. Child profiles only need to override values that differ from the parent.

The print profile hierarchy:
- `Ellis_PIF_Standard_22mm3` — base 0.4mm nozzle profile
  - `Ellis_PIF_Standard_22mm3_detail_016mm` — 0.16mm layer height variant
  - `Ellis_PIF_Standard_22mm3_06mm` — 0.6mm nozzle variant
    - `Ellis_PIF_Standard_22mm3_06mm_TPU` — TPU for 0.6mm nozzle
  - `Ellis_PIF_Standard_22mm3_04mm_TPU` — TPU for 0.4mm nozzle
  - `Ellis_PIF_Decorative_22mm3` — decorative (lightning infill, fewer perimeters)
    - `Ellis_PIF_Decorative_22mm3_06mm` / `_08mm` — nozzle size variants

## Key conventions

- **Extrusion widths**: comments after values explain the percentage (e.g. `0.46 # 230 percent of layer height`).
- **Pressure advance** is set in `start_filament_gcode` conditionally per nozzle diameter: `{if nozzle_diameter[0]==0.4}...{elsif nozzle_diameter[0]==0.6}...{endif}`.
- **TPU profiles** are filtered via `compatible_prints_condition = notes=~/TPU/`; non-TPU profiles exclude TPU via `! (notes=~/TPU/)`.
- **Network config**: printer IP and API key live in `[physical_printer:Voron]` (`print_host`, `printhost_apikey`).
- The printer section uses `gcode_flavor = klipper` and `autoemit_temperature_commands = 0` — temperatures are managed entirely by the start gcode macros (`PRINT_START`, `MMU_START_*`).
