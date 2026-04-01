Fix 1 — Give Permission to Docker Socket
bashchmod 666 /var/run/docker.sock
Then restart the collector:
bashdocker restart otel-collector
Check logs:
bashdocker logs otel-collector --tail=20

Fix 2 — If Still Failing, Recreate with Root User
bashdocker rm -f otel-collector

docker run -d \
  --name otel-collector \
  --network signoz_default \
  --user root \
  -v /opt/otel/otel-collector-config.yaml:/etc/otel/config.yaml \
  -v /var/run/docker.sock:/var/run/docker.sock \
  otel/opentelemetry-collector-contrib:latest \
  --config /etc/otel/config.yaml

✅ --user root forces the container to run as root, which has permission to access the Docker socket.


Fix 3 — Add Docker Group Permission (Permanent Fix)
bash# Get the docker group id
getent group docker

# Add socket to docker group
chown root:docker /var/run/docker.sock
chmod 660 /var/run/docker.sock
Then recreate container with docker group:
bashdocker rm -f otel-collector

docker run -d \
  --name otel-collector \
  --network signoz_default \
  --group-add $(getent group docker | cut -d: -f3) \
  -v /opt/otel/otel-collector-config.yaml:/etc/otel/config.yaml \
  -v /var/run/docker.sock:/var/run/docker.sock \
  otel/opentelemetry-collector-contrib:latest \
  --config /etc/otel/config.yaml


💡 Quickest fix is Fix 1 — just chmod 666 /var/run/docker.sock and restart. That solves it 90% of the time on KillerCoda.
