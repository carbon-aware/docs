# CarbonAware Scheduler

## Overview

The CarbonAware Scheduler is a service designed to help reduce the carbon footprint of computing workloads by scheduling them during times of lower carbon intensity. It provides an API that allows you to find optimal execution times for your workloads based on forecasted carbon intensity data across different cloud regions.

## Key Features

- **Carbon-aware scheduling**: Determine the best time to run workloads to minimize carbon emissions
- **Multi-region support**: Compare carbon intensity across AWS, Azure, and GCP regions
- **Flexible time windows**: Define multiple time windows for potential execution
- **Comparative metrics**: View carbon savings compared to worst-case, median, and naive scheduling
- **Simple REST API**: Easy to integrate with existing systems and workflows

## How It Works

1. You define a job with a specific duration (e.g., "1 hour")
2. You specify one or more time windows when the job could run
3. You select one or more cloud regions where the job could be executed
4. The scheduler returns the optimal time and region to run your job to minimize carbon emissions

## Benefits

- **Reduce carbon footprint**: Lower the environmental impact of your computing workloads
- **Cost optimization**: Often, lower carbon intensity periods correlate with lower energy costs
- **Sustainability reporting**: Track potential carbon savings for sustainability initiatives
- **Cloud flexibility**: Compare emissions across different cloud providers and regions

## Client Libraries

- [Python](python/index.md) - A full-featured Python SDK with both synchronous and asynchronous clients

## Getting Started

The easiest way to get started is by using one of our client libraries. See the Python SDK documentation for examples and API reference.
