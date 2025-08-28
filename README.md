# prometheus-grafana

## Getting Started with Prometheus and Grafana: Monitoring, Dashboards, and Alerts

Monitoring is a critical part of modern infrastructure management. To ensure applications run smoothly, we need to track system performance, detect bottlenecks, and set up alerts when things go wrong.

In this blog, I’ll walk you through setting up Prometheus for collecting metrics, Grafana for visualization, and configuring alerts for proactive monitoring.

## 1. Setting Up Prometheus

Prometheus is an open-source monitoring system that scrapes metrics from targets and stores them in a time-series database.

Installation (Linux-based system):
## Download Prometheus
```
wget https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
```
## Extract files
```
tar -xvf prometheus-2.52.0.linux-amd64.tar.gz
cd prometheus-2.52.0.linux-amd64
```
## Run Prometheus
```
./prometheus --config.file=prometheus.yml
```

By default, Prometheus runs on http://localhost:9090

## Configuring Targets

In prometheus.yml, add your target (for example, a Node.js app exposing /metrics):
```
scrape_configs:
  - job_name: 'node_app'
    static_configs:
      - targets: ['localhost:3000']

```
Now Prometheus will scrape metrics from your Node.js application. You can verify by checking the “Targets” page in the Prometheus UI.

## 2. Creating a Grafana Dashboard

Grafana provides a powerful visualization layer on top of Prometheus.

Installation:
## On Debian/Ubuntu
```
sudo apt-get install -y apt-transport-https software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt-get update
sudo apt-get install grafana -y
```
## Start Grafana
```
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

Grafana runs on http://localhost:3000
 (default user: admin / admin).

## Adding Prometheus as a Data Source

1. Go to Configuration → Data Sources → Add Data Source.
2. Choose Prometheus, and set the URL: http://localhost:9090.

3. Save & Test.

## Creating a Dashboard

Click Create → Dashboard.

Add a new panel.

Use queries like:

node_cpu_seconds_total
node_memory_Active_bytes


Customize the graph for CPU and memory usage.

Within minutes, you’ll have a live dashboard tracking your system metrics.

3. Configuring Alerts

Prometheus includes an Alertmanager component that handles alerts based on rules you define.

Step 1: Create an Alert Rule

In prometheus.yml, add:

rule_files:
  - "alert.rules.yml"


Then create a file alert.rules.yml:

groups:
  - name: example-alerts
    rules:
      - alert: HighCPUUsage
        expr: 100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage detected"
          description: "CPU usage is above 80% for more than 2 minutes."


This alert triggers when CPU usage stays above 80% for 2 minutes.

Step 2: Integrating with Grafana

In Grafana, go to Alerting → Alert Rules.

Import the Prometheus alert rule.

Configure notification channels (email, Slack, PagerDuty, etc.).

Now, whenever CPU usage exceeds the threshold, Grafana will display the alert and notify your configured channels.

🔑 Key Takeaways

Prometheus helps collect and store metrics from your services.

Grafana makes metrics visual and insightful with dashboards.

Alerts ensure you get notified before an incident escalates.

Together, they form a powerful monitoring stack used widely across industries for observability and
