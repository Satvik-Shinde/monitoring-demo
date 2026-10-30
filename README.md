
# Prometheus, Node Exporter, and Grafana Monitoring

## Step 5: Verify Prometheus Targets

Open the Prometheus targets page:

http://localhost:9090/targets

Check that both the `prometheus` and `node-exporter` targets show the state **UP**.

If a target is down, check the container status and logs:

```bash
docker compose ps
docker compose logs prometheus
docker compose logs node-exporter
```

## Step 6: Run Sample Queries in Prometheus (PromQL)

Open Prometheus:

http://localhost:9090

Enter the following queries one at a time and click **Execute**.

### 1. Check target health

```promql
up
```

Returns `1` for a target that is successfully scraped and `0` for a target that is not.

### 2. View CPU time counters

```promql
node_cpu_seconds_total
```

Displays cumulative CPU time by CPU and operating mode.

### 3. Calculate CPU time rate

```promql
rate(node_cpu_seconds_total[1m])
```

Shows the rate of CPU time accumulated over the last minute, grouped by CPU and mode.

## Step 7: Connect Grafana to Prometheus

Open Grafana:

http://localhost:3001

Log in using the initial credentials:

- **Username:** `admin`
- **Password:** `admin`

Change the password if prompted.

Configure Prometheus as a data source:

1. Navigate to **Connections → Data sources**.
2. Click **Add data source**.
3. Select **Prometheus**.
4. Set the server URL to:

   `http://prometheus:9090`

5. Click **Save & test**.
6. Confirm that the connection succeeds.

**Note:** Use `http://prometheus:9090` instead of `localhost:9090` because Grafana accesses Prometheus through the Docker Compose network.

## Step 8: Import a Grafana Dashboard

Import the **Node Exporter Full** dashboard.

Dashboard ID: `1860`

Dashboard link:
https://grafana.com/grafana/dashboards/1860-node-exporter-full/

Steps:

1. Open Grafana at http://localhost:3001.
2. Navigate to **Dashboards → New → Import**.
3. Enter dashboard ID `1860`.
4. Click **Load**.
5. Select your Prometheus data source.
6. Click **Import**.

The dashboard can display CPU, memory, disk, network, and other available Node Exporter metrics.

**Note:** Running Node Exporter inside Docker does not automatically provide complete metrics for the Windows or WSL host. Additional host filesystem mounts and configuration may be needed for full host monitoring.

## Step 9: Stop the Monitoring Stack

Navigate to the directory containing `docker-compose.yml` and run:

```bash
docker compose down
```

This stops and removes the containers and the Compose-created network.

To check the containers before stopping:

```bash
docker compose ps
```

The sample Compose configuration does not define persistent volumes for Prometheus or Grafana, so data stored only inside the removed containers may be lost.

## Troubleshooting

### Prometheus target is DOWN

Check the running containers:

```bash
docker compose ps
```

Inspect the relevant service logs:

```bash
docker compose logs prometheus
docker compose logs node-exporter
```

### Grafana cannot connect to Prometheus

Verify the data source URL:

`http://prometheus:9090`

Ensure both services belong to the same Docker network.

### Grafana does not open

Use the configured host port:

http://localhost:3001

### Dashboard shows no data

1. Verify Prometheus targets are UP.
2. Confirm the Prometheus data source is selected.
3. Execute `node_cpu_seconds_total` in Prometheus.
4. Check that Node Exporter is exposing metrics at http://localhost:9100/metrics.

