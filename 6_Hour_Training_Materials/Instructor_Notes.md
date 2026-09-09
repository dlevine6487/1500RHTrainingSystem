# Instructor Notes: 6-Hour S7-1500R/H Commissioning Course

This document provides guidance for instructors delivering the 6-hour practical commissioning course for the S7-1500R/H system. The course is fast-paced and relies heavily on the pre-configured starter project to ensure students can focus on core HA concepts (Topologies, MRP, Watchdogs) rather than repetitive data entry.

## General Course Pacing & Philosophy

*   **Time Management:** 6 hours is tight for this material. Strictly enforce break times and keep theoretical lectures concise. The real learning happens during the "Pinch Points" in the labs.
*   **The Starter Project:** Ensure the TIA Portal starter project is pristine before every class. It should contain the generic hardware profiles (PLCs, IO stations, Switches) and templated tags, but **must not** have IP addresses assigned, MRP domains configured, multi-assignments made, or watchdogs tuned.
*   **Pinch Points:** The labs are designed with intentional "Pinch Points" (e.g., leaving the watchdog at default). Do not warn the students about these *before* they encounter them (unless time is critically short). Let them experience the failover failure, then use the Pro-Tip sections to guide them to the solution.

## Session Breakdown & Key Teaching Moments

### Session 1: Fundamentals (09:00 - 10:00)
*   **Key Concept:** Clearly distinguish between **R1** (Redundant Interface - two physical ports, two separate network rings) and **S2** (System Redundancy - single network connection logically communicating with two CPUs). Use the whiteboard heavily here.
*   **Demo Kit Orientation:** Spend 5 minutes physically pointing out the components on the demo kit. Show them the split backbone wiring vs. the subordinate S2 ring wiring.

### Session 2: Commissioning R1 (10:00 - 11:15)
*   **The Initial Setup Pinch Point:** The lab purposefully instructs students to build the network using two separate PROFINET subnets and configuring the backbone switches for S1 usage. This works perfectly for a pure R1 setup. **Do not correct them here.** This is setup for a powerful lesson in Session 3.
*   **Watch for:** Students accidentally cross-connecting the MRP domains. Domain 1 and Domain 2 must be strictly separate. If a student creates an MRP ring that encompasses both switches and both PLC sides, the network will storm and the CPUs will fail to sync.
*   **Multi-assignment:** Emphasize the "Not assigned" link. Show them how the Topology view changes visually when multi-assignment is successful.

### Session 3: The Y-Switch (11:30 - 12:30)
*   **The Subnet Refactoring Pinch Point:** When students attempt to introduce the Y-Switch, they will hit a wall because of the dual-subnet configuration from Session 2. Explain *why* this happens: Y-Switches in an S7-1500RH design require a single PROFINET IO subnet encompassing multiple domains. Force them to delete the second subnet and rebuild it as a single subnet. Emphasize that making this architectural decision early in a real project saves hours of rework.
*   **The DNA Redundancy Setting:** This is often missed. The switch will act as a generic unmanaged switch if DNA Redundancy is not explicitly checked in the module parameters.
*   **MRP Manager (Domain 3):** Explain *why* the Y-Switch is the manager. It isolates the MRP ring traffic of the subordinate S2 ring from the highly available R1 backbone. The backbone must not see Domain 3 traffic.

### Session 4: Populating S2 & Watchdogs (13:15 - 14:15)
*   **The Watchdog Pinch Point:** This is the most critical lab. If students breeze through the multi-assignment and try to go to RUN without tuning the watchdogs, **let them**. When they test failover in the next session, their IO will drop. This is a powerful teaching moment about the ~300ms switchover latency of the S7-1518HF.
*   **Templated Devices:** For the IE/PB Link HA and PN/PN Coupler, remind students that these are complex devices that often require their own dedicated training. For this class, they only need to verify the templated settings (e.g., F-Address matching on the Coupler) and ensure they are multi-assigned to the S2 network.
*   **Download Sequence:** Remind them the IE/PB Link requires a separate hardware download *after* the CPUs are loaded. It will likely throw a 'Wrong configuration' alarm if loaded out of order.

### Session 5: Failure Scenarios (14:15 - 15:00)
*   **The Payoff:** This should be fun. Have students physically pull cables and power supplies.
*   **Diagnostics:** Walk them through reading the diagnostic buffer. They need to understand the difference between OB70 (Peripheral Redundancy Loss - e.g., a pulled network cable) and OB72 (System Redundancy Loss - e.g., a dead primary CPU).
*   **Troubleshooting:** If the Backup CPU does not take over (Bumpless Transfer fails), immediately check the watchdog timers.

### Session 6: Programming Wrap-up (15:00 - 15:30)
*   **H-CiR (Hardware Configuration in RUN):** Briefly explain that while software updates are seamless, changing hardware configuration often requires breaking the redundant state (dropping to RUN-Solo) or even stopping the process entirely, depending on the modification.
*   **Sync Restrictions:** Mention that certain asynchronous instructions (like some open user communications or file handling) behave differently or are restricted in an H-system due to the need to keep both CPUs perfectly cycle-synchronous.

## Hardware Teardown

At the end of the course, instruct students to clear the PROFINET names and IP addresses from the training hardware (or perform a factory reset via TIA Portal) to prepare the kits for the next class.