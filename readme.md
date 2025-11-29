Goseanto Maps Proxy — High-Reliability ETA & Directions Layer

A lightweight routing layer for public safety, dispatch, taxi, and logistics systems.

Designed for platforms that need reliable, predictable, and cost-efficient access to Google Maps Distance Matrix and Directions.

🚀 What the Goseanto Maps Proxy Does

Instead of every unit calling Google directly:

Unit → Google → $0.005–$0.01 per element
Unit → Google → $0.005–$0.01 per element
Unit → Google → $0.005–$0.01 per element
...


Your units call our gateway:

Unit → Goseanto Proxy → Google (once)
                       ↳ Shared, cached result (10–20 sec)


Same accuracy.
Same Google data.
Up to 60–90% cost reduction.

💰 Why This Saves Money
Google charges:

Per request

Often per element (origins × destinations)

Even if the result is identical to a call 1 second ago

Fleets have duplicate calls:

20–100 units asking the same ETA

Every 1–3 seconds

For the same incident, same streets, same hospitals

What we do:

First request → goes to Google (billed normally)

Next 10–20 seconds → served from cache

Cache window is shorter than Google’s own traffic update cycle

So accuracy is preserved, but cost drops dramatically.

🎯 Why Accuracy Stays 100%

Traffic data does NOT update every second.
Google’s traffic feed updates every 2–7 minutes (varies by city).

Our cache TTL for traffic is:

10–20 seconds


Meaning:

Your units will never see stale data

Google would return the same ETA if you called them again during this window

We stay well inside Google’s own accuracy envelope.

🛡️ Reliability & Failover

We return an ETA even if:

Google times out

Google returns 5xx

Network issues occur

Your region overloads

Fallback behavior:

If Google fails:
    return last known good ETA (stale-but-safe)


Dispatch systems will never freeze waiting for a Google response.

🔑 Per-Client API Keys

Each customer receives a dedicated API key, e.g.:

EMERES-STG-123
TAXI-CA-001
LOGISTICS-EU-555


Used for:

Authentication

Individual metrics

Client-level throttling

Client-level API key rotation

No customer can see another customer’s traffic.

🌍 Regional Deployment

To meet compliance requirements:

✔ Canada (ca-central-1) — PIPEDA-compliant
✔ US regions — HIPAA-ready structure
✔ EU regions — GDPR alignment
✔ Additional regions available on request

Data residency is fully controlled.

🧭 Supported Sectors

The system is used for:

EMS / Paramedic services

Police dispatch (CAD)

Fire services

Taxi / ride-hailing

Logistics & last-mile delivery

Municipal operations

These sectors hit Google Maps heavily — we stabilize performance and slash costs.

⚙️ API Usage
1. ETA Endpoint
POST https://maps.<stage>.goseanto.com/eta?key=<API_KEY>

Request (JSON)
{
  "origins": ["45.5017,-73.5673"],
  "destinations": ["45.5081,-73.5550"],
  "traffic": true
}

Traffic behavior

true → live traffic (Google: departure_time=now, traffic_model=best_guess)

false → non-traffic ETA (longer TTL, cheaper)

Response

Google Distance Matrix JSON, unchanged.

2. Directions Endpoint
POST https://maps.<stage>.goseanto.com/directions?key=<API_KEY>

Request (JSON)
{
  "origin": "45.5017,-73.5673",
  "destination": "45.5081,-73.5550",
  "mode": "driving",
  "traffic": true,
  "waypoints": ["45.5050,-73.5600"]
}

Response

Google Directions JSON, proxied as-is.

🔄 Failover Strategy (recommended)

Your application should:

Call Goseanto (primary)

If any error occurs → fallback to Google

Continue as normal

Pseudocode:

try {
    return callGoseanto();
} catch {
    return callGoogle();
}


Zero operational risk.

📊 What You See in Metrics

Every client has its own counters:

total_requests

cache_hits

cache_misses

google_calls

fallback_uses

Stored daily:

metrics#client:EMERES-STG-123#2025-11-28


Useful for billing transparency and usage analysis.

🧪 Test Tools

Included:

/postman/gos-maps.postman_collection.json
/examples/curl/eta.sh
/examples/curl/directions.sh

🧱 Architecture (High-Level)
┌───────────────┐          ┌──────────────┐
│   Your App     │  HTTPS   │ Goseanto API │
│ (CAD / Taxi)   ├─────────►│  Gateway     │
└───────┬────────┘          └──────┬───────┘
        │                           │
        │                           ▼
        │                   ┌──────────────┐
        │                   │ AWS Lambda   │
        │                   │ (Go runtime) │
        │                   └──────┬───────┘
        │                          │
        │                          ▼
        │                   ┌──────────────┐
        │                   │ DynamoDB     │
        │                   │ Cache+Metrics│
        │                   └──────┬───────┘
        │                          │
        │                          ▼
        │                   ┌──────────────┐
        └──────────────────►│ Google Maps  │
                            │ Distance+Dir │
                            └──────────────┘

📦 Licensing & Contact

Open for evaluation and technical testing.
Commercial use requires API key activation.
