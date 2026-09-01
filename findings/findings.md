# Nmap Network Scanning Findings

## Target

- Scanner: Kali Linux
- Target: Windows 11 VM
- Target IP: `192.168.250.128`
- Network: `192.168.250.0/24`
- Scan type: Authorized VMware laboratory

## 1. Host Discovery

Nmap host discovery identified four active hosts on the laboratory subnet:

- `192.168.250.1`
- `192.168.250.128` — Windows target
- `192.168.250.129` — Kali scanner
- `192.168.250.254`

## 2. Port Scanning

A full TCP port scan identified these open ports on the Windows target:

| Port | State | Service |
|---|---|---|
| 135/tcp | Open | msrpc |
| 139/tcp | Open | netbios-ssn |
| 445/tcp | Open | microsoft-ds |
| 9468/tcp | Open | unknown |

The remaining TCP ports were reported as filtered during the full scan.

## 3. Service and Version Detection

Nmap identified:

- `135/tcp` — Microsoft Windows RPC
- `139/tcp` — Microsoft Windows NetBIOS Session Service
- `445/tcp` — Microsoft-DS
- `9468/tcp` — filtered/unknown

Nmap reported the target operating system family as Windows.

## 4. OS Detection

Nmap strongly suggested Microsoft Windows 11 with approximately 97% confidence.

However, Nmap also reported that the OS results may be unreliable because the scan conditions were non-ideal and no exact OS match was obtained.

Therefore, the result is documented as a high-confidence guess rather than an exact OS identification.

## 5. NSE Script Scanning

Default and safe NSE scripts identified:

- SMB dialects: `2.0.2`, `2.1`, `3.0`, `3.0.2`, `3.1.1`
- SMB message signing: enabled and required
- NetBIOS name: `DESKTOP-0MQ52K5`
- MTU: `1500`
- Clock skew: approximately `-2s`

Some NSE scripts returned negotiation/execution errors. These are documented as scan limitations and are not treated as vulnerabilities.

## 6. Firewall / Packet Filtering Detection

An ACK scan reported all tested ports as `filtered`:

- `135/tcp`
- `139/tcp`
- `445/tcp`
- `9468/tcp`

This indicates packet filtering behavior on the tested ports.

The scan does not identify a specific firewall product.

## 7. Security Observations

The exposed Windows services provide a useful attack-surface inventory for a defensive assessment.

Particular attention should be given to:

- RPC (`135`)
- NetBIOS (`139`)
- SMB (`445`)
- Any service associated with additional exposed ports

SMB message signing was observed as enabled and required in the NSE results.

## 8. Limitations

- OS detection did not produce an exact match.
- Some NSE scripts could not complete service negotiation.
- Firewall detection identifies filtering behavior rather than a specific firewall product.
- Results are limited to the authorized VMware laboratory network.

## Conclusion

The Nmap assessment successfully identified live hosts, exposed TCP services, service information, probable OS details, SMB characteristics, and filtering behavior on the Windows laboratory target.

The results provide a baseline network-attack-surface inventory for further defensive security assessment.
