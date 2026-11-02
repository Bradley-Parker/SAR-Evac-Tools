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

## Repo Layout

This repository contains:
- `sar-evac-bridge.html` — The complete, self-contained standalone application.
- `index.html` — A lightweight redirect file routing GitHub Pages visitors directly to `sar-evac-bridge.html`.

All functional updates are made directly in `sar-evac-bridge.html`.

## Background, Development, and Maintenance

The tool is designed to support real-world field operations during wildfire, flood, hurricane, and other emergency response events, where speed, accountability, and clear status tracking matter. It is offered publicly in hopes it may also help other responders and agencies working in similar situations.

AI is used to help draft and refine the JavaScript that powers the app’s functionality, including data handling, filtering, export logic, and interface behavior. The architecture, features, and design decisions are still led by humans with operational knowledge and user experience in mind. In other words, the human problem definition and workflow design come first; AI is a development aid rather than the source of the operational concept itself.

Feature requests and bug reports are welcome, but this project is maintained on a volunteer basis. There is no guarantee on response time, prioritization, or when or if a requested feature or bug fix will be addressed.

This tool is offered publicly in case it is useful to others working in evacuation or wellness-check contexts. Feature requests and bug reports are welcome, but this is a volunteer-maintained project with no guarantee of response time, prioritization, or when or if issues will be addressed.

All reasonable efforts are made to ensure the tool is accurate and useful, but no warranty is provided. The app is not guaranteed to be complete, error-free, or suitable for every operational situation. Users are responsible for validating outputs against local procedures, maps, and incident requirements before relying on them. Use is at your own risk.
