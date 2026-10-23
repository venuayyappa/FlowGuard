# FlowGuard — Customer Experience & Network Analytics Platform

FlowGuard is a full-stack analytics platform for monitoring network performance and customer experience (QoE). It processes network and application events through an event-driven pipeline, aggregates performance metrics, and exposes them through REST APIs and a React dashboard.

**Repository:** [github.com/venuayyappa/FlowGuard](https://github.com/venuayyappa/FlowGuard)

## Overview

FlowGuard is designed to help teams understand network quality by turning event-level measurements into useful performance indicators.

### Key capabilities

- Ingest network and application performance events through a REST endpoint.
- Stream events through Apache Kafka for asynchronous processing.
- Aggregate performance measurements into time windows.
- Calculate a Quality of Experience (QoE) score from network metrics.
- Store analytics in PostgreSQL and use Redis for caching.
- Explore KPI cards and time-series charts in a React dashboard.
- Filter dashboard results by region and application.

## Architecture

```text
Network / App Events
        |
        v
Event Ingestion Service
        |
        v
     Apache Kafka
        |
        v
   Analytics Service
        |
        +------> PostgreSQL
        |
        +------> Redis
                    |
                    v
              API Service
                    |
                    v
             React Dashboard
```

## Technology stack

| Layer | Technologies |
|---|---|
| Backend | Java 17, Spring Boot 3.3.5, Maven |
| Event streaming | Apache Kafka, Zookeeper |
| Data storage | PostgreSQL 16, Redis 7 |
| Frontend | React 18, Vite, Recharts |
| Infrastructure | Docker, Docker Compose, Nginx |

## Services

| Service | Port | Responsibility |
|---|---:|---|
| API Service | `8080` | Exposes REST endpoints for querying KPI data |
| Event Ingestion Service | `8081` | Accepts network events and publishes them to Kafka |
| Analytics Service | `8082` | Consumes events, aggregates metrics, calculates QoE and persists results |
| Dashboard Web | `3000` | Displays KPI cards, filters and time-series charts |

## Metrics

The platform focuses on the following measurements:

- Average latency (ms)
- Jitter (ms)
- Packet loss (%)
- Signal strength (dBm)
- Quality of Experience (QoE) score from 0 to 100

The QoE score uses a weighted calculation based on latency (40%), jitter (25%), packet loss (25%) and signal strength (10%). A higher score represents better estimated experience. This is a project-level scoring model, not a standardized carrier QoE measurement.

## Getting started

### Prerequisites

- Git
- Docker
- Docker Compose

### Run with Docker Compose

```bash
git clone https://github.com/venuayyappa/FlowGuard.git
cd FlowGuard
docker compose up --build
```

When the containers finish starting, open:

- Dashboard: <http://localhost:3000>
- API service: <http://localhost:8080>
- Event ingestion service: <http://localhost:8081>

> **Note:** The first startup may take a few minutes while Docker builds the services and downloads images. Confirm that all containers are healthy before testing the endpoints.

## Send a sample network event

```bash
curl -X POST http://localhost:8081/events/network \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "e-1001",
    "timestamp": "2025-11-22T20:00:00Z",
    "userId": "u-1",
    "region": "Dallas",
    "deviceType": "Android",
    "appName": "VideoStream",
    "latencyMs": 85.2,
    "jitterMs": 4.1,
    "packetLossPct": 0.4,
    "signalStrengthDbm": -82
  }'
```

## Query KPI data

Get the latest KPIs:

```bash
curl "http://localhost:8080/api/kpis/latest?limit=50"
```

Query a time range:

```bash
curl "http://localhost:8080/api/kpis/range?from=2025-11-22T00:00:00Z&to=2025-11-23T00:00:00Z"
```

## Dashboard

The React dashboard provides:

- KPI cards for QoE, latency, jitter and packet loss
- Time-series charts for viewing metric trends
- Region and application filters
- Periodic refresh of dashboard data

## Development

You can also run individual services locally. Start the supporting dependencies (Kafka, PostgreSQL and Redis) first, and configure each service with the appropriate connection settings.

### Build the Java services

Run these commands from each service directory:

```bash
mvn clean package
```

Then run the generated JAR from that service's `target/` directory. For example:

```bash
java -jar target/*.jar
```

### Run the frontend

```bash
cd dashboard-web
npm install
npm run dev
```

## Potential future improvements

- Add authentication and role-based authorization.
- Add configurable thresholds and alert notifications.
- Implement statistical anomaly detection for network degradation.
- Add regional performance heatmaps.
- Evaluate TimescaleDB for larger time-series workloads.
- Expand automated tests and add a continuous integration pipeline.


## Attribution

This project is adapted from the [Customer Experience & Network Analytics Platform](https://github.com/Akash5091/cx-network-analytics-platform). Refer to the upstream repository and its license for the original project's attribution and licensing terms.
