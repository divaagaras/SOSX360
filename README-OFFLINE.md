# 🚨 SOSX360

> **Offline-First, Multilingual (English · தமிழ் · हिन्दी) Emergency Response & Disaster Management Platform with Voice Reporting, Priority Scoring and Nearest-Team Dispatch.**

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![JavaScript](https://img.shields.io/badge/JavaScript-ES5%20Vanilla-F7DF1E.svg)
![Leaflet](https://img.shields.io/badge/maps-Leaflet%201.9.4-199900.svg)
![PWA](https://img.shields.io/badge/PWA-Service%20Worker%20Offline-5A0FC8.svg)
![Voice](https://img.shields.io/badge/voice-Web%20Speech%20API-E11D2E.svg)
![Languages](https://img.shields.io/badge/languages-EN%20%7C%20TA%20%7C%20HI-orange.svg)
![Deploy](https://img.shields.io/badge/deploy-Vercel-black.svg)

---

## 📌 Problem Statement

During fires, road accidents, floods and building collapses, the first minutes decide outcomes. Existing reporting and coordination workflows suffer from four bottlenecks:

1. **Typing Under Stress:** Victims and bystanders cannot reliably type long forms while panicking, injured or using a phone one-handed. Reports are delayed or incomplete.
2. **Language Barrier:** Most emergency software is English-only, excluding a large share of Tamil- and Hindi-speaking citizens exactly when clarity matters most.
3. **Manual Triage & Dispatch:** Operators rank incidents by gut feeling and pick teams by phone calls, wasting minutes on "who is closest and free?".
4. **Connectivity Loss:** Disasters damage networks. Cloud-only tools go blank precisely when they are needed.

**SOSX360** solves this with a single-file, installable web app. A citizen can **speak** a report in English, Tamil or Hindi; the platform auto-fills the form, scores the incident's priority, finds the **nearest suitable available team**, draws the route on a live map, and keeps working **offline**, syncing queued reports when the network returns.

---

## 🚀 Features

| Feature | Subsystem | Mechanism | Output | Status |
|---|---|---|---|---|
| **Voice Incident Reporting** *(novelty)* | Voice engine (`#mic`, `#voicego`) | Web Speech API recognition in `en-IN` / `ta-IN` / `hi-IN`; keyword + multilingual regex extracts category, people count and location from speech; offline mode requests on-device recognition | Pre-filled report; spoken confirmation with reference number | **Done** |
| **Multilingual UI (EN/TA/HI)** | i18n layer (`D`, `T()`, `tr()`) | Dictionary translation with DOM tree-walker + MutationObserver, covering dynamic content, placeholders, validation errors and Round 2 screens | Whole app switches language instantly, persisted | **Done** |
| **Offline-First Operation** | Service worker (`sw.js`) + outbox | App-shell caching, `localStorage` datastore, queued API writes replayed on reconnect, offline gazetteer for geocoding Chennai-area places | Report, assign and track with no network | **Done** |
| **Intelligent Incident Prioritization** | `score()` / `classify()` | Weighted score = severity + people affected + minutes since reporting + category base; resolved incidents drop to 0 | Ranked incident list with ⚡ priority score; Critical/High/Medium/Low badges | **Done** |
| **Response Team Management** | Teams view (`tlist`) | Create/delete teams with name/ID, category (Fire, Medical, Rescue, Police), location (text or lat/lng), availability and assigned incident | Team roster with live Available/Busy state | **Done** |
| **Location-Based Team Assignment** | `cand()` / `km()` | Haversine distance between incident and team coordinates, filtered by category suitability and availability; best fit = nearest with category preference | One-click *Auto-assign nearest team* plus top-3 ranked options with km | **Done** |
| **Assignment & Lifecycle Tracking** | `assign()` / `setStatus()` | Reported → Assigned → In Progress → Resolved; team auto-set Busy on assignment and Available on resolution/delete | Team details on incident and public tracking page | **Done** |
| **Route & Location Visualization** | Leaflet map | Incident markers, team markers, dashed team→incident polyline labelled with distance and ETA | Live map on Home and Live Map views | **Done** |
| **Advanced Analytics Dashboard** | `dash()` | 8 KPI tiles, CSS conic-gradient donut for category distribution, prioritized attention queue | Operational overview for responders | **Done** |

---

## 🏗️ Tech Stack

| Component | Version | Role | Rationale |
|---|---|---|---|
| **HTML5 / CSS3 / Vanilla JS (ES5)** | n/a | Entire application in one `index.html` | Zero build step, tiny payload, works on any phone, trivially cacheable offline |
| **Leaflet** | `1.9.4` | Interactive map and route lines | Lightweight, no API key, cached by service worker after first load |
| **Esri World Street tiles** | n/a | Map base layer | Free tile source; tiles are not available offline (see Limitations) |
| **Nominatim (OpenStreetMap)** | n/a | Online geocoding | Free address to coordinates; offline gazetteer used as fallback |
| **Web Speech API** | n/a | Speech recognition and synthesis | Native browser voice input/output with `en-IN`, `ta-IN`, `hi-IN` |
| **Service Worker + Cache API** | n/a | Offline app shell | Network-first navigation, cache-first static assets, never caches `/api/` |
| **localStorage** | n/a | Incidents, teams, outbox, language | Offline persistence and sync queue |
| **Vercel (optional)** | n/a | Static hosting, HTTPS | HTTPS is required for service workers, GPS and voice |

---

## 📂 Project Structure

```
SOSX360/
├── index.html      # Complete app: UI, i18n, voice, priority, teams, assignment, map, dashboard
├── sw.js           # Service worker: offline caching (cache sosx360-static-v2)
├── offline.html    # Fallback page when the app has never been cached
├── manifest.json   # (optional) PWA manifest referenced by index.html
├── README.md       # This file
└── api/            # (optional) backend endpoints; app works fully without them
```

---

## ⚙️ Installation & Setup

### Prerequisites
- Any modern **Chromium browser** (Chrome / Edge) recommended for voice input
- A static host with **HTTPS** (Vercel, Netlify, GitHub Pages) or `localhost`

### Run locally
```bash
git clone https://github.com/your-org/SOSX360.git
cd SOSX360
python3 -m http.server 8080
# open http://localhost:8080
```

### Deploy to Vercel
1. Place `index.html`, `sw.js`, `offline.html` in the repository root.
2. Commit and push; Vercel redeploys automatically.
3. Open the HTTPS URL **once while online** so the service worker installs.
4. Test: DevTools → Network → **Offline** → refresh. The app still loads.
5. After an update, if an old version persists: DevTools → Application → *Clear site data*, reload online.

### Demo admin access
Open **Admin login** and use the demo passcode `admin123` (client-side demo only, replace before real use).

### 🎤 Enabling offline voice (Tamil / Hindi)
Chrome's recognition normally streams audio to a server. For offline voice, install the on-device speech pack for the language (Chrome → Settings → Accessibility / Languages → on-device speech). If unavailable, the app shows a clear message and the user can type.

### Smoke test
1. **Report** → tap 🎤 Speak → say *"Fire at Anna Nagar, 6 people trapped"* → category, people and location auto-fill.
2. Submit → hear the spoken reference number.
3. **Admin login → Incidents** → open the incident → **Auto-assign nearest team**.
4. **Live Map** → see the dashed team→incident route with km and ETA.
5. Switch the language selector to **தமிழ்** or **हिन्दी** and repeat.

---

## 🧠 How It Works

```
┌─────────────────────────────────────────────────────────────────────┐
│                         SOSX360 PIPELINE                            │
├─────────────────────────────────────────────────────────────────────┤
│ 1 REPORT   Voice (EN/TA/HI) or form ➔ category/people/location      │
│            extraction ➔ GPS or geocode (online) / gazetteer (offline)│
├─────────────────────────────────────────────────────────────────────┤
│ 2 PRIORITISE  severity + people + waiting time + category ➔ score   │
│               ➔ Critical / High / Medium / Low, ranked queue        │
├─────────────────────────────────────────────────────────────────────┤
│ 3 DISPATCH   filter Available + suitable teams ➔ Haversine distance │
│              ➔ nearest best-fit team ➔ assigned, team set Busy      │
├─────────────────────────────────────────────────────────────────────┤
│ 4 TRACK      Assigned ➔ In Progress ➔ Resolved ➔ team Available     │
│              map route + ETA, dashboard analytics                   │
├─────────────────────────────────────────────────────────────────────┤
│ 5 SYNC       localStorage + outbox ➔ replayed when online again     │
└─────────────────────────────────────────────────────────────────────┘
```

### Priority score
```
score = (severity_rank + 1) × 25      # Low 25 … Critical 100
      + min(people_affected, 50) × 0.8
      + min(minutes_since_report, 240) × 0.1
      + category_base × 5             # fire/collapse 3, medical/road/flood 2, other 1
```
Older, larger and more dangerous incidents rise to the top; resolved incidents score 0.

### Nearest-team selection
Distance uses the Haversine formula. Suitability map: Fire → Fire/Rescue, Medical → Medical, Road → Medical/Rescue/Police, Flood → Rescue, Collapse → Rescue/Fire/Medical, Other → Police/Rescue. Only **Available** teams are candidates; a small preference penalty favours the primary category. ETA assumes ~35 km/h urban speed.

### Offline behaviour
- **Navigation:** network-first, falling back to cached `index.html`, then `offline.html`.
- **Static assets:** Leaflet and fonts cached after the first successful load.
- **Data:** incidents and teams live in `localStorage`; writes are queued in an outbox and flushed every 15 s or on reconnect.
- **Geocoding offline:** a built-in gazetteer of 24 Chennai-area localities resolves common place names; unresolved addresses show a clear notice.

---

## 📈 Scalability

- **Stateless static frontend:** served from a CDN, scales to any number of users.
- **Pluggable backend:** the same `/api/incidents` contract (GET/POST/PATCH/DELETE) lets a server become the shared source of truth; the outbox already handles retries and idempotent merges.
- **Team/incident volume:** all ranking and distance work is O(teams × incidents) in the browser, comfortable for hundreds of teams.
- **Easy language expansion:** add a column to the dictionary `D` and a locale code in `LC`.

## 💡 Feasibility

- **No build tooling, no framework, no paid APIs:** one HTML file plus a service worker.
- **Runs on low-end phones** and weak networks.
- **Zero licensing cost:** Leaflet, OpenStreetMap/Esri tiles and Web Speech API are free to use.
- **Deployable in minutes** on any HTTPS static host.

---

## 🌟 Novelty & Comparative Analysis

| Capability | SOSX360 | Typical hackathon CRUD app | Government helpline apps |
|---|---|---|---|
| Voice reporting in Tamil & Hindi | ✅ Auto-fills category, people, location | ❌ | ⚠️ Mostly call-based |
| Works offline with sync queue | ✅ | ❌ | ⚠️ Limited |
| Auto priority scoring | ✅ Transparent formula | ⚠️ Severity only | ❌ Manual |
| Nearest suitable team + ETA | ✅ Haversine + category fit | ❌ | ⚠️ Operator-driven |
| Spoken confirmation | ✅ | ❌ | ❌ |
| Zero-install, single file | ✅ | ⚠️ | ❌ |

**Core innovation:** a hands-free, language-inclusive, connectivity-tolerant path from *spoken sentence* to *dispatched team on a map*.

---

## 🔧 Feature Depth & Edge-Case Handling

- **Voice:** unsupported browser, denied microphone, and missing offline language pack each show a clear message instead of failing silently.
- **Language:** switch mid-session without reload; validation errors, placeholders, dynamic lists and spoken output are all localized.
- **Assignment:** no team when location is unknown, no available suitable team, or incident already resolved: each has a distinct message.
- **Team lifecycle:** deleting an incident or resolving it frees the team only if it has no other open incident; deleting a team unassigns its incidents.
- **Sync safety:** permanent 4xx errors drop a queued item; transient errors retry; server data is merged without losing local team assignments or coordinates.
- **Security basics:** all user text is HTML-escaped before rendering.

### Incident record
```json
{
  "id": "INC-4821", "title": "Warehouse fire", "cat": "fire",
  "desc": "Smoke spreading, people trapped", "loc": "Anna Nagar, Chennai",
  "ppl": 6, "sev": "Critical", "status": "Assigned",
  "teamId": "T-02", "lat": 13.085, "lng": 80.2101, "at": 1762767000000
}
```

---

## ⚠️ Limitations & Disclaimer

- **Not a replacement for emergency calls.** In India dial **112** (Police 100 · Fire 101 · Ambulance 102/108).
- Map **tiles need internet**; markers, routes and data still work offline.
- Voice recognition quality and offline support depend on the browser and installed language packs (best in Chrome).
- Route lines are **straight-line distance/ETA**, not turn-by-turn navigation.
- Demo authentication and `localStorage` records are **not secure shared services**; use a real authenticated backend for production.
- Test the deployed site after each service-worker update.

---

## 📜 License

MIT License. Copyright 2026 SOSX360 Contributors.

---

## 🧩 Team

**SOSX360 Team**, Code Clash 2026 Hackathon, Round 2
- **Project Repository:** https://github.com/your-org/SOSX360
