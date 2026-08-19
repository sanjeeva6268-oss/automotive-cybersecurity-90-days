# Day 1 — Attack Surface Inventory

## Purpose

Map the ways an attacker could interact with a connected vehicle before performing any security testing.

## Inventory

| # | Interface | Exposure | Untrusted Input | Trust Boundary | Potential Reach | Primary Asset / Concern |
|---|---|---|---|---|---|---|
| 1 | Cellular | Remote | Yes | Yes | TCU / backend | Remote connectivity |
| 2 | Wi-Fi | Wireless | Yes | Yes | IVI / local network | Network access |
| 3 | Bluetooth | Wireless | Yes | Yes | IVI / local network | Device pairing and data |
| 4 | GNSS/GPS input | Wireless | Yes | Yes | Navigation / applications | Location integrity |
| 5 | USB | Physical | Yes | Yes | IVI | Malicious files/media |
| 6 | OBD-II | Physical | Yes | Yes | Diagnostic network | Diagnostic access |
| 7 | Mobile application | Remote | Yes | Yes | Cloud/backend | Authentication and API security |
| 8 | Cloud backend | Remote | Yes | Yes | Vehicle services | Backend compromise |
| 9 | Software update | Remote/physical | Yes | Yes | ECU/software | Firmware integrity |
| 10 | Automotive Ethernet | Internal | Potentially | Yes between zones | ECUs/gateway | Network segmentation |
| 11 | CAN/CAN-FD | Internal | Depends on source | Between network zones | ECUs | Message integrity/authenticity |
| 12 | LIN | Internal | Depends on source | Network boundary | Local ECU components | Local bus security |
| 13 | Diagnostic service | Physical/remote | Yes | Yes | ECU diagnostics | Unauthorized commands |
| 14 | V2X interface | Wireless | Yes | Yes | Vehicle/V2X stack | Untrusted external messages |
| 15 | Debug/service interface | Physical | Yes | Yes | ECU | Development/service access |

## Questions for Each Interface

- What can send data into this interface?
- Can an unauthenticated party interact with it?
- Is input validated?
- Does the interface cross a trust boundary?
- Can it communicate with another vehicle network?
- Is there a gateway or firewall between zones?
- What assets could ultimately be affected?

## Initial Observations

1. External interfaces are not equally dangerous; risk depends on reachability, trust, privileges, and downstream network access.
2. A gateway can reduce the impact of an exposed interface by enforcing segmentation and security policy.
3. Diagnostic and service interfaces deserve special attention because they may provide privileged access.
4. Wireless and cloud interfaces increase the number of remote attack paths.
5. Any component processing untrusted input should be included in the security analysis.

## Next Step

Refine this inventory into a threat model by identifying assets, threat actors, attack paths, security goals, and potential threats.
