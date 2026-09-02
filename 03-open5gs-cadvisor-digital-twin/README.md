![Docker Active Containers](docs/docker-active-containers.png)
![Prometheus Target Health](docs/prometheus-targets.png)
![Grafana CPU & Memory Telemetry](docs/telemetry-dashboard-1.png)
![Grafana Network Traffic Telemetry](docs/telemetry-dashboard-2.png)

# Open5GS 5G Core Digital Twin & Telemetry Stack

```text
[+] Running 8/8
 ✔ Network 03-open5gs-cadvisor-digital-twin_ran_twin_net  Created                             0.3s
 ✔ Container open5gs-db                                    Started                             4.0s
 ✔ Container twin-telemetry-grafana                        Started                             5.4s
 ✔ Container twin-telemetry-cadvisor                       Started                             4.7s
 ✔ Container twin-telemetry-prometheus                     Started                             6.1s
 ✔ Container vRAN-UPF                                      Started                             6.7s
 ✔ Container vRAN-NRF                                      Started                             6.7s
 ✔ Container vRAN-AMF                                      Started                             6.8s
NAME                      IMAGE                              COMMAND                  SERVICE
open5gs-db                mongo:6.0                          "docker-entrypoint.s…"   open5gs-db
twin-telemetry-cadvisor   gcr.io/cadvisor/cadvisor:v0.49.1   "/usr/bin/cadvisor -…"   twin-telemetry-cadvisor
twin-telemetry-grafana    grafana/grafana:latest             "/run.sh"                twin-telemetry-grafana
twin-telemetry-prometheus prom/prometheus:latest             "/bin/prometheus --c…"   twin-telemetry-prometheus
vRAN-AMF                  ghcr.io/borjis131/amf:latest       "entrypoint.sh -c /e…"   vRAN-AMF
vRAN-NRF                  ghcr.io/borjis131/nrf:latest       "open5gs-nrfd -c /et…"   vRAN-NRF
vRAN-UPF                  ghcr.io/borjis131/upf:latest       "entrypoint.sh -c /e…"   vRAN-UPF
```
## Architecture & Service Topology

The stack operates on a custom Docker bridge network (`ran_twin_net`: `10.45.0.0/16`) to isolate network functions and telemetry scrapers:

* **Open5GS Core Database (`open5gs-db`):** MongoDB 6.0 (`10.45.0.2`) serving as the central subscriber state registry.
* **Control Plane Functions:**
  * `vRAN-AMF` (`10.45.0.3`): Access and Mobility Management Function (`amd64`).
  * `vRAN-NRF` (`10.45.0.5`): Network Repository Function (`arm64` via QEMU emulation).
* **User Plane Function:**
  * `vRAN-UPF` (`10.45.0.4`): User Plane Function (`amd64`) with host TUN/TAP device binding.
* **Telemetry Pipeline:**
  * `twin-telemetry-cadvisor` (`10.45.0.12`): Real-time container resource collector.
  * `twin-telemetry-prometheus` (`10.45.0.10`): Time-series metrics scraper and storage engine.
  * `twin-telemetry-grafana` (`10.45.0.11`): Dashboard visualization interface.

## Technical Engineering & Remediation Highlights

1. **Multi-Architecture Execution (`arm64` on `x86_64`):** Resolved `exec format error` crashes on `vRAN-NRF` by registering host-level QEMU `binfmt_misc` handlers (`tonistiigi/binfmt`).
2. **Ephemeral File System Log Isolation:** Fixed Open5GS log file locking and startup aborts (`cannot open log file`) by mounting in-memory `tmpfs` file systems over `/open5gs/install/var/log/open5gs`.
3. **Host Kernel Routing Integration:** Bound `/dev/net/tun` with `NET_ADMIN` capabilities to enable packet processing for `vRAN-UPF`.
4. **Container DNS Service Discovery:** Configured cross-container scraping using static Docker network aliases (`http://twin-telemetry-prometheus:9090` and `twin-telemetry-cadvisor:8080`).

## Environment Prerequisites & Quick Start

Ensure host kernel modules and emulation handlers are active before starting the stack:

```bash
# 1. Register QEMU multi-architecture binary handlers
sudo docker run --privileged --rm tonistiigi/binfmt --install all

# 2. Load the host Linux kernel TUN module
sudo modprobe tun

# 3. Launch the complete digital twin stack
docker compose up -d
```

## Monitoring Interfaces

Grafana Dashboards: http://localhost:3000 (Default credentials: admin / admin)

Prometheus Target Health: http://localhost:9090/targets

cAdvisor Engine: http://localhost:8080
