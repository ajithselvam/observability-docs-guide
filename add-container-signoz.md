Step 1 — Check Jenkins Container is Running
bashdocker ps | grep jenkins
Note down the container name (e.g., jenkins)

Step 2 — Create OTel Collector Config File
bashmkdir -p /opt/otel && nano /opt/otel/otel-collector-config.yaml
Paste this:
yamlreceivers:
  docker_stats:
    endpoint: unix:///var/run/docker.sock
    collection_interval: 10s
    containers:
      - jenkins

processors:
  batch: {}

exporters:
  otlp:
    endpoint: "localhost:4317"
    tls:
      insecure: true

service:
  pipelines:
    metrics:
      receivers: [docker_stats]
      processors: [batch]
      exporters: [otlp]

Step 3 — Run OTel Collector via Docker
bashdocker run -d \
  --name otel-collector \
  --network host \
  -v /opt/otel/otel-collector-config.yaml:/etc/otel/config.yaml \
  -v /var/run/docker.sock:/var/run/docker.sock \
  otel/opentelemetry-collector-contrib:latest \
  --config /etc/otel/config.yaml

Step 4 — Verify OTel Collector is Running
bashdocker logs otel-collector
```

You should see:
```
Everything is ready. Begin running and processing data.
```

---

## Step 5 — View in SigNoz

1. Open SigNoz at `http://localhost:8080`
2. Go to **Dashboards** → **New Dashboard**
3. Add a panel → select metrics like:
   - `container.cpu.usage.total`
   - `container.memory.usage`
   - `container.network.io`
4. Filter by `container_name = jenkins`

---

## Quick Summary
```
Jenkins Container
      ↓
OTel Collector (docker_stats receiver)
      ↓
SigNoz (port 4317)
      ↓
SigNoz Dashboard (metrics visualization)

✅ Once data flows in, you'll see CPU, Memory, Network, and Disk metrics of your Jenkins container in SigNoz dashboards.
plz assist me more with step 5 bcoz there is so confusion2:03 AMSure! Let me walk you through Step 5 in detail with screenshots description.

Step 5 — View Jenkins Metrics in SigNoz (Detailed)
1. Open SigNoz
http://localhost:8080

2. Go to Dashboards

On the left sidebar, click "Dashboards"
Click "+ New Dashboard" button (top right)
Click "+ Add Panel"


3. Select Metric
In the panel editor:
① Change Panel Type

Top right of panel → select "Time Series"

② In the Query section:
Metric Name  →  container.cpu.usage.total
③ Add Filter:
WHERE → container_name → = → jenkins
④ Click "Run Query"

4. Add More Panels (One by one)
Panel NameMetricCPU Usagecontainer.cpu.usage.totalMemory Usagecontainer.memory.usageNetwork Incontainer.network.io.usage.rx_bytesNetwork Outcontainer.network.io.usage.tx_bytes

5. Save Dashboard

Click "Save" button (top right)
Give name → "Jenkins Monitoring"
Click "Save"


If You Don't See Any Metrics ⚠️
Run this to confirm data is flowing:
bash# Check otel-collector logs
docker logs otel-collector --tail=50

# Check if port 4317 is open
netstat -tulnp | grep 4317
```

---

## Also Check This in SigNoz

Go to **"Metrics Explorer"** (left sidebar):
```
Search → type "container" → you'll see all container metrics
```
This confirms data is reaching SigNoz before building dashboard.

---

## Flow Recap
```
OTel Collector
     ↓
SigNoz receives on port 4317
     ↓
Metrics Explorer → confirm data
     ↓
Dashboards → build panels → visualize Jenkins

💡 Tip: If metrics are still not showing, wait 2-3 minutes after starting otel-collector — there's a slight delay before data appears in SigNoz UI.
not working2:09 AMI can see the issue! It says "no data found for container.cpu.usage.total" — this means the OTel Collector is not sending data to SigNoz yet.
Let's debug step by step:

Step 1 — Check OTel Collector Logs
bashdocker logs otel-collector --tail=50
Look for any errors and share the output.

Step 2 — Verify OTel Collector is Running
bashdocker ps | grep otel
If it's not running, restart it:
bashdocker start otel-collector

Step 3 — Check What Metrics Are Actually Arriving
Go to SigNoz UI:

Left sidebar → "Metrics Explorer"
Search for container

If you see no results → data is not reaching SigNoz at all.

Step 4 — Check Your OTel Config Endpoint
The most common issue is wrong endpoint. Since SigNoz is running via Docker Compose, the endpoint should be:
yamlexporters:
  otlp:
    endpoint: "signoz-otel-collector:4317"   # ← use this, NOT localhost
    tls:
      insecure: true
Fix the config:
bashnano /opt/otel/otel-collector-config.yaml
Change:
yaml# WRONG ❌
endpoint: "localhost:4317"

# CORRECT ✅
endpoint: "signoz-otel-collector:4317"
Then restart:
bashdocker restart otel-collector
```

---

## Step 5 — Also Fix the Container Name Filter

I can see in your screenshot:
```
WHERE → container_name = 'ubuntu'
Change ubuntu → jenkins (your actual container name)

Step 6 — Confirm Jenkins Container Name
bashdocker ps --format "{{.Names}}"
Use the exact name shown here in the WHERE filter in SigNoz.


💡 Most likely fix is Step 4 — using localhost inside Docker doesn't route to SigNoz. Use signoz-otel-collector:4317 instead.
