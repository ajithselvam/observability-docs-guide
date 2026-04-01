Step 1 — Install Docker & Docker Compose
bashapt-get update && apt-get install -y docker.io docker-compose git
Start Docker:
bashsystemctl start docker
systemctl enable docker
Verify:
bashdocker --version
docker-compose --version

Step 2 — Clone SigNoz Repository
Clone the SigNoz repo and cd into the deploy directory: SigNoz
bashgit clone -b main https://github.com/SigNoz/signoz.git && cd signoz/deploy

Step 3 — Run SigNoz with Docker Compose
bashdocker compose -f docker/docker-compose.yaml up -d
This pulls and starts all SigNoz containers in the background.

Step 4 — Verify Containers are Running
bashdocker ps
```

You should see containers like `signoz-frontend`, `clickhouse`, `otel-collector`, `signoz-query-service` all running.

---

## Step 5 — Access SigNoz UI
```
http://localhost:8080
On KillerCoda, use the Traffic Port button and open port 8080.

Important Ports
PortPurpose8080SigNoz Web UI4317OpenTelemetry gRPC4318OpenTelemetry HTTP

Useful Commands
bash# Check logs
docker compose -f docker/docker-compose.yaml logs -f

# Stop SigNoz
docker compose -f docker/docker-compose.yaml down

# Restart
docker compose -f docker/docker-compose.yaml restart

⚠️ Note: SigNoz requires at least 4GB RAM. KillerCoda's free tier may be slow — give it 2-3 minutes for all containers to be healthy.
give me password for signoz1:04 AMSigNoz does not have a default password out of the box.
When you access SigNoz for the first time at http://localhost:8080, it will ask you to create an account with:
