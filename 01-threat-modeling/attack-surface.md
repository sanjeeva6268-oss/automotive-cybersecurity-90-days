
The book's Level 0 approach similarly begins with the vehicle at the center and maps how data can enter the vehicle from external and internal inputs. :contentReference[oaicite:1]{index=1}

### Day 1 second artifact

Create `01-threat-modeling/attack-surface.md`:

```markdown
# Automotive Attack Surface — Day 1

## Objective

Identify potential entry points into a connected vehicle.

## Attack Surface

| Interface | Category | Example Data | Initial Security Concern |
|---|---|---|---|
| Cellular | External | Network/remote services | Remote attack surface |
| Wi-Fi | External | Network traffic | Unauthorized access |
| Bluetooth | External | Device/application data | Malformed input |
| USB | External | Files/media/update data | Malicious content |
| Key Fob | External | Authentication signals | Authentication abuse |
| TPMS | External | Tire sensor data | Spoofed sensor information |
| Diagnostic Port | Physical | Diagnostic messages | Unauthorized diagnostics |
| CAN | Internal | ECU messages | Message injection |
| Infotainment | Internal/External | Applications/data | Pivot into vehicle network |

### Key Takeaway

The vehicle attack surface consists of both external and internal interfaces.

External interfaces such as Cellular, Wi-Fi, Bluetooth, USB, Key Fob, and TPMS
can provide potential entry points into the vehicle.

Physical interfaces such as the Diagnostic Port can provide direct access to
vehicle communication systems.

Internal interfaces such as CAN and Infotainment are important because a
compromised component may potentially be used to reach other ECUs or vehicle
functions.

The initial security concerns identified here will be used as inputs for
threat modeling and TARA activities later in the 90-day learning journey.

## Attack Surface Analysis

### 1. Which interfaces are externally reachable?

The following interfaces are externally reachable:

| Interface | External Reachability | Notes |
|---|---|---|
| Cellular | Yes | Connects the vehicle to mobile networks and remote services |
| Wi-Fi | Yes | Allows wireless network connectivity |
| Bluetooth | Yes | Allows nearby devices to communicate with the vehicle |
| USB | Yes | Physical external interface for media, files, and updates |
| Key Fob | Yes | Wireless authentication interface |
| TPMS | Yes | Receives wireless tire sensor data |
| Diagnostic Port | Yes – Physical | Allows physical diagnostic equipment to connect |
| Infotainment | Yes | May expose external connectivity through applications and wireless interfaces |
| CAN | No – Normally internal | Internal vehicle communication network |

---

### 2. Which interfaces cross a trust boundary?

Interfaces that accept data from outside the vehicle should be treated as trust-boundary crossings.

Examples:

- Cellular → Vehicle
- Wi-Fi → Vehicle
- Bluetooth → Vehicle
- USB → Vehicle
- Key Fob → Vehicle
- TPMS → Vehicle
- Diagnostic Tool → Vehicle
- Infotainment applications → Vehicle network

The key security principle is:

> Data crossing from an untrusted or less-trusted environment into the vehicle should not automatically be trusted.

---

### 3. Which interfaces can reach the internal vehicle network?

Potential paths into the internal vehicle network include:

| Interface | Potential Path |
|---|---|
| Cellular | Cellular → Telematics ECU → Vehicle network |
| Wi-Fi | Wi-Fi → Infotainment/Connectivity ECU → Vehicle network |
| Bluetooth | Bluetooth → Infotainment/Connectivity ECU → Vehicle network |
| USB | USB → Infotainment/Head Unit → Vehicle network |
| Diagnostic Port | Diagnostic Tool → Gateway → CAN/Vehicle network |
| Infotainment | Infotainment → Gateway → Internal ECUs |
| TPMS | TPMS Receiver → Gateway/ECU → Vehicle network |

The exact path depends on the vehicle architecture.

---

### 4. Which systems process untrusted input?

Potential systems processing untrusted or externally sourced input include:

- Telematics/Cellular ECU
- Wi-Fi subsystem
- Bluetooth subsystem
- Infotainment/Head Unit
- USB/media subsystem
- Keyless-entry system
- TPMS receiver
- Diagnostic interface
- Vehicle gateways
- ECUs receiving messages from external-facing systems

Examples of untrusted input include:

- Network packets
- Bluetooth messages
- USB files
- Diagnostic requests
- Wireless sensor messages
- Authentication messages
- Application data
- CAN messages originating from a compromised ECU

---

### 5. Which assets would be impacted by compromise?

Potential assets include:

| Asset | Potential Impact |
|---|---|
| Vehicle control ECUs | Unauthorized control or disruption |
| CAN messages | Message manipulation or injection |
| Gateway ECU | Network pivoting between vehicle domains |
| Infotainment system | Data exposure or network pivot |
| Vehicle credentials | Unauthorized access |
| Keyless-entry functionality | Authentication abuse |
| Diagnostic functions | Unauthorized configuration or programming |
| Firmware/software | Unauthorized modification |
| Personal/user data | Privacy breach |
| Location data | Tracking/privacy concerns |
| Vehicle availability | Denial of service |
| Safety-related functions | Potential safety impact |

### Security Perspective

The attack surface can be viewed as:

**External Interface → Trust Boundary → Processing System → Internal Network → Asset**

For example:

**Wi-Fi → Trust Boundary → Infotainment ECU → Gateway → CAN → Vehicle ECU**

This model provides a useful starting point for the next stage of the analysis: **Threat Modeling and TARA (Threat Analysis and Risk Assessment).**

## Day 1 Finding

A connected vehicle has multiple communication paths, so security
analysis must begin by identifying the ways information can enter the
vehicle before investigating individual vulnerabilities.
