## NVIDIA GPU DCGM Exporter 

Berikut panduan lengkap dari persiapan hingga visualisasi metrik GPU NVIDIA menggunakan DCGM Exporter, Prometheus, dan Grafana dalam satu orkestrasi Docker Compose. Anda akan mempelajari cara memasang prasyarat, membuat file konfigurasi (`docker-compose.yml` dan `prometheus.yml`), menjalankan container, menghubungkan Grafana ke Prometheus, dan mengimpor dashboard NVIDIA DCGM (ID 12239). Setelah mengikuti langkah demi langkah ini, Anda dapat memantau metrik GPU seperti suhu, penggunaan memori, dan utilitas streaming multiprocessor secara realtime.

---

## 1. Prasyarat

1. **NVIDIA Container Toolkit**
   Pastikan Anda sudah menginstal NVIDIA Container Toolkit agar Docker dapat menjalankan container yang membutuhkan akses GPU ([GitHub][1]).
2. **Docker Engine & Docker Compose**
   Instal Docker Engine dan Docker Compose versi 3.x atau lebih baru, yang mendukung format Compose file 3.x ([GitHub][1]).
3. **Driver NVIDIA Terbaru**
   Sistem harus memiliki driver NVIDIA dan `nvidia-docker2` yang sesuai dengan GPU di host ([NVIDIA Docs][2]).

---

## 2. Struktur Direktori & File

Buat direktori proyek, lalu di dalamnya buat dua file penting:

```
gpu-monitoring/
├── docker-compose.yml
└── prometheus.yml
```

* **`docker-compose.yml`**: Mengorkestrasi container DCGM Exporter, Prometheus, dan Grafana.
* **`prometheus.yml`**: Konfigurasi scrape Prometheus untuk endpoint DCGM Exporter.

---

## 3. Konfigurasi `docker-compose.yml`

```yaml
version: '3.8'

services:
  dcgm-exporter:
    image: nvcr.io/nvidia/k8s/dcgm-exporter:4.2.3-4.1.1-ubuntu22.04
    container_name: dcgm-exporter
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              capabilities: [gpu]
    cap_add:
      - SYS_ADMIN
    restart: unless-stopped
    ports:
      - "9400:9400"
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
    ports:
      - "9090:9090"
    networks:
      - monitoring

  grafana:
    image: grafana/grafana-oss:latest
    container_name: grafana
    restart: unless-stopped
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    ports:
      - "3000:3000"
    depends_on:
      - prometheus
    networks:
      - monitoring

networks:
  monitoring:
    driver: bridge
```

* **`dcgm-exporter`**: menjalankan exporter dengan opsi `--gpus all` dan `SYS_ADMIN` untuk akses metrik DCGM ([NVIDIA Docs][2]).
* **Jaringan `monitoring`**: memudahkan service‐to‐service discovery lewat DNS internal Docker ([GitHub][1]).

---

## 4. Konfigurasi `prometheus.yml`

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'dcgm-exporter'
    static_configs:
      - targets: ['dcgm-exporter:9400']
```

* **`scrape_interval`**: Prometheus akan melakukan scrape setiap 15 detik ([help.ateliere.com][3]).
* **`targets`**: mengarah ke hostname service Docker `dcgm-exporter`, port 9400.

---

## 5. Menjalankan Layanan

Di dalam direktori `gpu-monitoring/`, jalankan:

```bash
docker compose up -d
```

Perintah ini akan menarik semua image yang dibutuhkan dan menjalankan ketiga container dalam mode detached ([GitHub][1]).

---

## 6. Verifikasi

1. **DCGM Exporter**

   ```bash
   curl http://localhost:9400/metrics
   ```

   Anda akan melihat metrik DCGM yang diekspos (gauge, histogram, dll.) ([GitHub][4]).
2. **Prometheus**
   Akses di browser: `http://localhost:9090/targets`.
   Pastikan job `dcgm-exporter` berstatus “UP” ([NVIDIA Docs][2]).
3. **Grafana**
   Buka `http://localhost:3000`, login dengan `admin`/`admin` ([GitHub][1]).

---

## 7. Menambahkan Data Source Prometheus di Grafana

1. Di sidebar Grafana: **Configuration** → **Data Sources** → **Add data source**.
2. Pilih **Prometheus**.
3. Pada **URL**, isi `http://prometheus:9090`.
4. Klik **Save & Test**, harus muncul “Data source is working” ([NVIDIA Docs][2]).

---

## 8. Mengimpor Dashboard NVIDIA DCGM (ID 12239)

1. Di Grafana: klik ikon **“+”** → **Import**.
2. Masukkan **12239** pada kolom “Import via grafana.com” → **Load** ([Grafana Labs][5]).
3. Pilih Data Source: **Prometheus**.
4. Klik **Import**.

Dashboard NVIDIA DCGM akan muncul dengan panel metrik GPU seperti memory usage, temperature, SM utilization, dan banyak lagi ([GitHub][6]).
### ✅ **Solusi Cepat: Impor Dashboard Secara Manual**

Berikut langkah-langkah untuk mengimpor dashboard **NVIDIA DCGM (ID 12239)** secara **manual**:

### 🧭 2. Buka Grafana → Import → Upload JSON

1. Buka Grafana di browser: `http://localhost:3000`
2. Masuk (default: `admin` / `admin`)
3. Klik **"+" (Plus)** → **Import**
4. Klik tombol **“Upload JSON file”** → pilih file `nvidia-dcgm-dashboard.json` yang sudah Anda unduh
5. Pilih data source → pilih `Prometheus`
6. Klik **Import**

---

### 🔽 1. Unduh Dashboard JSON dari Grafana.com

Klik link ini untuk membuka dashboard secara langsung:

📎 [https://grafana.com/grafana/dashboards/12239](https://grafana.com/grafana/dashboards/12239)

Lalu klik tombol **"Download JSON"** di kanan atas.

Atau gunakan terminal:

```bash
curl -o nvidia-dcgm-dashboard.json https://grafana.com/api/dashboards/12239/revisions/1/download
```

---

## 9. Langkah Tambahan & Tips

* **Persistensi data**: Untuk menyimpan data Prometheus/Grafana setelah restart, gunakan volume Docker secara eksplisit.
* **Provisioning**: Anda dapat menyimpan dashboard JSON dalam direktori provisioning Grafana agar di-load otomatis.
* **Alerting**: Konfigurasikan **Alertmanager** untuk menerima notifikasi bila metrik GPU melewati ambang tertentu.

Dengan mengikuti panduan ini, Anda akan memiliki sistem monitoring GPU NVIDIA yang lengkap, scalable, dan mudah di-maintain menggunakan Docker Compose. Selamat mencoba!

[1]: https://github.com/hongshibao/gpu-monitoring-docker-compose?utm_source=chatgpt.com "hongshibao/gpu-monitoring-docker-compose - GitHub"
[2]: https://docs.nvidia.com/datacenter/dcgm/latest/gpu-telemetry/dcgm-exporter.html?utm_source=chatgpt.com "DCGM-Exporter - NVIDIA Docs Hub"
[3]: https://help.ateliere.com/live/docs/installation/prometheus-grafana/?utm_source=chatgpt.com "Prometheus and Grafana for monitoring |"
[4]: https://github.com/NVIDIA/dcgm-exporter?utm_source=chatgpt.com "NVIDIA GPU metrics exporter for Prometheus leveraging DCGM"
[5]: https://grafana.com/grafana/dashboards/12239-nvidia-dcgm-exporter-dashboard/?utm_source=chatgpt.com "NVIDIA DCGM Exporter Dashboard | Grafana Labs"
[6]: https://github.com/NVIDIA/dcgm-exporter/blob/main/grafana/dcgm-exporter-dashboard.json?utm_source=chatgpt.com "dcgm-exporter-dashboard.json - GitHub"
Jika saat Anda mengimpor dashboard di Grafana menggunakan ID **12239** muncul kolom **“Need a dashboard JSON model”**, berarti Grafana tidak berhasil **mengambil JSON dashboard** dari `grafana.com`. Ini biasanya disebabkan oleh:

* Grafana container **tidak memiliki akses internet**, atau
* Ada masalah sementara dengan API `grafana.com`.

---

### ✅ Selesai

Dashboard NVIDIA DCGM seharusnya langsung muncul dan menampilkan metrik GPU seperti:

* Memory usage
* SM utilization
* Power draw
* GPU temperature
* Fan speed

---

### 💡 Tips Tambahan

Jika Anda ingin **otomatis mengimpor dashboard saat container Grafana berjalan**, saya bisa bantu membuatkan **provisioning file** agar dashboard langsung muncul tanpa klik manual.

Ingin dibuatkan provisioning otomatis juga?
