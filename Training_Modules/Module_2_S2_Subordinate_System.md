## Module 2: S2 Subordinate System & Y-Switch

### 2.1 Learning Objectives
*   **Bridge** the R1 Backbone to a single-interface "Blue Ring" using the XF204-DNA.
*   **Integrate** S2 Devices and PN/PN Couplers into a dedicated MRP domain.
*   **Manage** MRP Domain 3 (Blue Ring) with the Y-Switch acting as the Manager.
*   **Commission** Client devices: IEPBLinkHA-A, ET200SP-B, and PNPNCoupler-B.

### 2.2 Reference Documents
*   `Application_Examples_and_Docs/Redundancy_and_MRP/109816704_S7-1500H_with_Y-Switch_V1_0_en.pdf`
*   `Application_Examples_and_Docs/Redundancy_and_MRP/109926733_S2_Systemredundancy_S7-1500H_G220_DOC_v10_en.pdf`
*   `Application_Examples_and_Docs/Hardware_Components/pn_pn_coupler_hardware_manual_en-US_en-US.pdf`

### 2.3 The Y-Switch (XF204-DNA) Setup
The Y-Switch (XF204-DNA) acts as the bridge between the redundant R1 backbone and the single-interface S2 devices. Its configuration is critical:

**1. Backbone Uplinks (Standard Ethernet):**
*   **Port 1:** Connect to `Switch-A` (Side A).
*   **Port 2:** Connect to `Switch-B` (Side B).
*   **Crucial:** These connections are **NOT** part of MRP Domain 1 or MRP Domain 2. They are standard Profinet uplinks. Do not assign them to a ring port role for the backbone domains.

**2. Subordinate Ring Manager (MRP Domain 3):**
*   **Ports 3 & 4:** These ports form the "Blue Ring" (Subordinate Ring).
*   **MRP Configuration:**
    *   Create a new domain: **mrpdomain-3**.
    *   Assign the Y-Switch Role: **Manager (Auto)**.
    *   Assign Ring Ports: **Port 3** and **Port 4**.

### 2.4 MRP Domain 3 Clients
All devices connected to the subordinate Blue Ring must be configured as **Clients** of `mrpdomain-3`. The Y-Switch manages this ring to ensure redundancy if a cable breaks within the ring.

**Device List & Configuration:**

1.  **IEPBLinkHA-A (IE/PB Link):**
    *   **Role:** MRP Client
    *   **Domain:** mrpdomain-3
    *   **Assignment:** Assigned to `PLC_1 (System)` (S2 Redundancy).

2.  **ET200SP-B (Standard IO Station):**
    *   **Role:** MRP Client
    *   **Domain:** mrpdomain-3
    *   **Assignment:** Assigned to `PLC_1 (System)` (S2 Redundancy).
    *   *Note:* This station contains the bulk of the process IO for Side B.

3.  **PNPNCoupler-B (PN/PN Coupler):**
    *   **Role:** MRP Client
    *   **Domain:** mrpdomain-3
    *   **Assignment:** Assigned to `PLC_1 (System)` on Side 1 (X1).
    *   *Function:* Used for safety data exchange with the external Safety PLC (PLC-C).

### 2.5 Critical Configuration: Watchdog Tuning (S2 Devices)
Just like R1 devices, S2 devices connected via the Y-Switch must survive the system switchover time (approx 300ms) without disconnecting.

*   **Requirement:** The Watchdog Timer must be > 300ms.
*   **Action:**
    1.  Go to the device's **Profinet Interface** > **Advanced Options** > **Real time settings**.
    2.  Set a stable **Update Time** (e.g., 2.0ms, 4.0ms).
    3.  Adjust **Accepted update cycles without IO data** so that `Update Time * Cycles > 300ms`.
    *   *Example:* 4ms update time * 100 cycles = 400ms watchdog.

### 2.6 PN/PN Coupler Integration
The PN/PN Coupler acts as the gateway to the external world (PLC-C).
1.  **Location:** Physically located in the Blue Ring (Domain 3).
2.  **Side 1 (X1 - Left):**
    *   **Controller:** Assigned to our Redundant System (PLC_1).
    *   **MRP:** Client of `mrpdomain-3`.
3.  **Side 2 (X2 - Right):**
    *   **Controller:** Assigned to external PLC-C.
    *   **MRP:** Not part of our domain (unless configured by the external PLC).
4.  **Data Transfer:** Configure Transfer Areas for both Standard and Failsafe (Safety) data exchange.
