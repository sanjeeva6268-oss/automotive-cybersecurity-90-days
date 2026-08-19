# Day 1 — Automotive Attack Surface Fundamentals

## Objective

Understand the **automotive attack surface** by identifying every realistic way data, commands, software, or physical access can enter, leave, or influence a connected vehicle.

The goal is not to exploit a vehicle. The goal is to learn how a security researcher systematically maps interfaces and trust boundaries before testing.

## Learning Outcomes

By the end of Day 1, I should be able to:

- Define an automotive attack surface.
- Identify external and internal vehicle interfaces.
- Distinguish an interface from an asset.
- Identify trust boundaries.
- Identify interfaces that can reach the internal vehicle network.
- Identify systems that process untrusted input.
- Build a first-pass attack-surface inventory.
- Explain why attack-surface mapping is the first step in automotive security assessment.

## Core Questions

For every interface, ask:

1. Which interfaces are externally reachable?
2. Which interfaces cross a trust boundary?
3. Which interfaces can reach the internal vehicle network?
4. Which systems process untrusted input?
5. What data or commands can enter through the interface?
6. What assets could be affected if the interface is compromised?
7. What security controls are expected at the boundary?

## Attack Surface Model

```text
                    EXTERNAL WORLD
                          |
      +-------------------+-------------------+
      |                   |                   |
   Cellular             Wi-Fi             Bluetooth
      |                   |                   |
      +-------------------+-------------------+
                          |
                    Gateway / TCU
                          |
                    TRUST BOUNDARY
                          |
                 +--------+--------+
                 |                 |
              IVI / Head Unit   Vehicle Gateway
                 |                 |
                 +--------+--------+
                          |
                    Internal CAN
                          |
             +------------+------------+
             |            |            |
            ECU          ECU          ECU
             |            |            |
          Sensors      Actuators    Diagnostics
```

This is a conceptual model only. A real vehicle will contain additional interfaces, networks, gateways, ECUs, and trust zones.

## Initial Attack-Surface Categories

### 1. Wireless Interfaces

- Cellular connectivity
- Wi-Fi
- Bluetooth / Bluetooth Low Energy
- GNSS/GPS-related inputs
- NFC where present
- Dedicated short-range communication or V2X where present

### 2. Physical Interfaces

- OBD-II / diagnostic connector
- USB ports
- SD/card interfaces where present
- Charging interface for EVs
- Service/debug interfaces
- Physical access to ECUs

### 3. Vehicle Network Interfaces

- CAN / CAN-FD
- LIN
- Automotive Ethernet
- FlexRay where present
- Gateway interfaces

### 4. External Ecosystem Interfaces

- Mobile applications
- Cloud services
- Manufacturer backend services
- Web portals
- Fleet-management systems
- Third-party integrations

### 5. Human and Software Inputs

- Infotainment applications
- Navigation data
- Voice commands
- Diagnostic requests
- Firmware/software updates
- Configuration files
- Media files

## Trust-Boundary Thinking

A **trust boundary** is a point where information moves between components or environments with different security assumptions.

Examples:

- Internet → telematics control unit
- Mobile app → cloud backend
- Bluetooth device → infotainment system
- Diagnostic tool → vehicle gateway
- Infotainment network → vehicle-control network
- External software update source → ECU firmware

Crossing a trust boundary does not automatically mean a vulnerability exists. It means the boundary deserves explicit security controls and analysis.

## Day 1 Exercise

Create an attack-surface inventory for a hypothetical connected vehicle.

For each interface record:

| Interface | External/Internal | Data/Input | Trust Boundary | Possible Network Reach | Security Concern |
|---|---|---|---|---|---|
| Cellular | External | IP/network traffic | Yes | TCU/backend path | Remote input exposure |
| Wi-Fi | External | Network traffic | Yes | IVI/local network | Unauthorized access |
| Bluetooth | External | Pairing/data | Yes | IVI/local network | Untrusted device input |
| USB | Physical | Files/data | Yes | IVI | Malicious media/input |
| OBD-II | Physical | Diagnostic messages | Yes | Vehicle network | Unauthorized diagnostics |
| Mobile App | External | API requests | Yes | Backend/vehicle path | Authentication/API abuse |
| Software Update | External | Firmware/software | Yes | ECU | Malicious or tampered update |

The table is a starting point. Expand it during the exercise instead of treating it as a complete vehicle architecture.

## Safety Boundary

All practical testing in this repository should use authorized, isolated, simulated, or laboratory environments. Do not test vehicle systems that you do not own or have explicit permission to assess.

## Deliverables

- [ ] Complete `attack-surface.md`.
- [ ] Identify at least 15 potential interfaces.
- [ ] Mark trust boundaries.
- [ ] Identify which interfaces could eventually reach an internal vehicle network.
- [ ] Identify assets potentially affected by each interface.
- [ ] Add a short reflection to `notes.md`.

## Interview Questions

1. What is an automotive attack surface?
2. What is the difference between an attack surface and an asset?
3. Why is a vehicle gateway security-critical?
4. What is a trust boundary?
5. Why is an OBD-II interface security-sensitive?
6. Why can infotainment systems become relevant to vehicle cybersecurity?
7. What makes wireless interfaces different from physical interfaces?
8. Why should attack-surface mapping happen before penetration testing?

## Evidence

Screenshots, diagrams, lab output, packet captures, and notes can be stored under:

```text
01-threat-modeling/Day-01/evidence/
```

## Status

**Day 1: In Progress**
