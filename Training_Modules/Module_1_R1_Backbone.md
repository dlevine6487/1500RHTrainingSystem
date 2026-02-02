## Module 1: R1 Backbone Architecture & Station Integration

### 1.1 Learning Objectives
*   **Construct** the "Split Backbone" topology using S7-1518HF PLCs and XC208 switches.
*   **Commission** Remote IO Stations (R1) spanning both backbone rings.
*   **Configure** Profinet Watchdog Timers to ensure bumpless transfer during redundancy switchover.
*   **Implement** correct MRP Client settings for distribution switches.

### 1.2 Reference Documents
*   `Application_Examples_and_Docs/System_Architecture_and_Manuals/s71500rh_manual_en-US_en-US_v21.pdf`
*   `Application_Examples_and_Docs/Redundancy_and_MRP/109739614_MRP_DOKU_V10_en.pdf`
*   `Application_Examples_and_Docs/Hardware_Components/et200sp_im_155_6_pn_r1_en-US_en-US.pdf`

### 1.3 Architecture: The R1 Split Backbone
The foundation of the system is the **R1 Topology**. This consists of two physically isolated Profinet rings (Domains 1 & 2) that are logically unified by the R1 IO devices.
*   **Side A (Left):** PLC A + XC208-A + Left Interface of R1 IM. (Manages **MRP Domain 1**).
*   **Side B (Right):** PLC B + XC208-B + Right Interface of R1 IM. (Manages **MRP Domain 2**).
*   *Note:* There is no copper link between XC208-A and XC208-B.

### 1.4 Step-by-Step: XC208 Switch Configuration
The XC208 switches act as the entry points for the R1 IO. They must be configured as **MRP Clients** to allow the PLCs (Managers) to control the ring.

**Procedure for XC208-A (Side A):**
1.  **Add Device:** Drag `SCALANCE XC208` from the Hardware Catalog to Network View.
2.  **Assign Name:** Set Profinet Name to `switch-backbone-a`.
3.  **MRP Configuration:**
    *   Navigate to **Profinet Interface** > **Advanced Options** > **Media Redundancy**.
    *   **Domain:** Select `mrpdomain-1`.
    *   **Role:** **Client**.
    *   **Ring Ports:** Select the two ports forming the ring with PLC A (e.g., P1 & P2).
4.  **Port Configuration:** Ensure the port facing the R1 IO Station (e.g., P5) is *not* a ring port but is active.

**Procedure for XC208-B (Side B):**
1.  **Add Device:** Add second XC208. Name: `switch-backbone-b`.
2.  **MRP Configuration:**
    *   **Domain:** Select `mrpdomain-2`.
    *   **Role:** **Client**.
    *   **Ring Ports:** Select ports forming ring with PLC B.

### 1.5 Step-by-Step: R1 Remote IO Setup (ET 200SP)
An R1 Station uses an **IM 155-6 PN R1** interface module. This module essentially contains two separate network adapters in one head unit.

1.  **Hardware Selection:**
    *   Catalog: `Distributed I/O` > `ET 200SP` > `Interface Modules` > `Profinet` > `IM 155-6 PN R1`.
    *   Drag into Network View.
2.  **Controller Assignment:**
    *   Click the "Not Assigned" link on the device.
    *   Select **PLC_1 (System)** (This assigns it to the Redundant Pair, not just one PLC).
3.  **Topology Connection:**
    *   **Port 1 (Interface 1):** Connect to `switch-backbone-a` (Side A).
    *   **Port 2 (Interface 2):** Connect to `switch-backbone-b` (Side B).
    *   *Result:* TIA Portal recognizes this as a valid R1 connection spanning the two redundant subnets.

### 1.6 Critical Configuration: Watchdog & Update Time
**The Challenge:** When a Primary PLC fails, it takes time (up to 300ms) for the Backup PLC to assume control and send new data. If the IO Station's "Watchdog" timer expires *before* the Backup takes over, the outputs will turn off (flicker) before coming back on. This breaks "Bumpless Transfer".

**The Formula:**
`Watchdog Time = Update Time x Accepted Update Cycles`

**Configuration Strategy:**
1.  **Navigate:** Go to the R1 Station > **Profinet Interface** > **Advanced Options** > **Real time settings** > **IO Cycle**.
2.  **Update Time:** Set a stable update time (e.g., **2.0 ms** or **4.0 ms**). Faster is not always better in redundant systems.
3.  **Watchdog (Accepted Update Cycles):**
    *   Standard default is often 3 cycles (2ms x 3 = 6ms). **This is too short for redundancy.**
    *   **Requirement:** Watchdog must be > System Switchover Time (approx 300ms).
    *   **Calculation:** 300ms / 2ms = 150 cycles.
    *   **Action:** Change "Accepted update cycles without IO data" to a manual value, e.g., **150** or **200**.
    *   *Target:* Ensure the calculated Watchdog Time is **> 300ms** (e.g., 400ms to be safe).
4.  **Verify:** Check the "Watchdog time" field updates to reflect the new calculation.
