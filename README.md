# Nmap Network Scanning & Reconnaissance

> Authorized network reconnaissance and service enumeration performed against a Windows 11 VMware laboratory host using Nmap.

## Project Overview

This project demonstrates a structured Nmap-based network assessment covering:

- Host discovery
- Full TCP port scanning
- Service and version detection
- Operating system detection
- NSE script scanning
- Firewall / packet-filtering detection
- Final scan reporting

All testing was performed against an authorized Windows VM in an isolated VMware laboratory.

## Lab Environment

| System | Role | IP |
|---|---|---|
| Kali Linux | Nmap Scanner | `192.168.250.129` |
| Windows 11 | Target | `192.168.250.128` |
| Network | VMware Host-only | `192.168.250.0/24` |

## Assessment Workflow

```text
Network Discovery
       ↓
Host Discovery
       ↓
Port Scanning
       ↓
Service & Version Detection
       ↓
OS Detection
       ↓
NSE Script Scanning
       ↓
Firewall / Filtering Analysis
       ↓
Final Scan Report
```

## 1. Host Discovery

Nmap host discovery identified four active hosts on the laboratory subnet:

- `192.168.250.1`
- `192.168.250.128` — Windows target
- `192.168.250.129` — Kali scanner
- `192.168.250.254`

### Command

```bash
nmap -sn 192.168.250.0/24
```

### Evidence

`screenshots/02-host-discovery.png`

## 2. Port Scanning

A full TCP port scan identified the following ports on the target:

| Port | State | Service |
|---|---|---|
| 135/tcp | Open | msrpc |
| 139/tcp | Open | netbios-ssn |
| 445/tcp | Open | microsoft-ds |
| 9468/tcp | Filtered | unknown |

The remaining TCP ports were reported as filtered.

### Command

```bash
nmap -p- 192.168.250.128
```

### Evidence

`screenshots/03-port-scan.png`

## 3. Service & Version Detection

Nmap identified:

- Microsoft Windows RPC on `135/tcp`
- Microsoft Windows NetBIOS Session Service on `139/tcp`
- Microsoft-DS on `445/tcp`
- Port `9468/tcp` remained filtered/unknown

### Command

```bash
nmap -sV -p 135,139,445,9468 192.168.250.128
```

### Evidence

`screenshots/04-service-version.png`

## 4. OS Detection

Nmap strongly suggested Microsoft Windows 11 with approximately 97% confidence.

Nmap also warned that the OS result might be unreliable and did not produce an exact match. Therefore, this result is documented as a high-confidence guess rather than an exact identification.

### Command

```bash
sudo nmap -O 192.168.250.128
```

### Evidence

`screenshots/05-os-detection.png`

## 5. NSE Script Scanning

Default and safe NSE scripts identified:

- SMB dialects: `2.0.2`, `2.1`, `3.0`, `3.0.2`, `3.1.1`
- SMB signing enabled and required
- NetBIOS name: `DESKTOP-0MQ52K5`
- MTU: `1500`
- Approximately `-2s` clock skew

Some NSE scripts returned negotiation or execution errors. These are documented as scan limitations and are not treated as vulnerabilities.

### Command

```bash
nmap -sV --script "default,safe" -p 135,139,445,9468 192.168.250.128
```

### Evidence

`screenshots/06-nse-scan.png`

## 6. Firewall / Packet Filtering Detection

An ACK scan reported the tested ports as filtered:

- `135/tcp`
- `139/tcp`
- `445/tcp`
- `9468/tcp`

This indicates packet-filtering behavior on the tested ports.

The result does not identify a specific firewall product.

### Command

```bash
sudo nmap -sA -p 135,139,445,9468 192.168.250.128
```

### Evidence

`screenshots/07-firewall-detection.png`

## 7. Final Scan

The final combined scan was performed using SYN scanning, service and version detection, OS detection, and reason reporting.

### Command

```bash
sudo nmap -sS -sV -O --reason \
-p 135,139,445,9468 \
192.168.250.128 \
-oA nmap-final-report
```

### Generated Report Formats

```text
nmap-final-report.nmap
nmap-final-report.xml
nmap-final-report.gnmap
```

### Evidence

`screenshots/08-final-nmap-report.png`

## Key Findings

### Exposed Services

| Port | Service | Security Relevance |
|---|---|---|
| 135 | MSRPC | Windows remote procedure call exposure |
| 139 | NetBIOS | Legacy Windows file/session networking |
| 445 | SMB | Windows file sharing and network services |
| 9468 | Unknown / filtered | Requires further identification if exposed |

### SMB Security Observation

The NSE results reported that SMB message signing was **enabled and required** on the target.

### Filtering Observation

The ACK scan showed filtering behavior across the tested ports, indicating packet filtering between the scanner and target.

## Limitations

- OS detection did not produce an exact match.
- Some NSE scripts could not complete service negotiation.
- Firewall detection identifies filtering behavior, not a specific firewall product.
- Results are limited to the authorized VMware laboratory environment.

## Conclusion

The Nmap assessment successfully identified live hosts, exposed services, service information, probable operating-system details, SMB characteristics, and packet-filtering behavior.

The results provide a baseline network attack-surface inventory for further defensive security assessment.

## Evidence

| Stage | Evidence |
|---|---|
| Network connectivity | `screenshots/01-network-connectivity.png` |
| Host discovery | `screenshots/02-host-discovery.png` |
| Port scan | `screenshots/03-port-scan.png` |
| Service/version | `screenshots/04-service-version.png` |
| OS detection | `screenshots/05-os-detection.png` |
| NSE scanning | `screenshots/06-nse-scan.png` |
| Firewall detection | `screenshots/07-firewall-detection.png` |
| Final scan | `screenshots/08-final-nmap-report.png` |

## Repository Structure

```text
nmap-network-scanning/
├── README.md
├── findings/
│   └── findings.md
├── scans/
│   ├── port-scan.txt
│   ├── service-version.txt
│   ├── os-detection.txt
│   ├── nse-scan.txt
│   ├── firewall-detection.txt
│   └── final-report.nmap
├── reports/
│   ├── final-report.xml
│   └── final-report.gnmap
└── screenshots/
    ├── 01-network-connectivity.png
    ├── 02-host-discovery.png
    ├── 03-port-scan.png
    ├── 04-service-version.png
    ├── 05-os-detection.png
    ├── 06-nse-scan.png
    ├── 07-firewall-detection.png
    └── 08-final-nmap-report.png
```

## Disclaimer

This project was conducted for cybersecurity education and defensive security testing in an authorized VMware laboratory.

Only systems owned or explicitly authorized for testing should be scanned.
