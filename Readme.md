# NVIDIA GPU DCGM Exporter Monitoring Stack

## Repository Highlights

- Real-time NVIDIA GPU monitoring with DCGM Exporter
- Automated deployment using Docker Compose
- Pre-configured Prometheus for GPU metrics scraping
- Ready-to-use Grafana dashboard (ID 12239) for visualization
- Easy setup and verification steps
- Troubleshooting and manual dashboard import guidance
- Extensible for alerting and persistent data storage

<p align="center">
  <img src="img/Screenshot from 2025-05-15 18-12-07.png" alt="NVIDIA DCGM Grafana Dashboard" width="800"/>
</p>

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

1. **Install Requirements**
   - Make sure you have:
     - [Docker](https://docs.docker.com/get-docker/)
     - [NVIDIA drivers](https://www.nvidia.com/Download/index.aspx)
     - [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

2. **Download this project**
   - Click the green "Code" button on GitHub, then "Download ZIP" or use:
     ```bash
     git clone https://github.com/yourusername/NVIDIA-DCGM-Exporter.git
     cd NVIDIA-DCGM-Exporter
     ```

3. **Start Monitoring**
   - Run this command in your project folder:
     ```bash
     docker compose up -d
     ```

4. **Open the dashboards**
   - DCGM Exporter: [http://localhost:9400/metrics](http://localhost:9400/metrics)
   - Prometheus: [http://localhost:9090/targets](http://localhost:9090/targets)
   - Grafana: [http://localhost:3000](http://localhost:3000) (login: `admin` / `admin`)

---

## See Your GPU Dashboard

1. In Grafana, click **+** (left menu) → **Import**.
2. Enter `12239` and click **Load**.
3. Choose **Prometheus** as the data source.
4. Click **Import**. Now you can see your GPU stats!

---

## Need Help?

- If you see errors, check that your NVIDIA drivers and Docker are installed.
- If the dashboard does not load, try to import it manually:
  1. Download the dashboard:
     ```bash
     curl -o nvidia-dcgm-dashboard.json https://grafana.com/api/dashboards/12239/revisions/1/download
     ```
  2. In Grafana, click **+ → Import → Upload JSON file** and select the file.

---

## Useful Links

- [NVIDIA DCGM Exporter Docs](https://docs.nvidia.com/datacenter/dcgm/latest/gpu-telemetry/dcgm-exporter.html)
- [Prometheus Docs](https://prometheus.io/docs/introduction/overview/)
- [Grafana Docs](https://grafana.com/docs/grafana/latest/)
- [Official Dashboard 12239](https://grafana.com/grafana/dashboards/12239)

---

## License

MIT
