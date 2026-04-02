Got everything I need. Here's the full setup guide for monitoring your site's HTTP status in SigNoz (self-hosted via docker-compose).

HTTP Endpoint Monitoring in SigNoz (Self-Hosted)
The approach uses the OTel Collector's httpcheck receiver — it pings your URL on a schedule and records the HTTP status as metrics in SigNoz.

Step 1 — Find your OTel Collector config
In your SigNoz docker-compose setup, the collector config is usually at:
signoz/deploy/docker/otel-collector-config.yaml
or wherever your otel-collector service mounts its config volume.

Step 2 — Add the httpcheck receiver
Edit your otel-collector-config.yaml and add the httpcheck block under receivers:
yamlreceivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

  httpcheck:
    targets:
      - endpoint: https://yoursite.com   # 👈 replace with your actual URL
        method: GET
    collection_interval: 60s             # check every 60 seconds
To monitor multiple endpoints:
yaml  httpcheck:
    targets:
      - endpoint: https://yoursite.com
        method: GET
      - endpoint: https://yoursite.com/api/health
        method: GET
    collection_interval: 30s

Step 3 — Wire it into the metrics pipeline
Find the service.pipelines.metrics section and add httpcheck to receivers:
yamlservice:
  pipelines:
    metrics:
      receivers: [otlp, httpcheck]      # 👈 add httpcheck here
      processors: [batch]
      exporters: [clickhousemetricswrite]

Note: Self-hosted SigNoz uses clickhousemetricswrite as the exporter (not otlp). Don't change that part.


Step 4 — Restart the collector
bashdocker compose restart otel-collector
Wait ~1 minute, then check collector logs for errors:
bashdocker compose logs otel-collector --tail=50
```

---

### Step 5 — Build a Dashboard in SigNoz

1. Go to **Dashboards → New Dashboard → New Panel → Time Series**
2. In Query Builder, search for `httpcheck` — you'll see `httpcheck.status`
3. Set **WHERE** `http.status_class = 2xx` to track successful responses
4. Set **Avg By** `http.url` to split by endpoint

The chart shows **1** = endpoint healthy, **0** = down or error.

---

### Step 6 — Set up an Alert

1. From your dashboard panel → dropdown → **Create Alert**
2. Configure like this:
```
   Metric:    httpcheck.status
   WHERE:     http.status_class = 2xx
   Avg By:    http.url
   Condition: below threshold at least once in last 5 mins
   Threshold: 1
This fires whenever your site returns a non-2xx (4xx, 5xx, down) even once in 5 minutes.

Available Metrics & Attributes
MetricWhat it tells youhttpcheck.status1 if status matches class, 0 if nothttpcheck.durationResponse time in mshttpcheck.errorCount of check errors
AttributeExampleshttp.status_code200, 404, 500http.status_class2xx, 4xx, 5xxhttp.urlyour full URL

Quick Troubleshooting

No metrics showing? → Make sure httpcheck is in the receivers list under the metrics pipeline
SSL errors? → Add tls: { insecure_skip_verify: true } under the target for self-signed certs
Collector container can't reach your site? → Test network access: docker exec <otel-container> curl -I https://yoursite.com

Let me know your collector config file location if you want me to help tailor the exact config for your setup!otel-collector curl -I https://www.mysite.com
OCI runtime exec failed: exec failed: unable to start container process: exec: "curl": executable file not found in $PATH: unknown7:56 PMNo worries, curl isn't in the OTel collector image. Use wget or test from the host directly instead.
Test from host machine:
bashcurl -I https://www.mysite.com
Or test DNS resolution from inside the container:
bashdocker exec <otel-container-name> wget --spider https://www.mysite.com
Or use sh + basic connectivity check:
bashdocker exec <otel-container-name> sh -c "nslookup www.mysite.com"

The real question is — did you restart the collector after editing the config? Let's verify it's actually picking up the httpcheck receiver.
Check collector logs for httpcheck:
bashdocker compose logs otel-collector 2>&1 | grep -i httpcheck
Check if metrics are flowing:
bashdocker compose logs otel-collector 2>&1 | grep -i "error\|warn" | tail -20
Also confirm your container name:
bashdocker compose ps
Paste the output of the logs command — that'll tell us if the httpcheck receiver loaded correctly or if there's a config issue.if i install curl inside otel it works ?7:57 PMTechnically yes, but don't do it — here's why:
❌ Why not install curl in OTel container

Changes are wiped on every container restart (containers are stateless)
The OTel collector image is minimal/distroless — package managers like apt may not even be available
It's not the right tool for the job


✅ Just test from your host machine instead
bash# On your server/host directly
curl -I https://www.mysite.com
This tells you if the host (where the container runs) can reach your site — which is what matters, since the OTel collector inherits the host's network by default.

If curl isn't on the host either, install it there:
bash# Ubuntu/Debian
sudo apt install curl -y

# Then test
curl -I https://www.mysite.com

What you actually need to verify
The real checks are:
bash# 1. Did collector load the httpcheck receiver?
docker compose logs otel-collector 2>&1 | grep -i httpcheck

# 2. Any errors in collector?
docker compose logs otel-collector 2>&1 | grep -iE "error|warn" | tail -20
Paste that output and I can tell you exactly what's happening. The curl test is just a bonus sanity check.curl is working in host7:59 PMGreat! So the host can reach your site. Now let's verify the OTel collector is actually picking up the httpcheck config.
Run these in order and paste the output:
1. Check collector loaded httpcheck
bashdocker compose logs otel-collector 2>&1 | grep -i httpcheck
2. Check for any errors
bashdocker compose logs otel-collector 2>&1 | grep -iE "error|warn" | tail -30
3. Show your current collector config
bashcat /path/to/otel-collector-config.yaml

Replace with your actual path — common ones:
~/signoz/deploy/docker/otel-collector-config.yaml
./deploy/docker/otel-collector-config.yaml

4. Confirm container name
bashdocker compose ps

Paste all of this and I'll tell you exactly what to fix. Most common issues at this stage are:

httpcheck added to receivers block but forgot to add it to the pipeline
Config file path mismatch (editing the wrong file)
Collector not restarted after the edit
