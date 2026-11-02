# AGENTS.md — Guidance, Constants & Architecture Invariants

This document defines the core principles, operational constants, architectural invariants, and UX guidelines for AI agents working on the **SAR Evac Tools** codebase.

---

## 1. Project Mission & Operational Context

* **Domain:** Search and Rescue (SAR) Evacuation Operations (wildfire, flood, hurricane, hazardous materials).
* **Primary End-Users:** SAR Managers, Incident Commanders, Operations/Planning Section Chiefs, and Field Team Leaders.
* **Operational Reality:** High-stress, rapidly evolving, time-critical incident command environments. Responders have varying degrees of technical literacy and may be operating in loud, mobile command vehicles on laptop screens with high glare or at night under red-light discipline.
* **Core Function:** Bridge local 911 dispatch / parcel GIS data and [CalTopo](https://caltopo.com/) mapping layers to track structure evacuation statuses and ensure 100% accountability of residents.

---

## 2. Immutable Architectural Invariants

Any agent contributing to this repository must preserve these constraints:

1. **Zero External Dependencies / 100% Pure Client-Side:**
   - Single self-contained HTML file (`sar-evac-bridge.html`).
   - Pure vanilla JavaScript, HTML5, and CSS.
   - **No external CDNs** (which fail completely when internet access is down at an Incident Command Post).
   - **No server backend** or build step required.
2. **Absolute Privacy / Zero Network Transmission:**
   - Resident names, phone numbers, gate codes, and medical notes must never leave the user's browser.
   - No analytics, telemetry, or remote tracking.
3. **Strict Manager Approval Gate (ADR-003):**
   - No functional code changes to `sar-evac-bridge.html` are permitted without prior review and explicit sign-off from the user/manager recorded in `PROPOSED_IMPROVEMENTS.md`.
4. **GitHub Pages Redirection:**
   - The primary standalone application file is `sar-evac-bridge.html`.
   - `index.html` serves as a permanent, zero-maintenance redirect to `sar-evac-bridge.html` so users visiting the GitHub Pages root URL are routed automatically without needing duplicate file copies.

---

## 3. System Constants & Specifications

### 3.1 Session Persistence & TTL Policy
* **Storage Engine:** Browser `localStorage`.
* **Storage Key:** `sar_evac_session_v1`.
* **Session TTL:** **48 Hours** (`48 * 60 * 60 * 1000 = 172,800,000 ms`).
  ```javascript
  const SESSION_TTL_MS = 48 * 60 * 60 * 1000; // 48 hours
  ```
* **Auto-Purge Rule:** On application launch, if `Date.now() - savedSession.timestamp > SESSION_TTL_MS`, all session data must be automatically wiped.
* **Manual Reset:** Must always provide an accessible, explicit **"Purge Incident Data"** button with a confirmation dialog.
* **Backup Reminder Standard:** The UI must display persistent guidance reminding operators to save local `.csv` and `.json` backup copies outside the browser.

### 3.2 Output Data Formats
* **Default Export:** **CalTopo-shaped GeoJSON** (FeatureCollection containing Point geometries with custom properties: `title`, `marker-symbol`, `marker-color`, `folderId`, `description`).
* **Secondary Export:** **GPX 1.1** (kept prominent for backwards compatibility with standalone handheld GPS units).
* **Tabular Exports:**
  - **"Export Visible / Filtered CSV":** Exports only the rows currently matching the active user filter/search.
  - **"Export All CSV":** Exports the entire dataset regardless of current filter.

### 3.3 Default CalTopo Evacuation Symbology Presets
The system must pre-load standard CalTopo default icons for immediate use:

```javascript
const DEFAULT_EVAC_PRESETS = [
  { symbol: "check", label: "Cleared / Evacuated", color: "#2e7d32", code: "CLEARED" },
  { symbol: "warning", label: "Needs Assistance (Urgent)", color: "#c62828", code: "ASSIST" },
  { symbol: "cancel", label: "Refused Evacuation", color: "#e65100", code: "REFUSED" },
  { symbol: "pause", label: "In Progress / Contacted", color: "#fbc02d", code: "IN_PROGRESS" },
  { symbol: "point", label: "Unchecked / Not Visited", color: "#1565c0", code: "UNCHECKED" }
];
```

* **Agency Portability:** Must support one-click **"Export Presets"** and **"Import Presets"** as a lightweight JSON file (`.json`) so agencies can share and standardize their SOPs.

---

## 4. UI / UX Design Standards (High-Stress Guided Ergonomics)

Per **ADR-006**, all interfaces must adhere to the following standards:

1. **Clear Visual Hierarchy & Next-Step Prompts:**
   - Every step must clearly highlight the **recommended primary action button** (e.g. high-contrast colored button vs secondary outline button).
   - Guide the user step-by-step: `1. Ingest Master List` ➔ `2. Export for CalTopo` ➔ `3. Import Field Updates & Triage`.
2. **Plain Simple Language:**
   - Avoid developer jargon (e.g., use "Load Structure File" instead of "Parse Ingest Stream"; use "Save CalTopo Map File" instead of "Serialize GeoJSON FeatureCollection").
3. **No-Excel-Required Triage Filters:**
   - Provide 1-click status filter buttons:
     `[All (N)]` `[Needs Assistance (N)]` `[Cleared (N)]` `[Refused (N)]` `[Unchecked (N)]`
   - Include real-time summary count badges at the top of the screen.
4. **Two-Way Reconciliation Diff:**
   - When field GeoJSON is loaded against a master CSV, the table must flag **Unvisited / Missing Structures** (present on the master roster but absent or unmodified in CalTopo).
5. **Command Post Lighting Modes:**
   - **Day Mode:** Clean, high-contrast typography for sunlight readability.
   - **Night Mode:** Dark background with red/amber accents to preserve night-vision discipline inside mobile command posts.
6. **Drag-and-Drop Dropzones:**
   - Every file input must support both click-to-browse and drag-and-drop with prominent visual drop targets.

---

## 5. Checklist for Agents Making Changes

When authorized to implement changes:
- [ ] Ensure `sar-evac-bridge.html` has zero remote assets or external script tags.
- [ ] Test that the HTML file functions when opened directly from the local file system (`file:///...`).
- [ ] Confirm all persistent state uses the `48-hour` TTL schema and auto-purges expired entries.
- [ ] Confirm `index.html` redirects to `sar-evac-bridge.html`.
- [ ] Update `PROPOSED_IMPROVEMENTS.md` review logs upon completing implementation.

