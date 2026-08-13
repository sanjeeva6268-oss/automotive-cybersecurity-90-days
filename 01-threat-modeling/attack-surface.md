
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

## Questions to Investigate

1. Which interfaces are externally reachable?
2. Which interfaces cross a trust boundary?
3. Which interfaces can reach the internal vehicle network?
4. Which systems process untrusted input?
5. Which assets would be impacted by compromise?

## Day 1 Finding

A connected vehicle has multiple communication paths, so security
analysis must begin by identifying the ways information can enter the
vehicle before investigating individual vulnerabilities.
