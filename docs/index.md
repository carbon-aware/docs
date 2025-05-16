# Welcome to CarbonAware Docs

## What is CarbonAware?

CarbonAware is a suite of open-source tools and libraries designed to help developers build applications that can reduce their carbon footprint by making intelligent decisions based on real-time and forecasted carbon intensity data.

By integrating carbon awareness into your applications, you can:

- Schedule workloads during times of cleaner energy
- Choose regions with lower carbon intensity for your deployments
- Optimize resource usage based on environmental impact
- Track and report on carbon savings for sustainability initiatives

## Core Components

### Scheduler

The [CarbonAware Scheduler](scheduler/index.md) is a service that helps you find the optimal time and location to run your computing workloads to minimize carbon emissions. It provides an API that allows you to query for optimal scheduling times based on forecasted carbon intensity data across different cloud regions.

#### Key Capabilities

- Determine the best time to run workloads within specified time windows
- Compare carbon intensity across multiple cloud providers (AWS, Azure, GCP)
- Calculate potential carbon savings compared to worst-case scheduling
- Support for both batch jobs and interactive workloads

#### Available Client Libraries

- [Python SDK](scheduler/python/index.md) - A full-featured Python client with both synchronous and asynchronous APIs

## Getting Started

The easiest way to get started with CarbonAware is to use one of our client libraries. Check out the [Scheduler Python SDK](scheduler/python/index.md) for examples and API reference.

## Contributing

CarbonAware is an open-source project, and we welcome contributions from the community. Whether you're interested in adding new features, fixing bugs, or improving documentation, your help is appreciated.
