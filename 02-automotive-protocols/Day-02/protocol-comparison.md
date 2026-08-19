# Day 2 — Protocol Comparison

| Protocol | Medium | Speed / characteristic | Topology / model | Security focus |
|---|---|---|---|---|
| CAN | Two-wire differential | Classic CAN commonly up to ~1 Mbps | Multi-node bus, arbitration | Message trust, segmentation, gateway access |
| LIN | Single wire | Up to ~20 Kbps | Master/slave | Master trust, gateway exposure |
| FlexRay | Twisted pair | Up to ~10 Mbps, deterministic | Bus/star, time-scheduled | Schedule integrity, node control |
| MOST | Optical/electrical variants | Version-dependent | Network/ring | Infotainment exposure and gateways |
| SAE J1850 | Single/differential variants | Legacy | Bus | Legacy access and segmentation |
| Automotive Ethernet | Ethernet PHY | Implementation-dependent | Switched network | IP security, segmentation, authentication |

## Key Takeaways

1. CAN is not the only vehicle bus.
2. Different buses exist because vehicle functions have different timing, bandwidth, cost, and reliability requirements.
3. OBD-II can expose CAN but does not mean every internal vehicle network is available there.
4. Gateways determine how traffic moves between network segments.
5. Security analysis must consider both the protocol and the architecture around it.

Source: *The Car Hacker's Handbook*, Chapter 2 — Bus Protocols. https://opengarages.org/handbook/ebook/
