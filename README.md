# Lead-RIO — Geographic Lead Discovery (Public Showcase)

> Find and prioritize leads by **City / State / ZIP**, visualize them on a map, work them with a lightweight CRM, and export to CSV.  
> **This repo is a public showcase (docs + screenshots).** Production source code and datasets remain private.

[Live App](#) · [Features](#features) · [Screenshots](#screenshots) · [Assets](#assets) · [Data Model](#data-model) · [Workflows](#workflows) · [Deployment Notes](#deployment-notes) · [Security](#security--privacy) · [FAQ](#faq) · [Contact](#contact)

---

<p align="center">
  <img src="assets/lead-rio-login.png" alt="Lead-RIO login screen" width="840">
</p>

## Table of Contents
- [Overview](#overview)
- [Key Value Props](#key-value-props)
- [Features](#features)
  - [Discover](#discover)
  - [Qualify](#qualify)
  - [Organize](#organize)
  - [Analyze & Export](#analyze--export)
- [Screenshots](#screenshots)
- [Assets](#assets)
- [Plans & Access](#plans--access)
- [Data Model](#data-model)
  - [Normalized Lead Schema](#normalized-lead-schema)
  - [Saved Views](#saved-views)
  - [Lead Heat](#lead-heat)
- [Workflows](#workflows)
  - [Geo Search → Dashboard → Export](#geo-search--dashboard--export)
  - [Team Follow-ups](#team-follow-ups)
  - [Notes & Tags](#notes--tags)
- [Deployment Notes](#deployment-notes)
  - [High-Level Architecture](#high-level-architecture)
  - [Environment Variables (example)](#environment-variables-example)
  - [Data Import Guidance](#data-import-guidance)
- [Security & Privacy](#security--privacy)
- [Accessibility](#accessibility)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Credits](#credits)
- [License](#license)
- [Contact](#contact)

---

## Overview
**Lead-RIO** is a geo-driven lead finder with a built-in, minimal CRM. Start with geography, refine with keywords, then **rank, annotate, and export** your targets. It’s built for teams that need fast discovery and a transparent, lightweight workflow rather than a full-blown sales cloud.

### Key Value Props
- **Geography-first discovery:** City / State / ZIP queries backed by a **SQLite CityGeo** index for speed.
- **Practical scoring:** **Lead Heat (🔥1–5)** provides a quick, transparent prioritization signal.
- **Work the list quickly:** Inline status, notes, follow-ups, and saved views for recurring campaigns.
- **Own your data:** Export filtered sets to CSV for downstream outreach tools.

---

## Features

### Discover
- Search by **City / State / ZIP** (multi-select; supports US & Intl regions in CityGeo).
- Add **Custom Keywords** (e.g., `boutique, artisan, luxury`) to refine results.
- De-duplication helpers on import and across saved views.

### Qualify
- **Lead Heat (🔥 1–5):** quick prioritization using rule-of-thumb criteria plus operator judgment.
- **Contact Status:** Not Contacted, Left Voicemail, Emailed, Replied, In Progress, Meeting Scheduled, Deal Won/Lost, Do Not Call, etc.
- **Per-lead notes:** free text, follow-up date, and tag chips (`#Qualified`, `#Urgent`, `#DemoComplete`, etc.).

### Organize
- **Saved Views** (name + filters + sort + page length) for easy re-runs.
- **Reminders / follow-ups** shown inline on the dashboard.
- **Super Filter** text search over the current view to slice instantly.

### Analyze & Export
- **Map (Leaflet)** with clustered pins.
- **Charts** (leads by city, heat distribution, status breakdown).
- **CSV Export** of the currently filtered set (admins can restrict fields).

---

## Screenshots

**Lead Generator Portal**
<p align="center">
  <img src="assets/lead-rio-generator.png" alt="Lead Generator Portal form with region and keywords" width="1024">
</p>

**Dashboard**
<p align="center">
  <img src="assets/lead-rio-dashboard.png" alt="Main dashboard showing lead table" width="1280">
</p>

**Contact status menu**
<p align="center">
  <img src="assets/lead-rio-contact-status.png" alt="Contact status dropdown with many workflow states" width="1280">
</p>

**Lead Heat picker**
<p align="center">
  <img src="assets/lead-rio-heat.png" alt="Lead Heat flames selector 1-5" width="1280">
</p>

**Notes & follow-ups**
<p align="center">
  <img src="assets/lead-rio-notes.png" alt="Notes editor with quick tag chips and follow-up date" width="980">
</p>

**Map + Charts**
<p align="center">
  <img src="assets/lead-rio-map-charts.png" alt="Leads plotted on map with city distribution bar chart" width="1280">
</p>

---

## Assets
All screenshots live in `./assets/`:

