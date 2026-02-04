## Module 2: S2 Subordinate System & Y-Switch

### 2.1 Learning Objectives
*   **Bridge** the R1 Backbone to a single-interface "Blue Ring" using the XF204-DNA.
*   **Integrate** S2 Devices and PN/PN Couplers.
*   **Manage** MRP Domain 3.

### 2.2 Reference Documents
*   `Application_Examples_and_Docs/Redundancy_and_MRP/109816704_S7-1500H_with_Y-Switch_V1_0_en.pdf`
*   `Application_Examples_and_Docs/Redundancy_and_MRP/109926733_S2_Systemredundancy_S7-1500H_G220_DOC_v10_en.pdf`
*   `Application_Examples_and_Docs/Hardware_Components/pn_pn_coupler_hardware_manual_en-US_en-US.pdf`

### 2.3 The Y-Switch (XF204-DNA) Setup
The Y-Switch is the gateway. It connects as a client to the backbones and a manager to the Blue Ring.
1.  **Upstream (Ports 1 & 2):**
    *   Connect Port 1 to `Switch-A` -> **Client** of `mrpdomain-1`.
    *   Connect Port 2 to `Switch-B` -> **Client** of `mrpdomain-2`.
2.  **Downstream (Ports 3 & 4):**
    *   Create **MRP Domain 3**.
    *   Set Role to **Manager (Auto)**.

### 2.4 S2 Device Integration
Devices in the Blue Ring (Standard ET 200SP, IE/PB Link) are "S2".
1.  **Connection:** Connect to the Blue Ring (Domain 3).
2.  **MRP:** Set as **Client** in `mrpdomain-3`.
3.  **Assignment:** Assign to **PLC_1 (System)**.
4.  **Critical Configuration: Watchdog Tuning:**
    *   **Requirement:** Just like R1 devices, S2 devices must survive the system switchover time (approx 300ms) without disconnecting.
    *   **Action:** Adjust **Accepted update cycles without IO data** so that `Update Time * Cycles > 300ms`.
    *   *Example:* 2ms update time * 150 cycles = 300ms watchdog.

### 2.5 PN/PN Coupler Integration
The PN/PN Coupler allows data exchange with the external world.
1.  **Location:** Inside the Blue Ring.
2.  **Side 1 (X1):**
    *   Assign to Redundant System.
    *   Client of `mrpdomain-3`.
3.  **Side 2 (X2):**
    *   Assign to external PLC.
4.  **Transfer:** Configure I/O mapping bytes.
