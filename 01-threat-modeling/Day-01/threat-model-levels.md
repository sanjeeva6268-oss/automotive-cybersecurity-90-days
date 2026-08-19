# Day 1 — Threat Model: Level 0, Level 1 and Level 2

This model follows the Chapter 1 approach in *The Car Hacker's Handbook*: begin with the vehicle at a bird's-eye view, identify receivers, then break complicated receivers into internal processes and trust boundaries. The handbook describes threat models as living documents that should be updated as the architecture becomes better understood. citehttps://opengarages.org/handbook/ebook/

## Level 0 — Bird's-Eye View

The objective is to identify how data can enter the vehicle without yet deciding how an attack would be performed.

```text
                         EXTERNAL ENVIRONMENT

  Cellular       Wi-Fi       Bluetooth       Key Fob       V2X
      |             |             |             |            |
      +-------------+-------------+-------------+------------+
                            |
                       [ External Boundary ]
                            |
                    +-------------------+
                    |   CONNECTED CAR   |
                    |      1.0           |
                    +-------------------+
                            |
                       [ Internal Boundary ]
                            |
             +--------------+---------------+
             |              |               |
           IVI/TCU       Gateway       Diagnostics
             |              |               |
             +--------------+---------------+
                            |
                     Internal Networks
                            |
                  CAN / CAN-FD / Ethernet
                            |
              +-------------+-------------+
              |             |             |
             ECU           ECU           ECU
```

### Level 0 inputs

- Cellular
- Wi-Fi
- Bluetooth
- Key fob / immobilizer-related wireless input
- V2X where deployed
- USB/media
- OBD-II/diagnostic access
- Charging interface for EVs
- Physical/service access
- Mobile/cloud-connected services

## Level 1 — Receivers

At Level 1, each external input is mapped to the first vehicle component or process that receives it.

| ID | Input | Receiver | Process / Zone | Trust Level |
|---|---|---|---|---|
| 1.1 | Cellular | TCU | Telematics | Low |
| 1.2 | Wi-Fi | IVI / connectivity service | Infotainment | Low |
| 1.3 | Bluetooth | IVI / Bluetooth service | Infotainment | Low |
| 1.4 | Key fob | Immobilizer / body controller | Access control | Medium |
| 1.5 | V2X | V2X receiver / application | V2X | Low |
| 1.6 | USB | IVI / USB stack | Infotainment | Low |
| 1.7 | OBD-II | Diagnostic interface / gateway | Diagnostics | Low-to-Medium |
| 1.8 | Charging interface | Charging controller | EV charging | Low-to-Medium |
| 1.9 | Mobile/cloud service | Backend / TCU | Connected services | Low |
| 1.10 | Service/debug access | ECU/service interface | Maintenance | Privileged |

### Trust-boundary observation

The handbook emphasizes that communication channels crossing more trust boundaries should be treated as higher risk. This is a prioritization principle, not proof that a vulnerability exists. citehttps://opengarages.org/handbook/ebook/

## Level 2 — Receiver Breakdown

The most useful Level 2 target for this exercise is the infotainment receiver because it can aggregate several untrusted inputs and may communicate with vehicle networks.

```text
                     IVI / Connectivity Receiver 1.1

 Cellular --------> HSI / modem interface --------+
                                                  |
 Wi-Fi ----------> WPA supplicant ----------------+----> User Space
                                                  |          |
 Bluetooth ------> Bluetooth service -------------+          |
                                                             |
 USB ------------> udev / USB stack ------------------------+
                                                             |
                                                     [ Trust Boundary ]
                                                             |
                                                        Kernel Space
                                                             |
                                                     CAN / Ethernet
                                                             |
                                                     Vehicle Gateway
                                                             |
                                                   Internal ECU Network
```

### Level 2 security questions

1. Which application receives the input?
2. Does the input reach privileged code?
3. Does it cross from user space to kernel space?
4. Does the receiver have access to a vehicle-network driver?
5. Can the receiver communicate through a gateway?
6. What authentication, authorization, validation, isolation, and logging controls exist?

## Candidate High-Risk Paths

| Path | Why it matters | Priority |
|---|---|---|
| Cellular → TCU → gateway → internal network | Potentially remote and crosses multiple zones | High |
| Wi-Fi → IVI → vehicle network | Wireless input can reach a connected receiver | High |
| Bluetooth → IVI → vehicle network | Short-range untrusted input with application exposure | High |
| USB → IVI → vehicle network | Physical media can provide malformed content | High |
| OBD-II → diagnostic interface → CAN | Direct physical access to vehicle diagnostics | High |
| V2X → V2X stack → vehicle applications | External wireless messages enter vehicle processing | Medium-High |
| Cloud → backend → TCU | Remote ecosystem dependency | Medium-High |

## Day 1 Conclusion

The attack surface is not just a list of ports. It is a map of **inputs → receivers → trust boundaries → downstream processes → assets**. The next stage is to identify threats against these paths and prioritize them using a consistent risk-rating method.
