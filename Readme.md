<p align="center">
  <img src="img/Screenshot from 2025-05-15 18-12-07.png" alt="NVIDIA DCGM Grafana Dashboard" width="800"/>
</p>

# NVIDIA GPU DCGM Exporter Monitoring Stack

A complete solution for monitoring NVIDIA GPU metrics using DCGM Exporter, Prometheus, and Grafana, orchestrated with Docker Compose. This stack enables real-time visualization of GPU metrics such as temperature, memory usage, and SM utilization.

---

## Features

- **Automated deployment** of DCGM Exporter, Prometheus, and Grafana via Docker Compose
- **Pre-configured Prometheus** scraping for GPU metrics
- **Ready-to-import Grafana dashboard** for NVIDIA DCGM (ID 12239)
- **Step-by-step instructions** for setup and troubleshooting

---

## Prerequisites

- **NVIDIA Container Toolkit** installed on the host ([NVIDIA Toolkit Docs](https://github.com/NVIDIA/nvidia-docker))
- **Docker Engine** and **Docker Compose** (version 3.x or newer)
- **Latest NVIDIA GPU drivers** and `nvidia-docker2` installed

---

## Directory Structure

```
gpu-monitoring/
├── docker-compose.yaml
├── prometheus.yml
└── nvidia-dcgm-dashboard.json (optional, for manual import)
```

---

## Quick Start

1. **Clone this repository** and navigate to the project directory.
2. **Start all services**:
   ```bash
   docker compose up -d
   ```
3. **Verify services**:
   - DCGM Exporter: [http://localhost:9400/metrics](http://localhost:9400/metrics)
   - Prometheus: [http://localhost:9090/targets](http://localhost:9090/targets)
   - Grafana: [http://localhost:3000](http://localhost:3000) (default login: `admin` / `admin`)

---

## Configuration

### docker-compose.yaml

Orchestrates the DCGM Exporter, Prometheus, and Grafana containers. GPU access is enabled for the exporter.

### prometheus.yml

Configures Prometheus to scrape metrics from DCGM Exporter every 15 seconds.

---

## Importing the NVIDIA DCGM Dashboard

### Automatic (Recommended)

1. In Grafana, go to **+ → Import**.
2. Enter dashboard ID: `12239` and click **Load**.
3. Select **Prometheus** as the data source and click **Import**.

### Manual

1. Download the dashboard JSON:
   ```bash
   curl -o nvidia-dcgm-dashboard.json https://grafana.com/api/dashboards/12239/revisions/1/download
   ```
2. In Grafana, go to **+ → Import → Upload JSON file** and select `nvidia-dcgm-dashboard.json`.
3. Select **Prometheus** as the data source and click **Import**.

---

## Troubleshooting

- If dashboard import fails with "Need a dashboard JSON model", ensure the Grafana container has internet access or use the manual import method.
- For persistent data, configure Docker volumes for Prometheus and Grafana.
- For automated dashboard provisioning, place the dashboard JSON in Grafana's provisioning directory.

---

## References

- [NVIDIA DCGM Exporter Documentation](https://docs.nvidia.com/datacenter/dcgm/latest/gpu-telemetry/dcgm-exporter.html)
- [Prometheus Documentation](https://prometheus.io/docs/introduction/overview/)
- [Grafana Documentation](https://grafana.com/docs/grafana/latest/)
- [Official Dashboard 12239](https://grafana.com/grafana/dashboards/12239)

---

## License

MIT
