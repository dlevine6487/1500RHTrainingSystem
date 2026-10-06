# Instructor Notes: Session 2 - Commissioning R1

## 10:00 - 11:30 | Commissioning the R1 Network (Hands-On Lab & Pivot 1)

### Key Teaching Moments
*   **The Initial Setup Pinch Point & Flat Network Transition:** The lab purposefully instructs students to build the initial R1 network using two separate PROFINET subnets (`PN/IE_1` and `PN/IE_2` / `PN/IE_PlantA` and `PN/IE_PlantB`). This works perfectly for the pure R1 ET200SP and compiles successfully. However, the pinch point arrives immediately afterward in this same session when they try to add the standard network switches.
*   **The S2 Compilation Failure (The "Why"):** When students attempt to compile with the standard switches on disjoint subnets, it will fail. This is the moment to present the theory from the slides:
    *   *Option 1 (Subnet Separation)* allowed them to build the pure R1 ET200SP, but it *cannot* support a multi-assigned S2 connection for standard switches.
    *   *Option 2 (Shared Subnet)* is structurally required to satisfy TIA Portal compilation. To merge R1 components with S2 devices, the controllers and IO must share a common, flat subnet.
    *   Force them to delete the disjoint subnets, create the unified `PN/IE_Plant` subnet, and then show them how this flat network allows the standard switches to compile successfully as S2 multi-assigned devices.
*   **Watch for:** Students accidentally cross-connecting the MRP domains. Domain 1 and Domain 2 must be strictly separate. If a student creates an MRP ring that encompasses both switches and both PLC sides, the network will storm and the CPUs will fail to sync.
*   **Pivot Point 1 (Presenter Handoff):** Once the students finish configuring the R1 ET200SP (before the switch failure pinch point), hand off to the presenter for a 15-minute presentation showcasing the ET200SP HA as another R1 device available in the Siemens portfolio.

### Relevant Slide Content
Please reference `1500RH v21.pdf`:
*   **Slide: "Network Configuration Examples S7-1500H" (Around slide 2776 - 3255)**: Highlight the topologies showing disjoint rings for pure R1 setups vs the shared flat subnet required when integrating S2/S1 switches.
*   **Slide: "Configuration of System Redundancy R1" (Starts around Slide 2510)**: Show how TIA Portal handles the domain assignments and the physical/logical mapping of R1 interfaces.
*   **Slide: "Siemens Products with System Redundancy R1" (Starts around Slide 2380)**: Relevant for Pivot Point 1, discussing other R1 devices like the ET200SP HA.
