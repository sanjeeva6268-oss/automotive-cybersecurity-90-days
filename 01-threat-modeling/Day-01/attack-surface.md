# Day 1 — Attack Surface Inventory

# Automotive Cybersecurity: Attack Surface

## What is an Attack Surface?

An **Attack Surface** is the set of possible **entry points, interfaces, and communication paths** through which data can enter, leave, or interact with a vehicle.

In automotive cybersecurity, we consider **every way data can enter or communicate with the vehicle**, because each interface can potentially introduce security risks.

## Examples of Vehicle Attack Surface

The attack surface of a connected vehicle can include:

* **Radio Signals** – Wireless communication received by the vehicle.
* **Key Fobs** – Wireless communication used for locking, unlocking, and vehicle access.
* **Sensors** – Inputs from cameras, radar, ultrasonic sensors, temperature sensors, and other vehicle sensors.
* **Keypads** – User input interfaces that allow commands or authentication.
* **Charging Interfaces** – Communication between the vehicle and charging infrastructure, especially in EVs.
* **USB Ports** – External devices can exchange data with infotainment or other vehicle systems.
* **Bluetooth** – Wireless communication with smartphones and other nearby devices.
* **Diagnostic Ports** – Interfaces such as **OBD-II** that provide access for diagnostics and vehicle maintenance.
* **GPS/GNSS** – Location and navigation data received from satellite systems.
* **Internet Connectivity** – Cellular, Wi-Fi, and other network connections that allow communication with external systems.
* **Infotainment Systems** – Interfaces connecting users, smartphones, applications, and vehicle networks.
* **Mobile Applications** – Smartphone applications that communicate remotely with connected-vehicle services.
* **V2X Communication** – Vehicle-to-Vehicle (V2V), Vehicle-to-Infrastructure (V2I), and other vehicle-to-everything communication.

### Simple Model

```text
                    Connected Vehicle
                           |
        +------------------+------------------+
        |                  |                  |
     Physical           Wireless           Network
        |                  |                  |
     USB/OBD           Bluetooth          Internet
     Keypad            Key Fob            Cellular
     Charging          Radio              Wi-Fi
        |                  |                  |
        +------------------+------------------+
                           |
                       Vehicle
                           |
                 +---------+---------+
                 |                   |
              ECUs              Vehicle Network
                 |                   |
               CAN               CAN / LIN
               ECU               Ethernet
```


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
