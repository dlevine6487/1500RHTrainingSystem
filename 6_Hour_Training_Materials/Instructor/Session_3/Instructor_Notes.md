# Instructor Notes: Session 3 - The Y-Switch & Subordinate Networks

## 11:45 - 13:00 | The Y-Switch & Subordinate S2 Networks (Theory & Lab)

### Key Teaching Moments
*   **Smooth Y-Switch Integration:** Because the students already refactored the network to a flat `PN/IE_Plant` subnet in Session 2, integrating the Y-Switch here will be straightforward and compile without issues, reinforcing the network topology lesson they just learned.
*   **The DNA Redundancy Setting:** This is often missed. The switch will act as a generic unmanaged switch if DNA Redundancy is not explicitly checked in the module parameters.
*   **MRP Manager (Domain 3):** Explain *why* the Y-Switch is the manager. It isolates the MRP ring traffic of the subordinate S2 ring from the highly available R1 backbone. The backbone must not see Domain 3 traffic.

### Relevant Slide Content
Please reference `1500RH v21.pdf`:
*   **Slide: "PROFINET System Redundancy" (Around Slide 1472 - 1636)**: Review how the system redundancy works at the PROFINET layer, specifically how the Y-Switch functions as a proxy/bridge.
*   **Slide: "Switching behavior with PROFINET System Redundancy S2" (Around Slide 1826 - 1894)**: While discussing the Y-Switch, reinforce how the devices *behind* it will experience a switchover, relying on the S2 logical connections.
