# Airflow Integration with CarbonAware

## What is Airflow?

Apache Airflow is a workflow orchestration platform for authoring, scheduling, and monitoring data pipelines. It uses DAGs (Directed Acyclic Graphs) defined in Python to express task dependencies and scheduling logic.

## Why CarbonAware in Airflow?

CarbonAware helps you minimize the carbon footprint of your data pipelines by scheduling computational tasks when grid carbon intensity is at its lowest. Integrating CarbonAware with Airflow empowers engineers to automatically defer workloads to the most environmentally friendly execution windows without manual intervention.

---

## Installation

Install the provider package via pip:

```bash
pip install airflow-provider-carbonaware
```

---

## Usage

### Basic Usage

```python
from pendulum import datetime as pendulum_datetime
from airflow.decorators import dag
from airflow.operators.bash import BashOperator
from airflow_provider_carbonaware.operators.carbonaware import CarbonAwareOperator

@dag(
    start_date=pendulum_datetime(2023, 1, 1),
    schedule=None,
    default_args={"retries": 1},
    tags=["carbon-aware"],
)
def example_carbonaware_dag():
    setup = BashOperator(
        task_id="setup",
        bash_command="echo 'Start setup'"
    )

    find_slot = CarbonAwareOperator(
        task_id="find_carbon_slot",
        execution_window_minutes=120,
        task_duration_minutes=30,
        zone={"provider": "aws", "region": "us-east-1"}
    )

    process = BashOperator(
        task_id="process",
        bash_command="echo 'Processing data'"
    )

    setup >> find_slot >> process

example_carbonaware_dag_instance = example_carbonaware_dag()
```

### Advanced Usage

#### 1. Automatic Region & Provider Detection

By default, if you omit the `zone` parameter, the operator will introspect cloud metadata (AWS IMDSv2, GCP, Azure) to determine your execution environment:

```python
find_slot = CarbonAwareOperator(
    task_id="find_carbon_slot",
    execution_window_minutes=180,
    task_duration_minutes=45,
    # No `zone` passed — autodetection kicks in
)
```

If you are not running your workload on one of the above cloud providers, set zone to the provider and region that is closest to the location where your code is running. To add support for a new cloud provider or zone, submit a [ticket](https://github.com/carbon-aware/scheduler/issues).

#### 2. Tuning `execution_window_minutes`

Selecting an appropriate window size helps balance delay vs. carbon savings:

* **Shorter windows (e.g., 30 minutes)**: Limits your task delay to under a half hour while capturing moderate carbon improvements.
* **Long windows (e.g., 12 hours)**: Maximizes carbon reduction but may postpone your workload by many hours.

---

## Under the Hood

1. **CarbonAwareScheduler client**: The operator calls the Python SDK to fetch the optimal start time within your window based on carbon intensity forecasts. This utilizes [WattTime](https://watttime.org) to get carbon intensity forecasts.
2. **Deferrable operator**: Leveraging Airflow’s deferrable framework, the operator releases the worker slot while waiting and resumes at the scheduled time.

---

## Next Steps

* Explore the [API reference](./api.md) for full parameter details.
* Try integrating with other orchestration tools (e.g., [Prefect](../prefect/index.md)).
* Contribute to the provider on [GitHub](https://github.com/carbon-aware/airflow-provider-carbonaware).
