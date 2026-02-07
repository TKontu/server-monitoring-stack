# 🖥️ System Monitoring Stack

A comprehensive Docker Compose stack for monitoring your computer's status including CPU, RAM, disk space, GPU, temperatures, and processes.

## 📊 What's Included

| Service | Port | Description |
|---------|------|-------------|
| **Glances** | [61208](http://localhost:61208) | Real-time system monitor with web UI |
| **Grafana** | [3000](http://localhost:3000) | Beautiful dashboards & visualization |
| **Prometheus** | [9090](http://localhost:9090) | Metrics collection & storage |
| **Node Exporter** | 9100 | Detailed host metrics |
| **cAdvisor** | 8080 | Container metrics |

## 🚀 Quick Start

```bash
# Clone or download this folder
cd monitoring-stack

# Start all services
docker compose up -d

# View logs
docker compose logs -f
```

## 🌐 Access the Web UIs

1. **Glances** (instant view): http://localhost:61208
   - Real-time CPU, RAM, disk, network, processes, temperatures
   - No login required

2. **Grafana** (dashboards): http://localhost:3000
   - Username: `admin`
   - Password: `admin` (change in `.env` file)
   - Pre-configured dashboard included!

3. **Prometheus** (raw metrics): http://localhost:9090

## 📈 Monitored Metrics

### CPU
- Usage per core
- Load averages (1m, 5m, 15m)
- Context switches
- Interrupts

### Memory
- Total, used, available
- Cached, buffers
- Swap usage

### Disk
- Space usage per mount
- I/O read/write rates
- IOPS

### Network
- Traffic per interface
- Packets, errors, drops

### Temperature
- CPU cores
- GPU (if available)
- Other sensors via hwmon

### Processes
- Running/blocked count
- Top consumers

### GPU (NVIDIA)
See GPU section below for setup.

## 🎮 NVIDIA GPU Monitoring

For NVIDIA GPU monitoring, you need:

1. **NVIDIA Container Toolkit** installed:
   ```bash
   # Ubuntu/Debian
   distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
   curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
   curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
     sudo tee /etc/apt/sources.list.d/nvidia-docker.list
   sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
   sudo systemctl restart docker
   ```

2. **Uncomment GPU section** in `docker-compose.yml`:
   ```yaml
   deploy:
     resources:
       reservations:
         devices:
           - driver: nvidia
             count: all
             capabilities: [gpu]
   ```

## 🔧 Configuration

### Environment Variables (`.env`)

```bash
TZ=UTC                    # Your timezone
GRAFANA_USER=admin        # Grafana admin username
GRAFANA_PASSWORD=admin    # Grafana admin password (CHANGE THIS!)
```

### Data Retention

By default, Prometheus retains 30 days of metrics. Modify in `docker-compose.yml`:
```yaml
command:
  - '--storage.tsdb.retention.time=30d'  # Change to desired retention
```

## 📁 Directory Structure

```
monitoring-stack/
├── docker-compose.yml          # Main compose file
├── .env                        # Environment variables
├── README.md                   # This file
├── prometheus/
│   └── prometheus.yml          # Prometheus scrape config
└── grafana/
    ├── provisioning/
    │   ├── datasources/
    │   │   └── datasources.yml # Auto-configure Prometheus
    │   └── dashboards/
    │       └── dashboards.yml  # Dashboard provisioning
    └── dashboards/
        └── system-monitoring.json  # Pre-built dashboard
```

## 🛑 Stop & Cleanup

```bash
# Stop services (keep data)
docker compose down

# Stop and remove all data
docker compose down -v
```

## 🔍 Troubleshooting

### No temperature data?
- Ensure `lm-sensors` is installed on host: `sudo apt install lm-sensors`
- Run `sudo sensors-detect` and follow prompts
- Node Exporter needs access to `/sys` which is mounted read-only

### Glances not starting?
- Check if port 61208 is available
- Verify Docker socket permissions

### No GPU metrics?
- Ensure NVIDIA drivers are installed
- Install nvidia-container-toolkit
- Uncomment GPU section in docker-compose.yml

### Permission denied?
```bash
# Add user to docker group
sudo usermod -aG docker $USER
# Logout and back in
```

## 📚 Additional Resources

- [Glances Documentation](https://nicolargo.github.io/glances/)
- [Grafana Dashboards](https://grafana.com/grafana/dashboards/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Node Exporter Metrics](https://github.com/prometheus/node_exporter)

## 🎨 Importing More Dashboards

Grafana has thousands of community dashboards. Popular ones for this stack:

1. Go to Grafana → Dashboards → Import
2. Enter dashboard ID:
   - **1860** - Node Exporter Full
   - **893** - Docker and System Monitoring
   - **11074** - Node Exporter for Prometheus

---

**Happy Monitoring!** 🎉
