
### Day 1 first artifact

Create `01-threat-modeling/automotive-architecture.md`:

```markdown
# Automotive Architecture — Day 1

## Objective

Create a high-level view of a connected vehicle and identify the major
systems through which information can enter or leave the vehicle.

## High-Level Architecture

```text
                    Internet / Cloud
                          |
                  +-------+-------+
                  |               |
               Cellular          Wi-Fi
                  |               |
                  +-------+-------+
                          |
                    Infotainment
                  /       |       \
             Bluetooth   USB    Applications
                  |
                  |
              Vehicle Network
                  |
        +---------+---------+
        |         |         |
       ECU       BCM       ABS
        |                     |
        +--------- CAN -------+
                  |
             Other ECUs
