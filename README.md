# 📊 Monitoring Stack with Prometheus, Grafana, Loki

This project provides a full monitoring stack using Prometheus, Grafana, Loki, and Promtail using Docker Compose. It also includes system monitoring from the host using `node_exporter` and `promtail` as systemd services.
The repository provides a complete observability stack using **Docker Compose**. It includes:

- **Prometheus** for metrics collection
- **Grafana** for visualization
- **Loki** for log aggregation
- **Promtail** for sending logs to Loki
- **Node_exporter** for sending metrics to Prometheus

---

## 🧱 Services
| Service       | Port | Description                              |
| ------------- | ---- | ---------------------------------------- |
| Prometheus    | 9090 | Metrics collection and monitoring        |
| Grafana       | 3300 | Metrics and logs visualization           |
| Loki          | 3400 | Log storage and querying backend         |
| Node Exporter | 9100 | Host metrics exporter for Prometheus     |
| Promtail      | 9080 | Log collection agent, sends logs to Loki |


---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/rajibconf/monitoring.git
cd monitoring
```

2. Set up environment variables
Copy the example .env and configure if needed:

```bash
cp .env.example .env
```

⚙️ Setup on Host Machine
---
📁 3. Install Binaries on Host
---
Make sure node_exporter and promtail binaries are available in /usr/local/bin.
```bash
# Example for Node Exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.9.1.linux-amd64.tar.gz
sudo mv node_exporter-1.9.1.linux-amd64/node_exporter /usr/local/bin/
```

```bash
# Example for Promtail
wget https://github.com/grafana/loki/releases/latest/download/promtail-linux-amd64.zip
unzip promtail-linux-amd64.zip
chmod +x promtail-linux-amd64
sudo mv promtail-linux-amd64 /usr/local/bin/promtail
```

🛠️ 4. Configure Systemd Services
Copy systemd unit files:
```bash
sudo cp systemd/node_exporter.service /etc/systemd/system/node_exporter.service
sudo cp systemd/promtail.service /etc/systemd/system/promtail.service
```
Enable and start the services:
```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload

sudo systemctl enable node_exporter
sudo systemctl start node_exporter

sudo systemctl enable promtail
sudo systemctl start promtail
```

Check status:
```bash
sudo systemctl status node_exporter
sudo systemctl status promtail
```

📦 5. Docker Compose Services
```bash
docker compose up -d
```
6. Access the services
- Grafana: http://localhost:3300
- Prometheus: http://localhost:9090
- Loki: http://localhost:3400 (API only)

🔐 Grafana Login
Default credentials:
```pgsql
Username: admin
Password: admin
```
⚠️ Make sure to change the password after first login.

📂 File Structure
```bash
.
├── docker-compose.yml         # All services defined here
├── prometheus/
│   └── prometheus.yml         # Prometheus config
├── promtail/
│   └── promtail-config.yml    # Promtail config
├── systemd/                   # systemd unit files for host setup
    └── node_exporter.service
    └── promtail.service
├── nginx/                     # nginx config file for host setup
    └── grafana.conf
├── .env.example               # Sample environment variables
└── README.md                  # intructions
```

📁 nginx/grafana.conf (Host Machine Configuration)
---
This Nginx configuration file sets up HTTPS reverse proxying for the Grafana dashboard running on your server.

🔧 Instructions:
- Location: This file resides in the nginx/ directory of your project.
- Usage: It must be used on the host machine, not inside a container.
- Change the SSL certificate & domain to the correct one in nginx/grafana.conf file.
- Copy the file to the Nginx configuration folder on your host machine:
```bash
sudo cp nginx/grafana.conf /etc/nginx/conf.d/grafana.conf
```
- Test the Nginx configuration to make sure everything is okay:
```bash
sudo nginx -t
```
- Reload or restart Nginx:
```bash
sudo systemctl reload nginx
# or if Nginx isn't running yet:
sudo systemctl start nginx
```

📈 Dashboards
- Grafana includes support for:
- Prometheus metrics (Node Exporter, container metrics)
- Loki logs via Promtail
- You can import dashboards from Grafana Dashboards. ID: 1860

🛠️ Customization
- Add new targets in prometheus/prometheus.yml
- Configure new log paths in promtail/promtail-config.yml

🔒 Notes
- Promtail scrapes logs and sends to Loki.
- Node Exporter exposes system metrics on port 9100.
- Prometheus scrapes Node Exporter on host IP.
- Grafana is available on http://localhost:3000 (default login: admin/admin).
