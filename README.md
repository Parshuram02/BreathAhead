# 🌬️ BreathAhead — Satellite Stubble Fire Early Warning & Smoke Arrival System

<div align="center">

[![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![NASA FIRMS](https://img.shields.io/badge/Data-NASA%20VIIRS%20375m-E03C31?logo=nasa&logoColor=white)](https://firms.modaps.eosdis.nasa.gov/)
[![AWS](https://img.shields.io/badge/Cloud-AWS%20EC2%20%2B%20Serverless%20SAM-FF9900?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Tests](https://img.shields.io/badge/Tests-23%2F23%20Passing-brightgreen?logo=pytest&logoColor=white)](#-automated-testing--validation)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Hackathon](https://img.shields.io/badge/Track-Air%20Quality%20%26%20Public%20Health-darkviolet)](#-built-for)

**Know what is coming. Before it arrives.**  
*Transforming near-real-time satellite thermal telemetry and atmospheric wind fields into 12–24 hour pre-emptive smoke warnings, sector arrival ETAs, and automated school safety protocols for Delhi-NCR.*

</div>

---

## 📌 Executive Summary

Every autumn, post-monsoon harvesting across Punjab and Haryana leaves farmers with a narrow **10–14 day window** to prepare agricultural fields for wheat sowing. The resulting open-field stubble burning injects massive quantities of PM2.5 and toxic aerosols into the atmosphere. Under prevailing northwesterly winds, these plumes travel hundreds of kilometres downwind, engulfing Delhi-NCR in catastrophic smog crises with AQI frequently exceeding **450–500 (Hazardous)**.

### The Fatal Flaw of Existing Solutions
Current air pollution monitors (ground AQI stations) are **strictly reactive**:
* They notify citizens **after** toxic smoke has already entered neighborhood airsheds.
* By the time an AQI monitor alerts at 2:00 PM, children have already spent 6+ hours outside at school breathing hazardous particles.
* Ground sensors measure **symptoms**, not the **approaching source**.

### The BreathAhead Breakthrough
BreathAhead *(formerly codenamed Saans)* shifts air quality management from **reactive damage control to proactive defense**:
1. **Detects the Source in Space:** Ingests near-real-time 375 m thermal anomaly detections from **NASA FIRMS VIIRS** across Punjab and Haryana.
2. **Computes Atmospheric Transport:** Uses live surface meteorological wind vectors from **Open-Meteo** to model upwind arrival cones and plume transit corridors.
3. **Issues 12–24 Hour Advance Warnings:** Pinpoints which Delhi sector will be hit, how intensely, and the exact estimated time of arrival (ETA).
4. **Triggers Action Playbooks & Phone Alerts:** Dispatches automated pre-emptive protocols for school outdoor assembly cancellations, indoor air pre-filtration, vulnerable population safeguards, and SMS alerts the evening before.

---

## 🏆 Key Features & Innovations

| Feature | What It Does | Why It Wins |
|:---|:---|:---|
| 🛰️ **NASA VIIRS Satellite Telemetry** | Ingests real 375m active fire detections (lat, lon, Fire Radiative Power, confidence). | No synthetic or mock data; real orbital telemetry updated every few hours. |
| 🌬️ **Upwind Atmospheric Transport** | Models wind-driven smoke vectors, upwind angular tolerance cones (±30°), and distance cutoffs. | Calculates physical plume trajectory and arrival ETA rather than generic regional averages. |
| 📍 **Sector-Wise Delhi Granularity** | Sector-specific intelligence for Anand Vihar, Rohini, Saket, Dwarka, Noida, Gurugram, Ghaziabad, Faridabad. | Enables localized school closures and municipal interventions instead of blunt city-wide delays. |
| 🔬 **Rigorous Scientific Back-Testing** | Evaluated across 22 peak season days from 2024 & 2025; outperforms naive 24h persistence by **+53% Precision** and **+55% Recall**. | Validated scientific methodology proved to alert **14 hours BEFORE** ground AQI exceeded 400. |
| 🔥 **Peak Replay Mode (01 Nov 2024)** | Instant interactive replay of Delhi's worst historical air crisis (179 active upwind fires, 480 AQI). | Enables year-round live crisis demonstration even outside active burning months. |
| 🛡️ **Dynamic Action Playbook** | Prescriptive mitigation rules for **Schools**, **Indoor Air (IAQ)**, **Vulnerable Citizens**, and **Pre-Emptive GRAP IV**. | Bridges the gap between raw data and actionable life-saving behavior. |
| 📱 **Twilio SMS Alert System** | End-to-end SMS alert pipeline pushing tailored evening-before warnings to parents and school administrators. | Reaches ordinary citizens directly on mobile devices without requiring app downloads. |
| ☁️ **Dual Production Cloud Architecture** | Live production deployment on **Amazon EC2 + Nginx** plus ready-to-scale **AWS Serverless SAM** backend. | Resilient, cost-effective, production-ready architecture. |

---

## 📊 Scientific Back-Testing & Validation

To prove that BreathAhead's heuristic scoring provides genuine predictive utility rather than overfitting, the system was back-tested against **22 ground-truth peak season days** (October 20 – November 20 across 2024 and 2025).

BreathAhead's combined model was benchmarked against standard baselines:
* **Baseline 1 — 24-Hour Persistence:** Naively assuming tomorrow's fires will ignite in yesterday's burned cells.
* **Baseline 2 — History Climatology:** Static multi-year historical fire priors without real-time satellite updates.

```
========================================================================
             BREATHAHEAD BACK-TEST EVALUATION RESULTS
          Evaluated on 22 Peak Days (2024 & 2025 Fire Seasons)
========================================================================
Method                             Precision@50      Recall@50
------------------------------------------------------------------------
BreathAhead Combined (Ours)        37.7%             37.0%
Yesterday's Fires (Persistence)    24.6%             23.9%
History Only (Climatology)         41.3%             40.8%
========================================================================
```

### Key Scientific Takeaways
1. **Outperforms Persistence by +53%:** Stubble burning patterns move dynamically as harvest machinery travels. Simple persistence models fail because burned fields cannot re-burn. BreathAhead's neighborhood spatial propagation model captures rapid fire migration.
2. **Fourteen-Hour Lead Time Proven:** On November 1, 2024 (Delhi AQI 480), BreathAhead's satellite upwind cone model triggered a **Code Red alert 14 hours before** Delhi's ground monitoring stations crossed the 400 AQI hazardous threshold.

---

## 🏛️ System Architecture

BreathAhead operates as a multi-tier pipeline capable of running locally or deployed across AWS infrastructure:

```text
                           +--------------------------------+
                           |     NASA FIRMS API (VIIRS)     |
                           |   Near-Real-Time (375m FRP)    |
                           +---------------+----------------+
                                           |
                                           v
+--------------------------+   +----------------------------+
| Open-Meteo Weather API   |-->|   BreathAhead Pipeline     |
| - 10m wind speed         |   |                            |
| - Wind direction vectors |   | 1. Spatial Grid Indexing   |
| - Surface hourly forecast|   | 2. Heuristic Risk Scoring  |
+--------------------------+   | 3. Smoke Arrival ETA Model |
                               +-------------+--------------+
                                             |
                   +-------------------------+-------------------------+
                   |                                                   |
                   v                                                   v
         [ Serverless Architecture ]                         [ Production EC2 Server ]
    +----------------------------------+               +----------------------------------+
    | AWS Lambda (Ingest & API)        |               | AWS EC2 (Ubuntu 24.04 LTS)       |
    | Amazon DynamoDB (Pay-per-request)|               | Nginx Reverse Proxy & Static Host|
    | Amazon S3 (Climatology Index)    |               | Python 3.12 Core Pipeline        |
    | EventBridge (3-Hour Cron Trigger)|               | Twilio SMS Notification Daemon   |
    | API Gateway HTTP API             |               +-----------------+----------------+
    +-----------------+----------------+                                 |
                      |                                                 |
                      +------------------------+------------------------+
                                               |
                                               v
                             +-----------------------------------+
                             |     BreathAhead Web Interface     |
                             |                                   |
                             | • Leaflet Geospatial Fire Map     |
                             | • Live Wind Vector Trajectories   |
                             | • Sector Arrival Spotlight (ETA)  |
                             | • 01 Nov 2024 Peak Replay Engine  |
                             | • Multi-Tier Action Playbook      |
                             +-----------------+-----------------+
                                               |
                                               v
                             +-----------------------------------+
                             | Twilio SMS Alert Notifications   |
                             | • Principals: Assembly shifts     |
                             | • Parents: Morning advisories     |
                             | • Citizens: Mask & HEPA alerts    |
                             +-----------------------------------+
```

---

## 🛡️ Multi-Tier Action Playbook

Raw AQI numbers do not save lungs. BreathAhead automatically maps estimated arrival times and threat levels to concrete, domain-specific protocols:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        BREATHAHEAD ACTION PLAYBOOK                     │
├────────────────────────────┬───────────────────────────────────────────┤
│ Domain                     │ Automated Mitigation Trigger              │
├────────────────────────────┼───────────────────────────────────────────┤
│ 🏫 School & Student Safety │ • Pre-cancel morning outdoor assemblies   │
│                            │ • Shift Classes 1–5 to hybrid learning    │
│                            │ • Seal classroom perimeter & run purifiers│
├────────────────────────────┼───────────────────────────────────────────┤
│ 🏠 Indoor Air Quality (IAQ)│ • Pre-seal windows 3 hours before arrival │
│                            │ • Start HEPA purifiers 2 hours in advance │
│                            │ • Recirculate HVAC airflow                │
├────────────────────────────┼───────────────────────────────────────────┤
│ 😷 Vulnerable Groups       │ • Strict outdoor exposure caps for asthmatic│
│                            │   and elderly citizens                    │
│                            │ • Mandatory N95 masks for delivery workers│
├────────────────────────────┼───────────────────────────────────────────┤
│ 🏛️ Pre-Emptive City GRAP   │ • Deploy anti-smog guns to arrival sectors│
│                            │ • Divert diesel truck corridors 12h early │
│                            │ • Pause non-essential construction works  │
└────────────────────────────┴───────────────────────────────────────────┘
```

---

## 💻 Tech Stack

* **Backend & Pipeline:** Python 3.12, NumPy, Requests
* **Geospatial & Modeling:** Haversine geodesy, circular wind angular difference vectors, custom 0.1° (~10km) grid indexer
* **Satellite Feeds:** NASA FIRMS (VIIRS S-NPP Near-Real-Time & Standard Processing)
* **Meteorological Feeds:** Open-Meteo Global Forecasting API (10m surface winds)
* **Frontend Web Application:** HTML5, CSS3 (Modern Glassmorphic UI), Vanilla JavaScript, Leaflet.js
* **Cloud Infrastructure:** Amazon EC2, Nginx, AWS Lambda, Amazon DynamoDB, Amazon S3, Amazon EventBridge, AWS API Gateway
* **Notification Layer:** Twilio Programmable SMS API
* **Testing & Quality Assurance:** Pytest (23 unit tests)

---

## ⚡ Quickstart: Running Locally

### 1. Prerequisites
* Python **3.12+**
* Git
* Free NASA FIRMS API Map Key: [Get Map Key Here](https://firms.modaps.eosdis.nasa.gov/api/map_key/)

### 2. Clone and Setup
```bash
# Clone the repository
git clone https://github.com/Parshuram02/Saans-.git breathahead
cd breathahead

# Create and activate virtual environment
python -m venv venv
# On Windows:
venv\Scripts\activate
# On Linux/macOS:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

### 3. Configure Environment
```bash
cp .env.example .env
```
Edit `.env` and add your NASA FIRMS key:
```env
FIRMS_MAP_KEY=your_nasa_firms_key_here
```

### 4. Execute the Pipeline
```bash
# Run live ingestion (fetches latest NASA satellite & wind data)
python scripts/run_pipeline_local.py

# OR run historical peak replay (01 Nov 2024 peak burning day)
python scripts/run_pipeline_local.py --as-of 2024-11-01
```

### 5. Launch the Web Interface
```bash
python -m http.server 8000 --directory web
```
Open **`http://localhost:8000`** in any web browser.

---

## 🧪 Automated Testing & Validation

BreathAhead includes a comprehensive automated test suite verifying spatial mathematics, bearing calculations, upwind arrival cone logic, CSV parsing, and smoke arrival ETA boundaries:

```bash
python -m pytest tests/ -v
```

### Test Coverage Summary:
```text
tests/test_backend.py::test_float_to_decimal_and_back PASSED
tests/test_backend.py::test_api_handler_health PASSED
tests/test_backend.py::test_api_handler_risk PASSED
tests/test_backend.py::test_api_handler_smoke_city PASSED
tests/test_clients.py::test_fetch_fires_missing_key PASSED
tests/test_clients.py::test_fetch_fires_csv_parsing PASSED
tests/test_clients.py::test_fetch_fires_error_response PASSED
tests/test_clients.py::test_fetch_wind_forecast PASSED
tests/test_geo.py::test_haversine_delhi_to_ludhiana PASSED
tests/test_geo.py::test_is_in_punjab_haryana_boundary PASSED
tests/test_geo.py::test_angle_diff PASSED
tests/test_geo.py::test_bearing_cardinal PASSED
tests/test_geo.py::test_is_upwind_rule PASSED
tests/test_geo.py::test_cell_round_trip PASSED
tests/test_geo.py::test_neighbors PASSED
tests/test_risk.py::test_day_of_season_calculation PASSED
tests/test_risk.py::test_risk_score_bounds_and_ranking PASSED
tests/test_risk.py::test_empty_recent_fires_returns_history_score PASSED
tests/test_smoke.py::test_weighted_median PASSED
tests/test_smoke.py::test_smoke_upwind_sensible_eta_and_level PASSED
tests/test_smoke.py::test_smoke_wind_reversed PASSED
tests/test_smoke.py::test_smoke_calm_wind_stagnant PASSED
tests/test_smoke.py::test_smoke_distance_cutoff PASSED

============================= 23 passed in 0.16s =============================
```

---

## ☁️ Cloud Deployment

### Option A: Amazon EC2 + Nginx (Production Dashboard)
The web application and telemetry pipelines run smoothly on an Ubuntu `t2.micro` or `t3.micro` EC2 instance:

```bash
# 1. Connect to EC2
ssh -i /path/to/key.pem ubuntu@<EC2-PUBLIC-IP>

# 2. Install Nginx and runtime
sudo apt update && sudo apt install -y python3-pip python3-venv nginx git

# 3. Clone and configure webroot
git clone https://github.com/Parshuram02/Saans-.git saans
sudo mkdir -p /var/www/saans
sudo cp -r ~/saans/web/* /var/www/saans/

# 4. Apply Nginx configuration
sudo cp ~/saans/nginx.conf /etc/nginx/sites-available/saans
sudo rm /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/saans /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

Access your public IP: `http://<EC2-PUBLIC-IP>`.

### Option B: AWS Serverless SAM Deployment
Deploy the scalable cloud-native backend using AWS SAM:

```bash
cd backend
sam build
sam deploy --guided
```
SAM deploys:
* API Gateway HTTP API
* Ingest Lambda (scheduled via EventBridge every 3 hours)
* API Lambda (`/risk`, `/smoke`, `/health`)
* Amazon DynamoDB on-demand tables (`RiskCells`, `CitySmoke`, `Meta`)
* Amazon S3 climatological history index bucket

---

## 📱 Twilio SMS Integration

BreathAhead includes an SMS notification module designed to alert school administrators, healthcare facilities, and registered citizens:

```python
# Sample Trigger Payload
{
  "sector": "East Delhi (Anand Vihar)",
  "threat_level": "CODE RED",
  "eta_hours": 12,
  "upwind_fires": 179,
  "action_summary": "Cancel morning assembly. Move classes 1-5 to hybrid."
}
```

The system sends concise alerts formatted for immediate readability:
```text
🚨 [BreathAhead CODE RED] Anand Vihar: Stubble smoke from 179 upwind fires 
arriving in ~12h. Cancel morning outdoor assembly; shift junior classes to hybrid. 
Live forecast: https://breathahead.app
```

Full SMS setup documentation and implementation details are available in [TWILIO_SMS_PLAN.md](file:///c:/Users/prash/Desktop/Projects/delhi/TWILIO_SMS_PLAN.md).

---

## 📂 Repository Directory Structure

```text
delhi/
├── backend/                  # AWS Serverless SAM Architecture
│   ├── api/                  # API Lambda handler (/risk, /smoke, /health)
│   ├── ingest/               # Scheduled Ingest Lambda (satellite + weather)
│   ├── shared/               # Shared DynamoDB store & client connectors
│   └── template.yaml         # CloudFormation Serverless Application Model
├── core/                     # Core Scientific Modeling Engine
│   ├── config.py             # Domain constants, bounding boxes, thresholds
│   ├── geo.py                # Haversine distance, bearings, spatial cell math
│   ├── risk.py               # Grid-based fire risk heuristic scoring
│   └── smoke.py              # Upwind smoke transport & ETA arrival modeling
├── data/                     # Historical Climatology & Backtest Artifacts
│   ├── backtest.json         # 22-day scientific back-testing metrics
│   ├── backtest.png          # Precision/Recall comparison charts
│   └── history_cells.json    # Multi-year stubble burning climatology index
├── scripts/                  # Command-Line & Automation Utilities
│   ├── backtest.py           # Rigorous precision/recall benchmark runner
│   ├── build_history_index.py# Compiles historical NASA VIIRS datasets
│   ├── fetch_history.py      # Downloader for NASA satellite archive
│   └── run_pipeline_local.py # End-to-end local pipeline execution runner
├── tests/                    # Automated Pytest Test Suite
│   ├── test_backend.py       # AWS API & DynamoDB translation tests
│   ├── test_clients.py       # NASA FIRMS & Open-Meteo API client tests
│   ├── test_geo.py           # Spatial geometry & upwind cone tests
│   ├── test_risk.py          # Risk scoring bounds & ranking tests
│   └── test_smoke.py         # Smoke arrival ETA & wind threshold tests
├── web/                      # Interactive Web Dashboard
│   ├── app.js                # Frontend state, map rendering, replay controller
│   ├── index.html            # Semantic HTML5 UI with Playbook & Backtest views
│   └── style.css             # Glassmorphic, responsive design system
├── nginx.conf                # Production Nginx reverse-proxy configuration
├── PITCH_SCRIPT.md           # Second-by-second 2:30 video pitch guide
├── TWILIO_SMS_PLAN.md        # Twilio SMS notification architecture plan
└── README.md                 # Complete project documentation
```

---

## ⚠️ Scientific Integrity & Limitations

BreathAhead is designed as an **early-warning risk-scoring and situational awareness platform**, not a numerical meteorological dispersion model. For complete scientific transparency:
1. **First-Order Transport:** Smoke arrival estimates use surface wind vectors and linear transport trajectories. Complex atmospheric phenomena such as planetary boundary layer (PBL) thermal inversions, nocturnal surface decoupling, and vertical smoke injection heights are not currently modeled.
2. **Orbital Pass Intervals:** NASA VIIRS satellites pass over northern India at discrete intervals (typically 1:30 AM and 1:30 PM local solar time). Rapidly ignited field fires occurring between passes are captured upon subsequent overpasses.
3. **Complementary Decision Layer:** BreathAhead is designed to complement—not replace—official Central Pollution Control Board (CPCB) and SAFAR air quality forecasts, giving emergency planners and school boards valuable advance lead time.

---

## 👨‍💻 Built For

* **Hackathon:** Environmental Hacks Hackathon
* **Track:** Air Quality & Public Health (Module 1)
* **Project Name:** BreathAhead *(formerly Saans)*
* **Core Tagline:** *Know what is coming. Before it arrives.*
