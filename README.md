# 📈 Monitoring Stack — Prometheus + Grafana + Node Exporter

A simple, containerized monitoring stack built with **Prometheus**, **Grafana**, and **Node Exporter** for real-time server metrics visualization.  
Supports both **Linux** 🐧 and **Windows (WSL2)** 🪟 environments through dedicated Docker Compose configurations.

---

## 🔧 Tech Stack

| Component | Description |
|------------|-------------|
| **Prometheus** | Time-series database for metrics collection and alerting |
| **Grafana** | Visualization and analytics dashboard platform |
| **Node Exporter** | Exposes system-level metrics (CPU, memory, disk, network, etc.) for Linux/WSL |
| **Windows Exporter (optional)** | Exposes native Windows metrics if running outside WSL |
| **Docker + Compose** | Manages the full containerized environment |
| **WSL2** | Enables near-native Linux performance on Windows |

---

## ✨ Features

- 📊 **Real-time system metrics** (CPU, memory, disk, network)
- ⚙️ **Persistent data storage** using Docker volumes
- 🔄 **Auto-refreshing dashboards** via Grafana
- 🧠 **Ready-to-use Node Exporter Dashboard (ID: 1860)**
- 🪟 **Cross-platform** — compatible with both Windows and Linux
- 🚀 **Modular** — easily extendable with Alertmanager, Loki, cAdvisor, etc.

---

## 🧰 Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/)
- **Windows users**:
  - [WSL2](https://learn.microsoft.com/en-us/windows/wsl/)
  - [Chocolatey](https://chocolatey.org/) *(optional, for Windows Exporter)*

---

## ⚙️ Setup & Usage

### 🐧 For Linux

```bash
docker-compose -f docker-compose-l.yml up -d
```

### 🪟 For Windows (WSL2)

```bash
docker-compose -f docker-compose-w.yml up -d
```

---

## 📊 Access the Services

| Service | URL | Default Credentials |
|----------|-----|---------------------|
| **Grafana** | [http://localhost:3000](http://localhost:3000) | `admin / admin` |
| **Prometheus** | [http://localhost:9090](http://localhost:9090) | — |
| **Node Exporter** | [http://localhost:9100/metrics](http://localhost:9100/metrics) | Raw metrics endpoint |

---

## 🧾 Prometheus Configuration

Located at:  
```
prometheus/prometheus.yml
```

Example:
```yaml
global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['prometheus:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['node_exporter:9100']
```

---

## 📦 Volumes

| Volume | Purpose |
|---------|----------|
| **prometheus_data** | Persists Prometheus time-series data |
| **grafana_data** | Persists Grafana dashboards, users, and settings |

To remove all data:
```bash
docker compose down -v
```

---

## 🧠 Adding the Dashboard

1. Open Grafana at [http://localhost:3000](http://localhost:3000)  
2. Navigate to **Dashboards → Import**
3. Enter dashboard ID: **1860**
4. Click **Load**
5. Select the **Prometheus** data source
6. 🎉 Enjoy live CPU, Memory, Disk, and Network metrics

---

## ⚙️ Troubleshooting

| Issue | Possible Fix |
|--------|---------------|
| `connection refused` between Grafana ↔ Prometheus | Ensure Grafana uses `http://prometheus:9090` as data source URL |
| Node Exporter DOWN in Prometheus targets | Check that `node_exporter` container is running (`docker ps`) |
| Metrics not updating | Verify time range in Grafana dashboard (set to “Last 15m”) |
| Error mounting `/` on Windows | Use `/c:/host:ro` instead of `/:/host:ro,rslave` in Windows compose file |

---

## 🪟 Optional: Windows Exporter (Native Windows Metrics)

If you want to monitor your **Windows host** (not just WSL):

1. Install Windows Exporter via PowerShell:
   ```powershell
   choco install windows-exporter
   ```
2. Update `prometheus.yml`:
   ```yaml
   - job_name: 'windows'
     static_configs:
       - targets: ['host.docker.internal:9182']
   ```
3. Restart Prometheus:
   ```bash
   docker restart prometheus
   ```

4. Import Grafana dashboard ID **21266** for Windows metrics visualization.
