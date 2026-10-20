# Instructor Notes: Session 4 - Populating S2 & Watchdogs

## 13:15 - 14:15 | Populating S2 & Watchdogs (Hands-On Lab)

### Key Teaching Moments
*   **Verifying Redundancy Modes:** Once the S2 devices are populated behind the Y-Switch, show students how to verify their architecture in TIA Portal. Have them open the `Network view`, select the `I/O communication` tab, and look at the `Mode` column. They should explicitly see the distinction verified here (e.g., the backbone ET200SP is `IO device(R1)`, the Y-Switch is `IO device(S2)`, and standard devices behind it are S2/S1).
*   **The Watchdog Pinch Point:** This is the most critical lab. If students breeze through the multi-assignment and try to go to RUN without tuning the watchdogs, **let them**. When they test failover in the next session, their IO will drop. This is a powerful teaching moment about the ~300ms switchover latency of the S7-1518HF.
*   **Templated Devices:** For the IE/PB Link HA and PN/PN Coupler, remind students that these are complex devices that often require their own dedicated training. For this class, they only need to verify the templated settings (e.g., F-Address matching on the Coupler) and ensure they are multi-assigned to the S2 network.
*   **Download Sequence:** Remind them the IE/PB Link requires a separate hardware download *after* the CPUs are loaded. It will likely throw a 'Wrong configuration' alarm if loaded out of order.

### Relevant Slide Content
Please reference `1500RH v21.pdf`:
*   **Slide: "Functional principle" (Around Slide 1317 - 1440)**: Use these slides to explain the mechanics of the primary/backup synchronization and why the switchover takes time (~300ms), which directly motivates the need for Watchdog tuning.
*   **Slide: "Switching behavior without PROFINET System Redundancy" (Around Slide 1698 - 1794)**: Discuss what happens to standard devices (S1) if they are not behind a Y-Switch during a failover vs when they are protected by S2.
*   **Slide: "Network Configuration for PROFIBUS Devices" (Around Slide 3285 - 3340)**: Briefly reference this when discussing the IE/PB Link HA integration.
