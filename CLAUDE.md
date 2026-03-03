# SimHub Dashboard - Fuzzwah v2

## Project Overview

This is a custom SimHub Dash Studio dashboard for iRacing. SimHub is a racing telemetry application that displays real-time data to the driver. Dash Studio is SimHub's WYSIWYG editor for creating dashboard layouts, which generates `.djson` configuration files.

SimHub Dash Studio docs: https://github.com/SHWotever/SimHub/wiki/Dash-Studio

## Architecture

The dashboard is a **composite layout** at 960x600 resolution. The main file `Fuzzwah v2.djson` acts as the shell that embeds other `.djson` files as widgets:

- **Main shell** (`Fuzzwah v2.djson`) — has Idle and Dashboard screens, embeds all other components
- **Left panels** (`Left Page - *.djson`) — car-specific info panels (ABS, TC, ARB, etc.), swapped based on which car is being driven
- **Right panel** (`Right Page - iRacing.djson`) — standings/session info
- **Overlays** — flags, warnings, lap times, fuel alerts, engine warnings
- **Widgets** — RPM bar, deploy bar (DRS/ERS)

## File Types

| Extension | Purpose |
|-----------|---------|
| `.djson` | Dashboard layout definitions (JSON). The primary files in this project. |
| `.djson.ressources` | Binary resource packages (images/assets embedded in dashboards) |
| `.djson.metadata` | Dashboard metadata (resolution, screen count, SimHub version) |
| `.djson.png` | Preview screenshots |
| `.carclasses` | Car class filter definitions for widgets |

## File Naming Conventions

- Main components use **Title Case**: `Left Page - GT3.djson`, `Yellow Flag.djson`
- Some newer files use **lowercase**: `left page - lmp2.djson`, `left page - w12.djson`
- Car-specific left panels are named by car/series abbreviation: `GT3`, `GTE`, `V8SC`, `FW31`, `IR18`, `919`, `FR35`, `LMP2`, `W12`, `IR01`, `SF`, `IL15`

## DJSON Format

These are JSON files with two known schema variants:
1. **Newer format** (used in main shell): flat arrays for Screens/Items, `$type` annotations on items
2. **Older format** (used in sub-dashboards): `$id`/`$values` wrapper objects for arrays, `$ref` for references

Key item types (under `$type`):
- `TextItem` — text display bound to telemetry data
- `ImageItem` — static/dynamic images
- `RectangleItem` — colored rectangles, often used as bars/indicators
- `Layer` — grouping container for other items
- `WidgetItem` — embeds another `.djson` file as a sub-dashboard

Items use **NCalc expressions** for dynamic behavior — data bindings, visibility conditions, color changes. These appear in `Expression` fields throughout the files.

## Key SimHub Properties Used

Common telemetry bindings referenced in expressions:
- `[DataCorePlugin.GameData.NewData.CarId]` — current car identifier
- `[DataCorePlugin.GameData.NewData.Rpms]` — engine RPM
- `[IRacingExtraProperties.iRacing_DriverCarSLShiftRPM]` — shift point RPM
- `[DataCorePlugin.GameData.NewData.Flag_*]` — flag states
- `[DataCorePlugin.GameData.NewData.BrakeBias]` — brake bias
- `[DataCorePlugin.GameData.NewData.ABSLevel]` — ABS level
- `[DataCorePlugin.GameData.NewData.TCLevel]` — traction control

## Fonts

Custom fonts are stored in `_SHFonts/` and must be installed in SimHub:
- **Fira Code** (Light, Regular, Medium, Bold) — primary telemetry display font
- **B612** — cockpit-optimized font (designed for aircraft displays)
- **digital-7** — seven-segment display style numbers
- **DroidSans** — UI text
- **Prezident** — decorative/header use

## Working with This Project

- **Editing dashboards**: Use SimHub's Dash Studio editor — it's a visual designer, not meant for hand-editing
- **Hand-editing djson**: Possible but fragile. The JSON contains precise pixel positions, unique `$id` references, and binary resource links. Small edits to expressions or text values are safe; structural changes should be done in Dash Studio
- **Safe to hand-edit**: NCalc expressions, text content, color hex values, visibility conditions, font sizes
- **Avoid hand-editing**: Item structure/hierarchy, `$id`/`$ref` references, widget embedding, resource references
- **Adding a new car**: Create a new `Left Page - <CarName>.djson` in Dash Studio, then update the car selection logic in the main dashboard to reference it
- **Git**: `_Backups/` directory is gitignored (SimHub auto-backup folder)
