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

## Directory Structure

```
gpu-monitoring/
├── docker-compose.yaml
├── prometheus.yml
└── nvidia-dcgm-dashboard.json (optional, for manual import)
```

---

## How to Run and Verify

### 1. Start the Services

In your project directory, run:

```bash
docker compose up -d
```

This command will download all required images and start the three containers in detached mode.

---

### 2. Verify the Services

**DCGM Exporter**

Check the metrics endpoint:

```bash
curl http://localhost:9400/metrics
```

You should see DCGM metrics (gauge, histogram, etc.) in the output.

**Prometheus**

Open in your browser: [http://localhost:9090/targets](http://localhost:9090/targets)

Make sure the job `dcgm-exporter` is marked as “UP”.

**Grafana**

Open [http://localhost:3000](http://localhost:3000) and log in with `admin` / `admin`.

---

### 3. Add Prometheus Data Source in Grafana

1. In Grafana sidebar: go to **Configuration → Data Sources → Add data source**.
2. Select **Prometheus**.
3. For **URL**, enter: `http://prometheus:9090`.
4. Click **Save & Test**. You should see “Data source is working”.

---

### 4. Import the NVIDIA DCGM Dashboard

1. In Grafana, click the **“+”** icon → **Import**.
2. Enter `12239` in the “Import via grafana.com” field and click **Load**.
3. Select **Prometheus** as the data source.
4. Click **Import**.

The NVIDIA DCGM dashboard will appear, showing GPU metrics like memory usage, temperature, SM utilization, and more.

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
