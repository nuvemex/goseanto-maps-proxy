# Goseanto Maps Proxy — High-Reliability ETA & Directions Layer


A lightweight routing layer for public safety, CAD/dispatch, taxi, and logistics systems that need predictable, stable, and cost-efficient access to Google Maps Distance Matrix and Directions APIs.

You keep your existing Google Maps logic.
You simply switch your calls to our gateway.

## 🚀 What the Goseanto Maps Proxy Does

Instead of every unit calling Google directly:

Unit → Google → $0.005–$0.01 per element  
Unit → Google → $0.005–$0.01 per element  
Unit → Google → $0.005–$0.01 per element  
...



Your units call our gateway:

Unit → Goseanto Proxy → Google (1 time)
                        ↳ Shared cached result (10–20 sec)



✔ Same accuracy
✔ Same Google data
✔ Up to 60–90% cost reduction
✔ Far more stable response times

## 💰 Why This Saves Money

Google bills:

Per request

Often per element (origins × destinations)

Even if traffic hasn’t changed

Large fleets produce duplicate calls:

20–100 units asking the same ETA

Every 1–3 seconds

For the same incident / same streets / same hospital

Our approach:

First request → goes to Google

For the next 10–20 seconds → cache serves identical data

Cache window is shorter than Google’s own traffic update cycle

Result:
Accuracy stays the same.
Billing drops dramatically.

## 🎯 Accuracy Guarantee

Traffic data does not update every second.

Google updates traffic every 2–7 minutes depending on the city.

Our cache TTL:

Traffic ON  → 10–20 seconds  
Traffic OFF → longer TTL


Meaning:

No stale results

Google would return the same ETA if you called them again

We stay well inside Google’s accuracy envelope

## 🛡️ Reliability & Failover

We serve an ETA even if:

Google times out

Google returns 500/502/503

Rate-limits occur

Regional outages happen

Fallback logic:

If Google fails:
    return last-known-good ETA


Dispatch systems will not freeze waiting on Google.

## 🔑 Per-Client API Keys

Each customer receives a dedicated key, e.g.:

Clinent-STG-123

TAXI-CA-001

LOGISTICS-EU-555

Used for:

Authentication

Metrics

Throttling

Key rotation

Clients are isolated and cannot see each other's traffic.

## 🌍 Regional Deployment (Data Residency)

We support deployments in:

🇨🇦 Canada (ca-central-1) — PIPEDA-compliant

🇺🇸 US regions — HIPAA-ready architecture

🇪🇺 EU regions — GDPR alignment

Additional regions available on request

All cache + metadata stays in-region.

## 🧭 Sectors We Support

EMS / Paramedic

Police CAD

Fire dispatch

Ride-hailing / taxi

Logistics & last-mile

Municipal fleets

These systems generate heavy Google load — we optimize it.

## ⚙️ API Endpoints

We support **both JSON body AND traditional query parameters**  


### ✅ Option A — JSON Body (recommended)
POST https://maps.<stage>.goseanto.com/eta?key=<API_KEY>


Request
```json
{
"origins": ["45.5017,-73.5673"],
"destinations": ["45.5081,-73.5550"],
"traffic": true
}
```
## Option B — Query Parameters (Google-compatible)
```bash
 GET https://maps.<stage>.goseanto.com/eta
      ?origins=45.5017,-73.5673
      &destinations=45.5081,-73.5550
      &traffic=true
      &key=<API_KEY>
```

Response — Google Distance Matrix JSON (unchanged).
```json
{
  "destination_addresses": ["155 Rue Notre Dame E, Montréal, QC"],
  "origin_addresses": ["René-Lévesque / Robert-Bourassa, Montréal, QC"],
  "rows": [
    {
      "elements": [
        {
          "distance": { "text": "2.0 km", "value": 1982 },
          "duration": { "text": "10 mins", "value": 595 },
          "status": "OK"
        }
      ]
    }
  ],
  "status": "OK"
}
```





### 2. Directions 

Supports **JSON body OR query parameters**.
POST https://maps.<stage>.goseanto.com/directions?key=<API_KEY>


Request
### ✅ Option A — JSON Body (recommended)
```json
{
  "origin": "45.5017,-73.5673",
  "destination": "45.5081,-73.5550",
  "mode": "driving",
  "traffic": true,
  "waypoints": ["45.5050,-73.5600"]
}
```
## ✅Option B — Query Parameters (Google-compatible)
```bash
GET https://maps.<stage>.goseanto.com/directions
      ?origin=45.5017,-73.5673
      &destination=45.5081,-73.5550
      &mode=driving
      &traffic=true
      &waypoints=45.5050,-73.5600
      &key=<API_KEY>
```
      
Response

Google Directions JSON (proxied directly).

## 🔄 Failover Pattern (Recommended)

Use our proxy as primary.
If any error → fallback to Google.

Pseudocode:
```js
{
  try {
    return callGoseanto();
} catch {
    return callGoogle();
}
}
```

Zero operational risk.

## 📊 Metrics & Transparency

Each API key gets daily counters:

total_requests

cache_hits

cache_misses

google_calls

fallback_uses

Useful for billing and optimization.

## 🧪 Testing Tools

Included in this repository:

postman/
examples/curl/


ETA test

Directions test

Traffic on/off

Waypoints tests

## 🧱 Architecture (High-Level)
┌──────────────────┐        ┌──────────────────┐
│    Your App       │ HTTPS │ Goseanto Gateway │
│ (CAD / Taxi / etc)├──────►│  /eta /directions│
└──────────┬────────┘        └─────────┬────────┘
           │                           │
           ▼                           ▼
     AWS Lambda (Go)            DynamoDB (cache+metrics)
           │                           │
           ▼                           ▼
                 Google Maps Distance & Directions

## 📦 Licensing & Access

This documentation is public.
Operational API usage requires an activated API key.