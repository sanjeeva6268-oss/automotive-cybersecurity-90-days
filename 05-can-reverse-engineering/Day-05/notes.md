# Day 5 — Notes

## What I learned

- CAN reverse engineering starts with observing raw traffic rather than assuming signal meanings.
- Grouping frames by arbitration ID makes a large capture easier to analyze.
- Periodic traffic and event-driven traffic require different observation strategies.
- Changing one controlled variable at a time improves confidence in a signal hypothesis.
- A changing byte is evidence of correlation, not proof of semantics.
- Record/playback makes experiments repeatable.
- Wireshark and can-utils provide complementary views of captured traffic.
- ICSim provides a safer vehicle-like environment for learning CAN signal analysis.

## Key takeaway

```text
Observe → Group → Compare → Hypothesize → Repeat → Validate
```

## Portfolio lesson

A strong automotive cybersecurity report should show the evidence behind a decoded signal rather than only publishing the final answer.

## Day 5 status

**Theory/documentation: Complete**

**Physical vehicle testing: Not performed**

**Virtual CAN evidence: To be captured locally**
