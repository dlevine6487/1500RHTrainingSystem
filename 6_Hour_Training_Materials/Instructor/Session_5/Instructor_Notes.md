# Instructor Notes: Session 5 - Failure Scenarios & Diagnostics

## 14:30 - 15:15 | Failure Scenarios & Diagnostics (Hands-On Lab & Pivot 2)

### Key Teaching Moments
*   **The Payoff:** This should be fun. Have students physically pull cables and power supplies.
*   **Diagnostics:** Walk them through reading the diagnostic buffer. They need to understand the difference between OB70 (Peripheral Redundancy Loss - e.g., a pulled network cable) and OB72 (System Redundancy Loss - e.g., a dead primary CPU).
*   **Troubleshooting:** If the Backup CPU does not take over (Bumpless Transfer fails), immediately check the watchdog timers.
*   **Pivot Point 2 (Presenter Handoff):** Conclude the lab with the second guest presentation. The presenter will use a subordinate PLC digitally coupled to the redundant system's IO to demonstrate hardwired failover timing, explicitly comparing R1 vs S2, and the impact of MRP configurations on those timings.

### Relevant Slide Content
Please reference `1500RH v21.pdf`:
*   **Slide: "Failure scenarios for S7-1500R/H" (Starts around Slide 3706)**: Use the comprehensive failure scenario flowcharts to walk students through what happens when specific components (CPUs, cables, rings) fail, and how the system reacts.
*   **Slide: "Reporting loss of redundancy" (Starts around Slide 5744)**: Explain the specific diagnostic interrupts (OB70/OB72) and how they are logged in the buffer.
