# Lead-RIO — Geographic Lead Discovery (Public Showcase)

[![Live App](https://img.shields.io/badge/Live%20App-Open%20Lead--RIO-blue)](https://leadrio-f9b268bd4539.herokuapp.com/)

> Find and prioritize leads by **City / State / ZIP**, visualize them on a map, work them with a lightweight CRM, and export to CSV.  
> **This repo is a public showcase (docs + screenshots).** Production source code and datasets remain private.

---

## Overview
**Lead-RIO** is a geo-driven lead finder with a built-in mini-CRM. Start with geography, refine with keywords, then **rank, annotate, and export** your targets. It’s built for teams that want speed and transparency without a heavy sales cloud.

### Key Value Props
- **Geography-first discovery:** fast City/State/ZIP queries (backed by a compact *CityGeo* SQLite index).
- **Practical scoring:** **Lead Heat (🔥1–5)** for quick, explainable prioritization.
- **Work fast:** inline status, notes, follow-ups, saved views.
- **Own your data:** export filtered results to CSV for downstream tools.

---

## Features

### Discover
- Search by **City / State / ZIP** (multi-select, US + Intl capable via CityGeo).
- Add **custom keywords** (e.g., `boutique, artisan, luxury`) to refine.
- Import de-duplication helpers and provenance tracking (`search_term`, `search_id`).

### Qualify
- **Lead Heat (🔥1–5)** with simple, auditable rules (see *Lead Heat* below).
- **Contact Status** menu: Not Contacted, Voicemail, Emailed, Replied, In Progress, Meeting Scheduled, Deal Won/Lost, Do Not Call, etc.
- **Notes & follow-ups:** free text, follow-up date, and tag chips (`#Qualified`, `#Urgent`, `#DemoComplete`, …).

### Organize
- **Saved Views** (name + filters + sort + page length) for one-click reruns.
- **Reminders** surface due/overdue follow-ups.
- **Super Filter** text search over the current slice.

### Analyze & Export
- **Map (Leaflet)** + **charts** (leads by city, heat distribution, status).
- **CSV export** of the currently filtered set (admins can restrict fields).

---

## Screenshots

**Login**  
![Lead-RIO login screen](assets/lead-rio-login.png)

**Lead Generator Portal**  
![Lead Generator Portal with region and keywords](assets/lead-rio-generator.png)

**Dashboard**  
![Dashboard with lead table](assets/lead-rio-dashboard.png)

**Contact Status Menu**  
![Contact status dropdown](assets/lead-rio-contact-status.png)

**Lead Heat Picker**  
![Lead Heat flames selector](assets/lead-rio-heat.png)

**Notes & Follow-ups**  
![Notes editor with tags and follow-up date](assets/lead-rio-notes.png)

**Map + Charts**  
![Leads on map with city distribution chart](assets/lead-rio-map-charts.png)

---

## Assets
All screenshots live in `./assets/`:

```
/assets
├─ lead-rio-login.png
├─ lead-rio-generator.png
├─ lead-rio-dashboard.png
├─ lead-rio-contact-status.png
├─ lead-rio-heat.png
├─ lead-rio-notes.png
└─ lead-rio-map-charts.png
```

> If you rename files, update the image paths above. Alt text is included for accessibility.

---

## Plans & Access
- **Starter** — Single user, geo search, notes, CSV export.  
- **Pro** — Saved Views, reminders, Lead Heat, team sharing.  
- **Enterprise** — SSO, audit logs, custom export schema, priority support.  

*(Pricing intentionally omitted. Replace with your public matrix, if desired.)*

---

## Data Model

### Normalized Lead Schema
```
Lead {
  id:                STRING/UUID
  created_at:        DATETIME
  name:              STRING
  phone:             STRING|null
  email:             STRING|null
  website:           STRING|null
  address:           STRING
  city:              STRING
  state:             STRING
  postal_code:       STRING
  country:           STRING
  latitude:          FLOAT|null
  longitude:         FLOAT|null

  search_term:       STRING
  search_id:         STRING
  status:            ENUM   # 'not_contacted' | 'voicemail' | 'emailed' | 'replied' | ...
  heat:              INT    # 1..5

  next_follow_up:    DATE|null
  tags:              STRING[]    # e.g., ["#Qualified","#HotLead"]
  notes:             TEXT|null

  last_contacted_at: DATETIME|null
  last_contact_mode: ENUM|null    # 'phone' | 'email' | 'inperson' | ...
}
```

### Saved Views (example)
```json
{
  "id": "svw_8f13",
  "name": "Sedona boutique 863xx",
  "filters": {
    "city": ["Sedona"],
    "state": ["Arizona"],
    "keywords": ["boutique"],
    "heat_min": 3,
    "status_in": ["not_contacted", "emailed"]
  },
  "sort": { "field": "created_at", "dir": "desc" },
  "page_size": 50
}
```

### Lead Heat (rules-of-thumb)
```
Base = 1
+1 if keyword match in name/domain
+1 if within target geo and category fit
+1 if valid phone/email present
+1 if website detected & not parked
Manual bump up/down by operator
Cap: 5
```
*Intentionally not ML-based; fast and explainable. If you need ML scoring, add it in your import pipeline.*

---

## Workflows

### Geo Search → Dashboard → Export
1. **Generator Portal:** choose Region/City/ZIP; add keywords.  
2. **Dashboard:** scan list, set **Status** and **Heat**.  
3. **Annotate:** notes, tags, follow-up date.  
4. **Save View** to reuse the slice.  
5. **Export CSV** (filtered) for outreach tools.

### Team Follow-ups
- Each lead shows **Date Last Reached** and **Next Follow-up**.  
- Due/overdue reminders appear at the top of the dashboard.  
- Enterprise: activity exports/audit logs.

### Notes & Tags
- Use free-text notes for context and outcomes.  
- Tag chips standardize taxonomy (`#Qualified`, `#Gatekeeper`, `#DemoComplete`, …).  
- Tags are queryable via **Super Filter**.

---

## Deployment Notes
*(Production app and infra are private. The outline below documents the high-level approach.)*

### High-Level Architecture
```
Browser (React or HTMX-style)
        |
        v
App API (Python/Flask or Node/Fastify)
        |
        +-- CityGeo (SQLite)    # fast geo lookups
        +-- Lead DB (Postgres)  # normalized leads, notes, views
        +-- Object Storage      # exports/backups
        +-- Auth (JWT/SSO)      # email+password; SSO on Enterprise
```

### Environment Variables (example)
```
APP_ENV=production
PORT=8080

# Auth
JWT_SECRET=change-me
OAUTH_PROVIDER_URL=
OAUTH_CLIENT_ID=
OAUTH_CLIENT_SECRET=

# DBs
CITYGEO_SQLITE_PATH=/data/citygeo.sqlite
POSTGRES_URL=postgresql://user:pass@host:5432/lead_rio

# Storage
EXPORT_BUCKET=s3://lead-rio-exports
EXPORT_SIGNED_URL_TTL=900

# Flags
ENABLE_SAVED_VIEWS=true
ENABLE_ACTIVITY_REPORTS=true
```

### Data Import Guidance
- Provide CSVs with at least: `name,address,city,state,postal_code,country,phone,email,website`.  
- If geocodes are missing, the system can geocode on-the-fly (rate-limited).  
- Imports are de-duplicated against existing data (name+address or fuzzy combos).  
- Include **provenance** fields (`search_term`, `search_id`) when importing from ad-hoc crawls.

---

## Security & Privacy
- **Data ownership:** customer-uploaded data remains the customer’s property.  
- **Isolation:** customer-scoped DBs; customer-owned buckets optional for exports.  
- **Access control:** role-based; SSO & audit logs on Enterprise.  
- **PII:** use business contacts only; avoid sensitive personal data.  
- **Deletion:** admins can purge uploads, views, and exports upon written request.

---

## Accessibility
- Screenshots include meaningful alt text in this README.  
- UI targets proper label associations, keyboard navigation on core controls, and color-contrast-conscious theming.

---

## Troubleshooting
- **No results after search:** verify region/city/ZIP and try fewer keywords.  
- **Map pins missing:** check geocoding; confirm latitude/longitude are present post-import.  
- **CSV export empty:** exports honor current filters—clear filters or widen date/window.  
- **Saved View not shared:** Starter views are private. Use Pro/Enterprise for team sharing.

---

## Roadmap
- Mobile field-rep mode & route planning  
- API/webhooks for downstream enrichment & outreach sync  
- Enrichment connectors (opt-in, per-customer keys)  
- Smarter dedupe / entity resolution across imports

---

## Credits
- **Product & Engineering:** Joseph-Michael Schulz

---

## License
Documentation in this repository is released under **MIT**.  
The production application and datasets are private.

---

## Contact
Questions, pilots, or enterprise access?  
**joey@dksmithandco.com**
