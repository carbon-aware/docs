# Prefect Integration with CarbonAware

The `carbonaware_prefect` package provides two easy‑to‑use interfaces for adding carbon‑aware delays to your Prefect flows and tasks:

1. **Decorator**: `@carbonaware_delay_decorator`  
2. **Task factory**: `carbonaware_delay_task(...)`

Use these to postpone execution of downstream work until a low‑carbon time window is available.

---

## Installation

```bash
pip install carbonaware-prefect
```

---

## 1. Carbon‑Aware Decorator

The `carbonaware_delay_decorator` can be used to apply a delay to any Prefect task, before it runs. If you’re using Prefect’s `@task` decorator, apply `@carbonaware_delay_decorator` **after** it:

```python
from datetime import timedelta
from prefect import flow, task
from carbonaware_prefect import carbonaware_delay_decorator

# This task will delay execution until a CO₂‑optimal window is available
@task
@carbonaware_delay_decorator(
    provider="gcp",             # Optional: "aws", "azure", or "gcp"
    region="us-central1",       # Optional: cloud region string
    window=timedelta(minutes=5),# Maximum wait time for an optimal slot
    duration=timedelta(minutes=30),
)
def train_model():
    print("✅ Training started at carbon‑optimal time!")
    # ... actual work here ...

@flow
def training_pipeline():
    print("🚀 Launching carbon‑aware training pipeline...")
    train_model()

if __name__ == "__main__":
    training_pipeline()
```

> Note: the delay runs in the same process as the task. This means that the worker running the task will be idle (sleeping) while waiting for a low-carbon time window to become available. It is highly encouraged to use as small a worker as possible to avoid excess idle resources.

### How it works

* Before `train_model()` runs, the decorator calls `carbonaware_delay(...)` under the hood.
* If a lower‑carbon slot is found within the `window`, execution is delayed until then.
* If the client can’t detect your region/provider (and `provider` and `region` are not specified), it logs a warning and proceeds immediately.

---

## 2. Carbon‑Aware Task Factory

If you prefer an explicit Prefect task that enforces a delay, use `carbonaware_delay_task` to generate one:

```python
from datetime import timedelta
from prefect import flow, task
from carbonaware_prefect import carbonaware_delay_task

@task
def train_model():
    print("✅ Training started at carbon‑optimal time!")
    # ... actual work here ...

@flow
def training_pipeline():
    print("🚀 Launching carbon‑aware training pipeline...")

    # Create a carbon‑aware delay task
    delay = carbonaware_delay_task(
        provider="gcp",
        region="us-central1",
        window=timedelta(minutes=5),
        duration=timedelta(minutes=30),
    )

    # Run the delay task first
    delay()

    # Then run your main task
    train_model()

if __name__ == "__main__":
    training_pipeline()
```

**Key points**

* `carbonaware_delay_task` returns a Prefect `@task` function.
* Calling that task blocks until a low‑carbon slot is available (or proceeds if undetectable).
* Use any Prefect task arguments via `**task_kwargs` if you need retries, tags, etc.

---

## Configuration Options

Both interfaces accept the same parameters:

| Parameter  | Type            | Default                 | Description                                                           |
| ---------- | --------------- | ----------------------- | --------------------------------------------------------------------- |
| `window`   | `timedelta`     | `timedelta(hours=6)`    | Max look‑ahead for optimal window                                     |
| `duration` | `timedelta`     | `timedelta(minutes=30)` | Estimated run time of your work                                       |
| `provider` | `str` or `None` | `None`                  | Cloud provider (`"aws"`, `"azure"`, `"gcp"`); auto‑detected if `None` |
| `region`   | `str` or `None` | `None`                  | Cloud region; auto‑detected if `None`                                 |

---

## Troubleshooting & Tips

* **Local development**: If you’re not in a cloud environment (on Azure, AWS, or GCP), set `provider` and `region` explicitly to avoid warnings.
* **Logging**: The decorator logs a warning if it can’t detect your environment; check Prefect logs for details.
* **Combining both**: You can mix and match—decorate a task with the decorator, or add a separate delay task in your flow.

---

## Next Steps

* See the [CarbonAware Prefect API reference](./api.md) for detailed function signatures.
* Return to the [Integrations overview](../index.md).
* Back to the [CarbonAware Docs Home](../../index.md).
