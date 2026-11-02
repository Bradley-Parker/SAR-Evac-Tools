# SAR Evac — CalTopo Bridge

Browser-based tools for SAR evacuation workflows with [CalTopo](https://caltopo.com/):

1. **Residences CSV → CalTopo GeoJSON / GPX** — import structure locations into CalTopo as waypoints, with optional grouping by street name.
2. **CalTopo GeoJSON → Triage Dashboard & Status CSV** — track field evacuation statuses, calculate operational metrics, and reconcile unvisited structures.

Everything runs in the browser. No server, no uploads — your data stays on your machine.

## Use online

Open the live app in any browser:

**https://bradley-parker.github.io/SAR-Evac-Tools/sar-evac-bridge.html**

Share that link with your team — no download required.

## Use offline

No install and no server — just grab the file and open it:

1. Download [`sar-evac-bridge.html`](https://github.com/Bradley-Parker/SAR-Evac-Tools/raw/main/sar-evac-bridge.html) (or use **Code → Download ZIP** and extract it)
2. Double-click the file, or drag it into Chrome, Firefox, Edge, or Safari

Everything runs in that one HTML file. It works fully offline after download.

## Privacy

All processing happens in JavaScript in your browser. Files are read locally and never sent to a server.

## Development

This repository contains:
- `sar-evac-bridge.html` — The complete, self-contained standalone application.
- `index.html` — A lightweight redirect file routing GitHub Pages visitors directly to `sar-evac-bridge.html`.

All functional updates are made directly in `sar-evac-bridge.html`.
