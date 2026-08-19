# Day 1 — Threat Identification and Prioritization

## Scope

This is a first-pass threat model for a hypothetical connected vehicle. It identifies threats from the attack paths mapped in `threat-model-levels.md`. It is intended for authorized defensive research and does not describe exploitation steps.

## Threat Inventory

| ID | Attack Path | Threat | Impacted Asset | Primary Security Property | Initial Priority |
|---|---|---|---|---|---|
| T01 | Cellular → TCU | Malformed or unauthorized remote input reaches a connected service | TCU / connected services | Integrity | High |
| T02 | Cellular → TCU → gateway | Compromise of a remotely reachable component could expose downstream networks | Gateway / internal network | Integrity | High |
| T03 | Wi-Fi → IVI | Untrusted network traffic is accepted by the infotainment stack | IVI | Confidentiality / Integrity | High |
| T04 | Bluetooth → IVI | Malicious or malformed Bluetooth input reaches an application/service | IVI / user data | Integrity | High |
| T05 | USB → IVI | Malicious media or malformed files are processed | IVI | Integrity | High |
| T06 | OBD-II → diagnostics | Unauthorized diagnostic activity reaches vehicle ECUs | ECU diagnostic services | Integrity | High |
| T07 | V2X → V2X stack | Forged, malformed, or misleading external messages are processed | V2X applications | Integrity | Medium-High |
| T08 | Cloud → backend → TCU | Weak authentication/authorization exposes vehicle-connected services | Backend / TCU | Confidentiality / Integrity | High |
| T09 | Update service → ECU | Unauthorized or tampered software reaches an ECU | ECU firmware | Integrity | High |
| T10 | Debug/service interface → ECU | Exposed service functionality provides privileged access | ECU | Integrity | High |
| T11 | IVI → gateway | Insufficient segmentation permits unintended cross-zone communication | Vehicle network | Integrity | High |
| T12 | Internal CAN → ECU | Unauthenticated or insufficiently protected messages are accepted by a receiver | ECU / vehicle function | Integrity | Medium-High |

## Security Controls to Look For

For each threat, investigate whether the architecture provides:

- Strong authentication
- Authorization and least privilege
- Input validation
- Secure parsing
- Network segmentation
- Gateway filtering
- Secure boot
- Signed software updates
- Diagnostic access control
- Encryption where confidentiality is required
- Message authenticity where required
- Logging and monitoring
- Rate limiting and abuse protection
- Service/debug interface restrictions

## Risk-Rating Method

For this Day 1 exercise, use a simple qualitative rating:

- **High:** remote/physically accessible input plus meaningful downstream reach or privileged functionality.
- **Medium:** realistic exposure exists but impact or reach is limited by architecture or additional controls.
- **Low:** limited exposure, limited reach, or strong isolation is expected.

This is an initial prioritization only. A production TARA should use the organization's defined methodology and criteria.

## Top Three Risks for Further Study

### 1. Remote connectivity → internal vehicle network

A remotely reachable connected component is important because compromise may provide a path toward more trusted vehicle systems. The key architectural question is whether gateways and segmentation prevent unintended propagation.

### 2. Diagnostic interface → ECU network

Diagnostics are security-sensitive because diagnostic functions can be privileged. The assessment should establish who can access diagnostics, which services are available, and what authorization controls exist.

### 3. IVI → vehicle gateway

The IVI system processes several forms of untrusted input. Its security significance depends heavily on whether it can communicate with safety- or security-relevant vehicle networks.

## Day 1 Security Principle

**An exposed interface becomes more important when it crosses trust boundaries, processes untrusted input, has elevated privileges, or provides a path toward high-value vehicle assets.**
