# AQI Dashboard — Hackathon Checklist
> 6-hour build · 5 senior CS students · Air Quality Index Dashboard

---

## Legend
- `[A]` Dev A — Backend / API
- `[B]` Dev B — Map / Geo
- `[C]` Dev C — Frontend / Charts
- `[D]` Dev D — Data / Alerts
- `[E]` Dev E — UX / Demo

---

## Phase 1 — Setup & Scaffolding `0:00 – 1:00`

### All hands
- [ ] Decide on final tech stack (React + Vite, Node.js, Mapbox GL, Chart.js)
- [ ] Create shared GitHub repo and add all members as collaborators
- [ ] Agree on branch strategy (e.g. feature branches → main)
- [ ] Share `.env.example` with required environment variable names
- [ ] Set up a group chat / Discord channel for quick communication

### `[A]` Backend / API
- [ ] Register for OpenAQ API key at openaq.org
- [ ] Test `GET /v2/measurements` endpoint — confirm data shape
- [ ] Test `GET /v2/locations` endpoint — confirm lat/lng fields
- [ ] Note rate limits (requests/min) and plan caching accordingly
- [ ] Scaffold Node.js + Express server with a `/health` route
- [ ] Create `/api/aqi` proxy endpoint (wraps OpenAQ)
- [ ] Set up `.env` for API keys — never commit to repo

### `[B]` Map / Geo
- [ ] Register Mapbox account and get public token
- [ ] Bootstrap React app using Vite (`npm create vite@latest`)
- [ ] Install Mapbox GL JS (`npm install mapbox-gl`)
- [ ] Render a base map centered on the US at zoom level 4
- [ ] Confirm map renders in browser before moving on
- [ ] Push working base map to repo so team can pull

### `[C]` Frontend / Charts
- [ ] Install Chart.js (`npm install chart.js react-chartjs-2`)
- [ ] Define shared TypeScript types: `Station`, `Measurement`, `AQIBand`
- [ ] Create `aqiUtils.ts` — function to map AQI value → color + label
- [ ] Define AQI bands: Good (0–50), Moderate (51–100), Sensitive (101–150), Unhealthy (151–200), Hazardous (201+)
- [ ] Create empty sidebar component with placeholder layout

### `[D]` Data / Alerts
- [ ] Create `mock-data.json` with 10–15 sample station objects
- [ ] Include fields: `id`, `city`, `lat`, `lng`, `aqi`, `pm25`, `pm10`, `no2`, `timestamp`
- [ ] Include a mix of AQI bands so the map looks varied
- [ ] Share mock data file with team so B, C, E can build offline
- [ ] Draft alert threshold rules: AQI > 100 = warning, AQI > 150 = danger

### `[E]` UX / Demo
- [ ] Sketch 3-screen wireframe: map view, station sidebar, alert panel
- [ ] Write a 2-minute demo script outline (problem → solution → live demo → impact)
- [ ] Define the "happy path" — what 3 clicks tell the whole story
- [ ] Create a shared Figma or whiteboard link for the team to reference
- [ ] Identify the 2–3 cities to demo (suggest: NYC, LA, Houston)

---

### Milestone check — `0:30`
- [ ] API key works and returns real data
- [ ] Mock data JSON is created and shared
- [ ] Repo exists and all 5 members have pushed at least one commit
- [ ] Base map renders in browser

---

## Phase 2 — Core Build `1:00 – 3:00`

### `[A]` Backend / API
- [ ] Add 5-minute in-memory cache to `/api/aqi` (use `node-cache` or simple object)
- [ ] Add `/api/locations` endpoint returning all active stations with coordinates
- [ ] Add `/api/history/:stationId` endpoint returning last 24 hours of readings
- [ ] Add CORS headers so React frontend can call the server locally
- [ ] Test all endpoints with Postman or curl before frontend integration
- [ ] Write a brief API doc comment at the top of each route

### `[B]` Map / Geo
- [ ] Fetch station data from mock JSON (not API yet — stay unblocked)
- [ ] Render a circle marker for each station on the map
- [ ] Color each marker by AQI band using `aqiUtils.ts` color function
- [ ] Add Mapbox cluster layer for dense station areas
- [ ] Add click handler on marker — logs station ID to console for now
- [ ] Add a zoom-to-city button for at least one demo city

### `[C]` Frontend / Charts
- [ ] Build sidebar layout: city name, AQI score (large), AQI label badge
- [ ] Add stat cards row: PM2.5, PM10, NO2 with units
- [ ] Build 24-hour sparkline using Chart.js line chart
- [ ] Color sparkline fill zones by AQI band (green → red gradient bands)
- [ ] Wire sidebar to accept a `selectedStation` prop (use mock data)
- [ ] Add skeleton loader for when data is fetching

### `[D]` Data / Alerts
- [ ] Implement `alertEngine.ts` — watches AQI values, emits alerts above threshold
- [ ] Install `react-hot-toast` for toast notifications (`npm install react-hot-toast`)
- [ ] Trigger a toast when a station crosses AQI 100
- [ ] Trigger a red urgent toast when a station crosses AQI 150
- [ ] Build alert log panel — list of recent alerts with timestamp + city
- [ ] Test alert engine against mock data edge cases (boundary values)

### `[E]` UX / Demo
- [ ] Build global CSS token file (colors, spacing, font sizes)
- [ ] Create app header with project name and live data indicator dot
- [ ] Add loading spinner / skeleton states for map and sidebar
- [ ] Build responsive layout shell (sidebar collapses on narrow screens)
- [ ] Confirm layout looks good in Chrome at 1280×800 (likely demo resolution)

---

### Milestone check — `1:30`
- [ ] Map renders colored markers from mock data
- [ ] Sidebar shows station info when a marker is clicked (even from mock)
- [ ] Toast alert fires when a high-AQI station is selected

---

### Milestone check — `2:30`
- [ ] Live OpenAQ API data is flowing into the map
- [ ] Sidebar updates correctly from real station data
- [ ] No console errors on the happy path

> If live API isn't ready by 3:00 — stay on mock data. Do not chase the bug past this point.

---

## Phase 3 — Integration & Features `3:00 – 5:00`

### `[A]` Backend / API
- [ ] Wire `/api/history/:stationId` to real OpenAQ historical endpoint
- [ ] Add query param support: `?days=7` for 7-day history
- [ ] Return data in a clean format: `[{ timestamp, aqi, pm25, pm10 }]`
- [ ] Confirm response time is under 1 second (important for demo)
- [ ] Handle OpenAQ rate limit errors gracefully (return cached data if available)

### `[B]` Map / Geo
- [ ] Connect marker click → fires `setSelectedStation()` in global state
- [ ] Add heatmap layer toggle button (show/hide AQI density heatmap)
- [ ] Smooth map fly-to animation when a city is selected from search
- [ ] Add map legend: AQI color band key in bottom-left corner
- [ ] Test clustering at zoom level 4 and 8 — confirm no overlap issues

### `[C]` Frontend / Charts
- [ ] Build 7-day trend chart (line chart, one line per pollutant)
- [ ] Add pollutant selector tabs: PM2.5 / NO2 / O3
- [ ] Add city search input — filters to matching stations on the map
- [ ] Format all numbers correctly (AQI = integer, PM2.5 = 1 decimal)
- [ ] Add "last updated" timestamp below stat cards

### `[D]` Data / Alerts
- [ ] Build health advisory panel: AQI band → plain-English guidance text
- [ ] Include at-risk group callouts (elderly, children, asthma sufferers)
- [ ] Add color-coded advisory banner at top of sidebar for high AQI stations
- [ ] Populate alert log from real data (not just triggered manually)
- [ ] Test alert panel with a station in the "Hazardous" band

### `[E]` UX / Demo
- [ ] Full end-to-end walkthrough of the happy path — note every bug found
- [ ] Create bug list shared doc and prioritize: P1 (blocks demo) vs P2 (cosmetic)
- [ ] Write final 2-minute demo script with exact click sequence
- [ ] Identify which city has the most interesting live AQI data right now
- [ ] Prepare a fallback: screenshot or recorded GIF in case of live API failure

---

### Milestone check — `4:00` — SCOPE FREEZE
- [ ] Full happy path works end-to-end with real or mock data
- [ ] Map, sidebar, alerts, and charts all functional
- [ ] No P1 bugs remaining
- [ ] **No new features added after this point — bugs only**

---

## Phase 4 — Polish & Demo Prep `5:00 – 6:00`

### `[A]` Backend / API
- [ ] Deploy backend to Railway, Render, or Fly.io — get a live HTTPS URL
- [ ] Update frontend `.env` to point at deployed backend URL
- [ ] Pre-seed cache with data for NYC, LA, and Houston
- [ ] Confirm deployed API returns data within 1 second
- [ ] Keep a localhost fallback ready in case deploy has issues

### `[B]` Map / Geo
- [ ] Smooth out any janky map animations
- [ ] Fix any marker overlap edge cases at certain zoom levels
- [ ] Confirm heatmap toggle works cleanly on/off
- [ ] Test on the demo screen resolution — zoom / pan to ideal starting position
- [ ] Set default map view to the best-looking starting state for the demo

### `[C]` Frontend / Charts
- [ ] Final chart color pass — consistent with AQI band palette
- [ ] Confirm all numbers are formatted (no raw floats displayed)
- [ ] Check all loading states — nothing should be blank during data fetch
- [ ] Cross-browser check in Chrome and Firefox
- [ ] Remove any `console.log` debug statements

### `[D]` Data / Alerts
- [ ] Run full alert engine stress test — fire 5 rapid station changes
- [ ] Confirm toasts dismiss cleanly and don't stack infinitely
- [ ] Verify health advisory panel text is accurate for all 5 AQI bands
- [ ] Write a one-paragraph README with setup instructions and API key note
- [ ] Push final README to repo

### `[E]` UX / Demo
- [ ] Rehearse the 2-minute demo at full speed — time it
- [ ] Rehearse again. Fix any hesitations or unclear transitions.
- [ ] Prepare one strong opening line that frames the problem
- [ ] Know the fallback plan: what to say if the API goes down mid-demo
- [ ] Confirm presenter has the live URL open and cached in browser

---

### Milestone check — `5:30`
- [ ] Live URL is up and returning real data
- [ ] Demo has been rehearsed at least once at full speed
- [ ] All team members know their role during the presentation

---

## Final — Submission `6:00`

- [ ] Push all final code to GitHub — confirm main branch is up to date
- [ ] Submit live URL + repo link to hackathon portal
- [ ] Double-check submission form is fully filled out (project name, description, team names)
- [ ] Take a team screenshot of the running app for the submission
- [ ] Write a 3-sentence project description: problem, solution, tech used

---

## Cut list — if behind schedule

| Cut when | Feature | Replace with |
|---|---|---|
| Behind at 2:00 | Heatmap layer | Colored markers only |
| Behind at 3:00 | 7-day historical chart | 24-hour sparkline only |
| Behind at 3:00 | Pollutant breakdown tabs | Single AQI number |
| Behind at 4:00 | City search input | Hard-coded 5-city dropdown |
| Behind at 4:00 | Toast alert notifications | Static colored alert badge |
| Never cut | Live map + colored markers | This is the demo core |
| Never cut | Sidebar AQI stat panel | This is the demo core |

---

## Emergency contacts & fallbacks

- [ ] OpenAQ down → switch to WAQI API (waqi.info) — same data shape
- [ ] Mapbox down → swap to Leaflet.js + OpenStreetMap (free, no key)
- [ ] Deploy fails → localhost + screen share is fully acceptable
- [ ] API rate limited → serve from mock data JSON with a disclaimer
- [ ] Live data looks boring → pre-load a day with visible AQI spikes

---

*Good luck. Freeze scope early. Rehearse the demo. Ship it.*
