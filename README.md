# SOC Engineering & Cybersecurity Labs

Hands-on Security Operations Center (SOC) labs, CLI tooling breakdowns, network protocol auditing, and security configuration artifacts.

## Lab Index

| Lab | Module Name | Core Tools & Concepts | Status |
| :--- | :--- | :--- | :--- |
| [Lab 01](./01-snort-wsl2-lab) | Snort 2.9 NIDS Deployment | WSL2, Custom ICMP Detection Rules, Raw Sockets | Complete |
| [Lab 02](./02-ports-and-protocols) | Ports & Socket Auditing | `ss -tulpn`, `/etc/services`, `curl` Banner Grabbing | Complete |
| [Lab 03](./03-open5gs-cadvisor-digital-twin) | 5G Core Digital Twin & Telemetry | Open5GS, cAdvisor, Prometheus, Grafana, QEMU `binfmt_misc` | Complete |

## Repository Architecture

```text
cyber-labs/
├── 01-snort-wsl2-lab/
├── 02-ports-and-protocols/
├── 03-open5gs-cadvisor-digital-twin/
└── README.md
