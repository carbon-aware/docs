# Python SDK for CarbonAware Scheduler

The CarbonAware Python SDK provides a simple interface to query optimal scheduling times based on carbon intensity in different regions. It supports both **synchronous** and **asynchronous** clients.

This guide covers:

- Installing the SDK  
- Creating a scheduler client  
- Listing available regions  
- Finding the optimal schedule for a job  
- **Advanced** usage examples  

---

## Installation

```bash
pip install carbonaware-scheduler-client
```

---

## Getting Started

### 1. Create a Scheduler Client (Sync)

```python
from carbonaware_scheduler import CarbonawareScheduler

# synchronous client
client = CarbonawareScheduler()
```

> By default, `client` points to `https://scheduler.carbonaware.dev`.
> If you're self-hosting the scheduler, you can override the base URL with the `CARBONAWARE_SCHEDULER_BASE_URL` environment variable or with `base_url="https://your-url"` when initializing the client.

---

### 2. List Available Regions

```python
# GET /v0/regions/
regions_response = client.regions.list()
for zone in regions_response.regions:
    print(f"{zone.provider}:{zone.region}")
```

Sample output:

```
aws:us-east-1
azure:eastus
...
gcp:us-central1
```

---

### 3. Find the Optimal Schedule for a Job

```python
from datetime import datetime, timedelta
from carbonaware_scheduler import CarbonawareScheduler

# create client
client = CarbonawareScheduler()

# prepare a single time window
now = datetime.utcnow()
window = {
    "start": now,
    "end":   (now + timedelta(hours=6))
}

# POST /v0/schedule/
schedule_response = client.schedule.create(
    duration="PT1H",
    windows=[window],
    zones=[{"provider": "aws", "region": "us-east-1"}]
)

# ideal option
ideal = schedule_response.ideal
print(f"Ideal: {ideal.time} in {ideal.zone.provider}:{ideal.zone.region}")
```

---

## Advanced Usage

### A. Compare Across Multiple Regions

```python
schedule_response = client.schedule.create(
    duration="PT1H",
    windows=[window],
    zones=[
        {"provider": "aws",   "region": "us-east-1"},
        {"provider": "azure", "region": "westeurope"},
        {"provider": "gcp",   "region": "us-central1"},
    ]
)
print(f"Best: {schedule_response.ideal.time} in {schedule_response.ideal.zone.region}")
```

---

### B. Multiple Non‑contiguous Time Windows

```python
from carbonaware_scheduler import CarbonawareScheduler

client = CarbonawareScheduler()

now = datetime.utcnow()
windows = [
    {
        "start": now,
        "end": (now + timedelta(hours=1))
    },
    {
        "start": (now + timedelta(hours=3)),
        "end": (now + timedelta(hours=5))
    }
]

schedule_response = client.schedule.create(
    duration="PT45M",
    windows=windows,
    zones=[{"provider": "aws", "region": "us-west-2"}]
)

print(f"Ideal across windows: {schedule_response.ideal.time}")
```

---

### C. Inspect Multiple Scheduling Options

```python
# request top 3 options
schedule_response = client.schedule.create(
    duration="PT30M",
    windows=[window],
    zones=[{"provider": "aws", "region": "us-east-1"}],
    num_options=3
)

for opt in schedule_response.options:
    print(f"{opt.time} — zone={opt.zone.region} — co2={opt.co2_intensity} gCO₂/kWh")
```

This returns:

* `.ideal` — the best single slot
* `.options` — a list of the next-best slots
* `.naive_case`, `.worst_case`, `.median_case`, and `.carbon_savings` for comparative context

---

## Async Client (Optional)

If you prefer **async I/O**, swap in `AsyncCarbonawareScheduler`:

```python
from carbonaware_scheduler import AsyncCarbonawareScheduler

async def main():
    client = AsyncCarbonawareScheduler()
    regions = (await client.regions.list()).regions
    print(regions)

# run with: asyncio.run(main())
```

The async client methods mirror the sync ones exactly.

---

## Next Steps

* Return to the [Scheduler Overview](../index.md).
* Back to the [CarbonAware Docs Home](../../index.md).
