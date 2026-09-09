# Siemens S7-1518HF Redundant System: Training & Integration Suite

Welcome to the Siemens S7-1518HF Training & Integration Suite. This documentation is organized into modular sections to guide you through the architecture, configuration, commissioning, and safety programming of a redundant Profinet system.

## Table of Contents

### Full Courses & Itineraries
*   **[6-Hour Practical Commissioning Course](6_Hour_Training_Course.md):** Hands-on labs focusing on R1 Backbone, Y-Switch, and S2 Subordinate Ring configuration.
    * *Includes:*
        * *[Lab 1: Commissioning the R1 Network](6_Hour_Training_Materials/Lab_1_Commissioning_R1.md)*
        * *[Lab 2: The Y-Switch & Subordinate S2 Networks](6_Hour_Training_Materials/Lab_2_YSwitch_S2.md)*
        * *[Lab 3: Populating the S2 Ring & Watchdog Tuning](6_Hour_Training_Materials/Lab_3_S2_Watchdogs.md)*
        * *[Lab 4: Failure Scenarios & Diagnostics](6_Hour_Training_Materials/Lab_4_Failures.md)*
        * *[Instructor Notes](6_Hour_Training_Materials/Instructor_Notes.md)*
*   **[1-Hour Executive Overview Course](1_Hour_Training_Course.md):** A high-level theoretical overview without hands-on labs, ideal for management and architectural planning.

### Technical Deep-Dive Modules (Reference)
### [Module 1: R1 Backbone Architecture & Station Integration](Training_Modules/Module_1_R1_Backbone.md)
*   **Topics:** Split Backbone Topology, XC208 Switch Configuration (MRP Clients), R1 Remote IO Setup, Watchdog Tuning.

### [Module 2: S2 Subordinate System & Y-Switch](Training_Modules/Module_2_S2_Subordinate_System.md)
*   **Topics:** XF204-DNA (Y-Switch) Setup, Blue Ring Integration, S2 Devices, PN/PN Coupler.

### [Module 3: Hardware Tagging & IO Configuration](Training_Modules/Module_3_Hardware_Tagging.md)
*   **Topics:** Detailed IO Tag Lists and Assignments for ET200SP-A and ET200SP-B (Slots 4-11).

### [Module 4: Failsafe Programming & Cross-System Safety](Training_Modules/Module_4_Failsafe_Safety.md)
*   **Topics:** Failsafe IO Config (F-Monitoring Time), E-Stop Logic (ESTOP1), Safety Communication (SENDDP/RCVDP).

### [Module 5: High Availability IO with LRedIO](Training_Modules/Module_5_LRedIO_HighAvail.md)
*   **Topics:** 1oo2 Voting for Digital Inputs, Dual Drive for Digital Outputs, LRedIO Library Configuration.

### [Module 6: Commissioning & Validation Plan](Training_Modules/Module_6_Commissioning_Validation.md)
*   **Topics:** Validation Strategy, Failover Tests (Domain 1/2/3), Bumpless Transfer Verification.
