# Grafana Lab – Monitoring Stack with Docker Compose (Grafana, Prometheus & Node Exporter)

# 1. 🎯 Purpose

Set up a local observability stack with:
    - Prometheus scraping metrics from itself and Node Exporter.
    - Grafana provisioned automatically with Prometheus as a datasource and dashboards.
    - Node Exporter exposing host metrics (CPU, memory, disk, network).

This project demonstrates:
    - Monitoring fundamentals (up metric, node metrics).
    - Dashboards-as-Code provisioning in Grafana.
    - Reproducible infra setup using Docker Compose.

# 2. 📂 Project Structure
grafana-lab/
│
├── docker-compose.yml                # Defines 3 services (Grafana, Prometheus, Node Exporter)
│
├── prometheus/
│   └── prometheus.yml                 # Prometheus scrape config
│
└── grafana/
    ├── provisioning/                  # Grafana provisioning configs
    │   ├── datasources/
    │   │   └── datasources.yaml
    │   └── dashboards/
    │       └── dashboards.yaml
    └── dashboards/
        ├── sample-dashboard.json      # Simple "up" metric
        └── sre-golden-signals-dashboard.json # Golden Signals dashboard

# 3. 🔑 Key Files

# docker-compose.yml
   - Spins up prometheus, grafana, and node-exporter.
   - Exposes ports: Grafana → 3000, Prometheus → 9090, Node Exporter → 9100.
# prometheus.yml
- job_name: 'prometheus'
  static_configs:
    - targets: ['localhost:9090']
- job_name: 'node_exporter'
  static_configs:
    - targets: ['host.docker.internal:9100']
# datasources.yaml
   - Auto-provisions Prometheus as Grafana datasource.
# dashboards.yaml
   - Tells Grafana to load JSON dashboards from /var/lib/grafana/dashboards. Uses updateIntervalSeconds: 30 to refresh changes.
# Dashboards (.json)
   - sample-dashboard.json → simple panel for Prometheus up metric.
   - sre-golden-signals-dashboard.json → Latency, Traffic, Errors, CPU Saturation.
   - 
# 4. Commands Uses
#Start stack
   - docker compose up -d

#Stop stack (keep data)
   - docker compose down

#Stop stack and wipe volumes (force reprovision)
   - docker compose down -v

#Restart Grafana only
   - docker compose restart grafana

#Check the Logs
   - docker compose logs -f grafana
   - docker compose logs -f prometheus

#Check resources
   - docker compose ps
   - docker system df
   - docker system prune -f      # optional cleanup
   - docker volume prune -f      # optional cleanup

# 5. Access URL's for Services
    - Grafana UI → http://localhost:3000
    - User: admin
    - Pass: <Password> -- set in docker-compose.yml
    - Prometheus UI → http://localhost:9090

# 6. Verification
    Grafana → Data Sources → Prometheus listed.
    Grafana → Dashboards →
        - Prometheus - Sample Dashboard shows up metric.
        - SRE Golden Signals Dashboard shows latency, traffic, errors, CPU.
    Prometheus → Status → Targets → both jobs (prometheus, node_exporter) are UP.

# 7. Learnings
    - up metric = heartbeat (1 if target reachable, 0 if not).
    - instance label comes from scrape target (e.g., localhost:9090).
    - Node Exporter = host metrics exporter (CPU, memory, disk, network).
    - Grafana provisioning = infra-as-code for observability.
    - docker compose down -v also removes volumes and networks (not visible in GUI).
