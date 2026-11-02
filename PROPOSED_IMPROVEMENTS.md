# SAR Evac Tools — Improvement Proposals & Decision Records

This document tracks all proposed enhancements, architectural decisions, and review statuses for the **SAR Evac — CalTopo Bridge** standalone tool.

> [!IMPORTANT]
> **Governance Rule:** No code changes will be implemented in `sar-evac-bridge.html` or other project code without explicit user/manager review and approval recorded in this document.

---

## 1. Project Review & Operational Context

### 1.1 What the Tool Does Today
The **SAR Evac — CalTopo Bridge** is a zero-dependency, client-side, single-file HTML/JavaScript utility designed to bridge the gap between dispatch address databases and [CalTopo](https://caltopo.com/) during emergency evacuation operations.

Currently, it provides two sequential capabilities:
1. **Step 1 (Ingest & Prep): Residences CSV → GPX**
   - Parses local address/parcel CSVs.
   - Maps column names for Latitude, Longitude, and Address.
   - Generates a GPX 1.1 file containing waypoints for import into CalTopo.
2. **Step 2 (Status Triage & Record Export): CalTopo GeoJSON → Status CSV**
   - Parses CalTopo exported GeoJSON (where field teams update marker symbols to reflect status).
   - Maps `marker-symbol` values to human-readable evacuation statuses (e.g. Evacuated, Refused, Needs Assistance).
   - Exports a tabular CSV containing address, coordinates, status, marker symbol, and CalTopo feature ID.

### 1.2 Core Strengths to Preserve
- **100% Client-Side Execution:** No data leaves the local machine. Essential for resident PII, operational security, and unreliable incident command internet/cellular connectivity.
- **Single-File Zero-Dependency Architecture:** Opens directly in any modern browser without npm, node, web servers, or installations.
- **Portability:** Operates via live GitHub Pages or offline from a USB thumb drive in mobile command posts (ICP).

---

## 2. Research & Architecture Guidelines

### 2.1 CalTopo API & In-Browser CORS Feasibility (re: PROP-01 / Live Sync)
- **CORS Barrier:** CalTopo's REST API (`caltopo.com/api/...`) does **not** return permissive CORS headers (`Access-Control-Allow-Origin: *`). Direct `fetch()` or `XMLHttpRequest` calls from a browser web app fail due to the browser's Same-Origin Policy.
- **Credential Security:** CalTopo Team API uses HMAC request signing with an **API Credential Secret**. Exposing this secret in client-side browser JavaScript would be an operational security hazard.
- **Decision:** Live direct API synchronization is deferred as a **future enhancement** when a companion local web server/daemon (e.g., lightweight Python or Go local CLI) is introduced. The current tool will focus on frictionless, instantaneous in-browser file workflows (GeoJSON / CSV / GPX) which run 100% reliably offline.

### 2.2 LocalStorage Expiry, Privacy & Backup Hints (re: PROP-05)
- HTML5 `localStorage` has no native Time-To-Live (TTL).
- **48-Hour TTL Policy:** Data will be wrapped with a 48-hour timestamp:
  ```json
  { "timestamp": 1730460000000, "ttlHours": 48, "payload": { ... } }
  ```
- **Auto-Purge & Manual Reset:** Any cached incident data older than 48 hours is automatically cleared on startup. A prominent **"Purge Incident Data"** button allows instant clearing at shift change.
- **Backup Hints & Prompts:** The UI will actively prompt users with clear, persistent hints to download and save local CSV/JSON backup copies so incident records are safely archived outside the browser.

---

## 3. Change Proposals & Acceptance Tracker

| ID | Proposal Summary | Operational Impact | Priority | Status | Phase | Decisions & Scope Notes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **PROP-01** | **Live Evacuation Operations Dashboard**<br>Instant visual counter badges upon loading GeoJSON: Total, Cleared, Needs Assistance (Urgent), Refused, In Progress, Unchecked, Missing. | High: Gives ICP instant situational awareness without opening Excel. | High | **COMPLETED** | **Phase 1** | Implemented client-side in unified dashboard with clickable status counters. |
| **PROP-02** | **CalTopo Evac Symbology Presets**<br>Pre-configured standard symbol presets using standard CalTopo default icons. Ability for an agency to import/export custom preset mappings via a small JSON file. | High: Prevents typos and saves valuable setup minutes. | High | **COMPLETED** | **Phase 1** | Standard CalTopo defaults built in; Agency JSON import/export and preset editor implemented. |
| **PROP-03** | **Extended Field Mapping & Metadata Preservation**<br>Secondary CSV fields (Phone, Gate Code, Resident Notes) into CalTopo descriptions. | Medium: Detailed notes for field teams. | Low | **DEFERRED** | Deferred | De-prioritized by manager to keep focus on core evac workflows. |
| **PROP-04** | **CalTopo GeoJSON as Default Export (with GPX retained)**<br>Default export format changed to CalTopo-shaped GeoJSON (with folder grouping, custom marker icons, and colors). GPX retained as secondary export for legacy devices. | High: Much richer map display and folder structure in CalTopo. | High | **COMPLETED** | **Phase 1** | CalTopo GeoJSON is default export with folder and style properties; GPX 1.1 retained. |
| **PROP-05** | **Offline Session Persistence with 48h TTL & Backup Prompts**<br>Store column mappings, active agency presets, and current session in `localStorage` with a 48-hour auto-TTL, manual purge button, and visible backup prompts. | Critical: Survives accidental tab close/battery loss while addressing data privacy and encouraging safe exports. | High | **COMPLETED** | **Phase 1** | 48-hour auto-purge TTL, prominent manual purge button, and persistent backup reminder banner implemented. |
| **PROP-06** | **Coordinate Validation & Anomaly Checks**<br>Detect swapped lat/lon, 0,0 coords, missing entries, and duplicate coordinates. | High: Prevents dispatching teams to wrong coordinates. | Medium | **UNDER DESIGN** | **Phase 3** | Scheduled for Phase 3; research and design underway. |
| **PROP-07** | **Interactive In-Browser Operational Review Table**<br>Searchable in-app table with quick status filters (e.g. view only "Needs Assistance"). Export Visible/Filtered CSV or Export All CSV. | High: Eliminates need for non-Excel savvy staff to build pivot tables. | High | **COMPLETED** | **Phase 1** | Live search, 1-click status pills, filtered & all CSV exports implemented. |
| **PROP-08** | **Master Roster Reconciliation / Diff Tool**<br>Compare initial dispatched address list against latest CalTopo GeoJSON to flag untouched or unvisited structures. | High: Guarantees 100% accountability; catches missed houses. | High | **COMPLETED** | **Phase 1** | Two-way diff flags missing/untouched master records with alert styling. |
| **PROP-09** | **Field Team Paper Log / Allocation Sheet**<br>Pre-formatted printable assignment log populated with assigned addresses, status checkboxes, and notes for teams operating without tablets. | Medium: Saves teams from manually handwriting dozens of addresses on paper. | Medium | **APPROVED** | **Phase 2** | Re-scoped strictly to field team paper log allocation sheets (not a generic ICS tool). |
| **PROP-10** | **Command Post UI Ergonomics & Day/Night Mode**<br>Drag-and-drop file zones, clean tabbed workflow (Ingest, Triage/Reconcile, Field Logs), high-contrast night/red-tint mode, and plain-language guided design. | Medium: High usability in glare or dark command vehicles under high stress. | High | **COMPLETED** | **Phase 1** | Stepped tabs, drag-and-drop dropzones, recommended action highlights, and red-tint night mode implemented. |
| **PROP-11** | **Integrated Operational Help & CalTopo Workflow Field Guide**<br>Comprehensive in-app help tab, step-by-step expandable CalTopo import/export walk-throughs near action buttons, clickable `ⓘ` term definitions (e.g. Missing/Diff, ICS Point), and key SAR/CalTopo reference links. | High: Eliminates guesswork for sleep-deprived incident managers and standardizes field team instructions. | High | **COMPLETED** | **Phase 1** | Approved by SAR Manager; includes contextual accordions, status glossary, and end-to-end SOP guide. |

---

## 4. Implementation Phasing Plan

```
===================================================================================
PHASE 1: High-Priority Core (Approved for Immediate Implementation)
===================================================================================
  1. Plain-Language Guided Ergonomics & Theme (PROP-10)
     - Clear stepped tabs: [Step 1: Prep & Map] -> [Step 2: Track & Reconcile]
     - Drag-and-drop file dropzones with clear, simple instructions
     - Night-vision / High-contrast command post mode
     - Recommended action highlights (clear visual cues on what to click next)

  2. CalTopo GeoJSON as Default Export + GPX Compatibility (PROP-04)
     - Default output: CalTopo-shaped GeoJSON with folders & styles
     - Secondary download: GPX 1.1 for legacy GPS units

  3. Built-in Symbology Presets & Agency JSON Portability (PROP-02)
     - Standard default CalTopo icons (Cleared, Needs Assistance, Refused, etc.)
     - One-click agency JSON preset import & export

  4. 48-Hour Session Persistence & Data Backup Reminders (PROP-05)
     - 48-hour auto-TTL with automatic purge on expiration
     - One-click "Purge Incident Data" button for shift handoff / privacy
     - Prompts and reminder banners to save local CSV/JSON backup copies

  5. Unified Operational Dashboard & Reconciliation Table (PROP-01, PROP-07, PROP-08)
     - Instant summary cards: Total, Cleared, Needs Assistance, Refused, Unchecked
     - Non-Excel-friendly interactive table with 1-click status filter buttons
     - Two-way reconciliation diff (identifies unvisited structures from master list)
     - Dual export buttons: "Download Filtered CSV" and "Download All CSV"

===================================================================================
PHASE 2: Field Operations & Paper Allocation (Approved Scope)
===================================================================================
  1. Field Team Paper Log Sheets (PROP-09)
     - Pre-printed paper assignment sheets with addresses and checkboxes

===================================================================================
PHASE 3: Coordinate Validation & Future Sync
===================================================================================
  1. Coordinate Validation & Swapped Axes Detection (PROP-06)
  2. Future: Local Companion Webserver / CLI for Direct CalTopo API Sync
```

---

## 5. Architectural Decision Records (ADRs)

### ADR-001: Pure Client-Side Architecture Retained
- **Status:** APPROVED
- **Date:** 2026-11-01
- **Decision:** Keep the application 100% client-side with zero external network dependencies.
- **Rationale:** Ensures complete offline functionality in remote command posts, eliminates CORS blocks, and ensures PII never leaves the user's laptop.

### ADR-002: Default to CalTopo GeoJSON with GPX Secondary
- **Status:** APPROVED
- **Date:** 2026-11-01
- **Decision:** Make CalTopo-formatted GeoJSON the default output for Step 1, while retaining GPX 1.1 as a secondary download.
- **Rationale:** CalTopo GeoJSON allows custom marker symbols, colors, labels, and folder structures that GPX 1.1 cannot represent natively.

### ADR-003: Symbology Presets with Agency JSON Portability
- **Status:** APPROVED
- **Date:** 2026-11-01
- **Decision:** Ship out-of-the-box defaults using standard CalTopo icons; support small JSON files for agency-specific customization.
- **Rationale:** Zero setup friction during emergencies, but fully adaptable for agency-specific standard operating procedures (SOPs).

### ADR-004: In-Memory / LocalStorage 48-Hour TTL & Backup Prompts
- **Status:** APPROVED
- **Date:** 2026-11-01
- **Decision:** Application stores active session data in `localStorage` wrapped with a 48-hour sliding inactivity TTL timestamp, renewed automatically whenever the user interacts with the app. A manual "Purge Incident Data" command is always available, and visible prompts encourage users to download backup CSV/JSON files.
- **Rationale:** Prevents data loss during accidental reloads, protects multi-day ongoing evacuation operations from being purged prematurely during active use, safeguards privacy after 48 hours of inactivity, and ensures operators maintain durable disk backups.

### ADR-005: Joint Rollout of Triage Table and Master Roster Diff
- **Status:** APPROVED
- **Date:** 2026-11-01
- **Decision:** Combine the interactive review table (PROP-07) and the master roster reconciliation diff (PROP-08) into a unified Operational Triage view.
- **Rationale:** Allows the manager to simultaneously see current statuses and immediately identify any structures that have not been visited.

### ADR-006: High-Stress Ergonomics & Plain-Language Guided UX
- **Status:** APPROVED
- **Date:** 2026-11-01
- **Decision:** Design the UI specifically for operators under high stress with varying levels of computer literacy. The recommended next action in every step must be visually prominent. Use plain, simple language rather than technical jargon. Provide visible hints, non-intrusive help callouts, and clean guided filters so non-Excel users can operate with zero training.
- **Rationale:** In an active evacuation, cognitive overload leads to errors. A self-evident, guided interface directly improves responder efficiency and community safety.

---

## 6. Review & Approval Sign-Off Log

| Proposal ID | Decision | Approved By | Date | Target Phase | Implementation Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **PROP-01** | Approved | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |
| **PROP-02** | Approved | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |
| **PROP-03** | Deferred | Brad (SAR Manager) | 2026-11-01 | Deferred | Deferred |
| **PROP-04** | Approved | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |
| **PROP-05** | Approved (48h TTL + Backup Hints) | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |
| **PROP-06** | Scheduled for Phase 3 | Brad (SAR Manager) | 2026-11-01 | Phase 3 | Pending Phase 3 |
| **PROP-07** | Approved | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |
| **PROP-08** | Approved | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |
| **PROP-09** | Re-scoped (Paper Log) | Brad (SAR Manager) | 2026-11-01 | Phase 2 | Scheduled for Phase 2 |
| **PROP-10** | Approved (Plain-Language Guided UX) | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |
| **PROP-11** | Approved (In-App Help & Walkthrough) | Brad (SAR Manager) | 2026-11-01 | Phase 1 | **Implemented & Verified** |

---

## 7. Implementation & Release Notes

### Phase 1 Release (2026-11-01)
- **Zero External Dependencies Maintained:** Retained 100% client-side operation with zero external CDN, font, or script calls.
- **Guided Ergonomics (PROP-10):** Stepped tabbed workflow (`Step 1: Prep & Map`, `Step 2: Track & Reconcile`, `Agency Presets & Settings`), visual dropzones for CSV and GeoJSON, pulsating recommended action highlights, and Red-Tint Night Vision mode for mobile command vehicles.
- **CalTopo GeoJSON Default Export (PROP-04):** Default export generated with CalTopo folder structure (`Evacuation Roster`), custom point symbols, marker colors, and descriptions. GPX 1.1 retained for standalone GPS receivers.
- **Symbology Presets & Portability (PROP-02):** Pre-loaded standard CalTopo presets (`CLEARED`, `ASSIST`, `REFUSED`, `IN_PROGRESS`, `UNCHECKED`) with one-click `.json` export and import for SOP portability.
- **Session Persistence & 48h TTL (PROP-05):** Auto-clears stale incident data after 48 hours; prominent "Purge Incident Data" button for shift handoffs; persistent data backup reminder banner.
- **Dashboard & Two-Way Reconciliation Diff (PROP-01, PROP-07, PROP-08):** Real-time summary status counters, interactive search and filter table, two-way roster diff highlighting missing/unvisited structures with alert badges, and dual CSV export ("Download Filtered CSV" and "Download All CSV").
- **GitHub Pages Sync:** Built and synced `sar-evac-bridge.html` directly to `index.html`.
