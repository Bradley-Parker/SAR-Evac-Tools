# SAR Evac — CalTopo Bridge

Browser-based tools for SAR evacuation workflows with [CalTopo](https://caltopo.com/):

1. **Residences CSV → GPX** — import structure locations into CalTopo as waypoints
2. **CalTopo GeoJSON → status CSV** — export marker statuses back to a spreadsheet

Everything runs in the browser. No server, no uploads — your data stays on your machine.

## Use online

Open the live app in any browser:

**https://bradley-parker.github.io/SAR-Evac-Tools/**

Share that link with your team — no download required.

## Use offline

No install and no server — just grab the file and open it:

1. Download [`sar-evac-bridge.html`](https://github.com/Bradley-Parker/SAR-Evac-Tools/raw/main/sar-evac-bridge.html) (or use **Code → Download ZIP** and extract it)
2. Double-click the file, or drag it into Chrome, Firefox, Edge, or Safari

Everything runs in that one HTML file. It works fully offline after download.

## Privacy

All processing happens in JavaScript in your browser. Files are read locally and never sent to a server.

## Development

This repo is a single self-contained HTML file (`sar-evac-bridge.html`) plus a GitHub Pages copy at `index.html`.

### GitHub Pages setup

1. Open [repo Settings → Pages](https://github.com/Bradley-Parker/SAR-Evac-Tools/settings/pages)
2. Under **Build and deployment** → **Source**, choose **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)**
4. Click **Save**

The site is usually live within a minute or two after the first push.

### Making changes

Edit `sar-evac-bridge.html`, then copy it to `index.html` before pushing so the live site stays in sync:

```powershell
Copy-Item sar-evac-bridge.html index.html
```
