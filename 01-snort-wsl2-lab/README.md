# Snort NIDS Threat Detection & Traffic Analysis Lab (WSL2)

![Snort](https://img.shields.io/badge/Snort-2.9-red?style=flat&logo=cisco)
![Ubuntu](https://img.shields.io/badge/Environment-WSL2%20Ubuntu-orange?style=flat&logo=ubuntu)
![Status](https://img.shields.io/badge/Verification-Validated-brightgreen)

## Project Overview
Deployed Snort 2.9 (NIDS) inside WSL2 Ubuntu to inspect virtual interface traffic (`eth0`), solve DAQ raw socket permissions, engineer custom rules, and analyze host-to-guest ICMP probes in real time.

---

## Lab Execution & Proof of Concept

### Phase 1: Network Reconnaissance & Interface Mapping
Identified the WSL2 virtual ethernet interface (`eth0`) and performed initial network reconnaissance using Nmap.

![Network Reconnaissance](assets/01-network-recon.png)

### Phase 2: Snort Installation & DAQ Socket Privilege Fix
Installed Snort NIDS on Ubuntu and elevated execution privileges (`sudo`) to resolve raw socket initialization errors (`Operation not permitted`).

![Snort Installation](assets/02-snort-installation.png)

### Phase 3: Custom Signature Authoring
Configured custom threat detection rule `sid:1000001` inside `/etc/snort/rules/local.rules` to detect ICMP probes.

![Custom Signature Authoring](assets/03-custom-signature.png)

### Phase 4: Threat Simulation & Live Alert Verification
Executed Snort in console alert mode (`sudo snort -A console -q -c /etc/snort/snort.conf -i eth0`) and verified real-time alert generation from Windows Host ping requests.

![Threat Simulation Alert Verification](assets/04-alert-verification.png)
