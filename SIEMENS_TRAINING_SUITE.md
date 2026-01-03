# Siemens S7-1518HF Redundant System: Training & Integration Suite

## Module 1: System Architecture & Hardware Fundamentals

### 1.1 Learning Objectives
*   **Identify** and classify hardware components within the S7-1518HF Redundant topology.
*   **Differentiate** between Redundancy Roles: S1 (Standard), S2 (System Redundant), and R1 (Redundant Interface).
*   **Understand** the physical implementation of the "Green" (Backbone) and "Blue" (Subordinate) Profinet rings.
*   **Explain** the synchronization mechanism (H-Sync) and its role in Bumpless Transfer.

### 1.2 Hardware Inventory & Specifications
*   **Controllers:** 2x **CPU 1518HF-4 PN** (High Performance Fail-safe).
    *   *Role:* System Controllers.
    *   *Sync:* Connected via Fiber Optic Patch Cables (H-Sync) for cycle-synchronous operation.
*   **Network Management (Y-Switch):** 1x **SCALANCE XF204-DNA**.
    *   *Role:* Dual Network Access (DNA) proxy. Bridges the Redundant System (Primary Ring) to single-interface devices (Secondary Ring).
*   **Distribution Switches:**
    *   2x **SCALANCE XC208** (Designated as S1 devices).
    *   2x **SEL1400** (Connecting R1 peripherals).
*   **Peripheral Devices:**
    *   **ET200SP (R1):** High-Availability IO with redundant interface capabilities.
    *   **ET200SP (S2):** System Redundant IO supporting dual Application Relations (ARs).
    *   **IE/PB Link HA:** Gateway for Profibus DP integration (on S2/Blue ring).
    *   **PN/PN Coupler:** Data exchange boundary to External/Subordinate networks.

### 1.3 Theoretical Architecture: The "Bumpless" Concept
The S7-1518HF system operates on a **Primary/Backup** model.
1.  **Primary CPU:** Executes the user program and controls IO.
2.  **Backup CPU:** Synchronously follows the Primary. It does *not* execute code independently but receives "Event-Sync" data frames via the H-Sync cables.
3.  **Bumpless Transfer:** In the event of a Primary failure, the Backup takes over immediately.
    *   *Mechanism:* Because the Backup memory is a mirror of the Primary, it resumes program execution at the exact instruction where the Primary failed.
    *   *IO Hold Time:* Output modules maintain their last valid state during the switchover (typically < 300ms) to ensure process continuity.

### 1.4 Topology Breakdown (Based on Diagram)
*   **The Backbone (MRP Domain 1 - Green):**
    *   Contains PLC A, PLC B, XC208 A, XC208 B.
    *   This is a physical **Ring Topology** configured with **Media Redundancy Protocol (MRP)**.
    *   *Critical:* PLC A and PLC B are physically part of the ring to ensure they can reach all switches even if one path is cut.
*   **The S2 Sub-System (MRP Domain 2 - Blue):**
    *   Managed by the **XF204-DNA**.
    *   The XF204-DNA connects to XC208 A and B (Yellow lines) effectively "dual-homing" the Blue Ring to the Redundant System.
    *   Devices in the Blue Ring (S2 ET200SP, IE/PB Link) appear to the CPUs as accessible via either XC208 path.

### 1.5 Commissioning Step: Hardware Verification
*   **Action 1:** Verify Fiber Sync Cables are crossed (Tx to Rx) and Link LEDs are Solid Green on both CPUs.
*   **Action 2:** Verify Firmware consistency. Both CPUs *must* run the exact same FW version.
*   **Action 3:** Assign unique Device Names to every component. In Profinet, *Name* is the primary identifier, not IP.
    *   *Naming Convention:* `plc-a`, `plc-b`, `switch-s1-a`, `xf204-dna`, `et200sp-s2`.

## Module 2: Profinet Backbone & MRP Domain 1 Configuration

### 2.1 Learning Objectives
*   **Configure** the Primary Ring (Green Topology) in TIA Portal.
*   **Assign** Media Redundancy Protocol (MRP) Roles: Manager vs. Client.
*   **Commission** the S1 Devices (XC208) and R1 Connection Points (SEL1400).

### 2.2 Technical Deep Dive: MRP Domain 1
The "Green" ring ensures that a single cable break does not isolate the Primary or Backup PLC from the rest of the network.
*   **Domain Name:** `mrpdomain-1` (Default).
*   **Participants:**
    *   CPU 1518HF (Side A)
    *   CPU 1518HF (Side B)
    *   XC208 (Side A)
    *   XC208 (Side B)
    *   SEL1400 (Side A & B)

### 2.3 MRP Configuration in TIA Portal
**Crucial Rule:** Every device in the ring *must* belong to the same MRP Domain and have correct Port settings.

#### Step 1: CPU Configuration
1.  Open **Device Configuration** > **CPU 1518HF** > **Profinet Interface [X1]**.
2.  Navigate to **Media Redundancy**.
3.  **Role:** Set to **Manager (Auto)**.
    *   *Why Auto?* In a redundant system, if the Primary PLC fails (who might be the active Manager), the Backup must take over or the ring management must be handled resiliently. Setting both CPUs to "Manager (Auto)" allows them to negotiate the active manager.
4.  **Domain:** `mrpdomain-1`.
5.  **Ring Ports:** Select the physical ports connected to the ring (e.g., Port 1 and Port 2). *Verify against physical wiring.*

#### Step 2: Switch Configuration (XC208 & SEL1400)
1.  Open **Device Configuration** for each switch.
2.  Navigate to **Media Redundancy**.
3.  **Role:** Set to **Client**.
4.  **Domain:** `mrpdomain-1`.
5.  **Ring Ports:** Explicitly define which two ports form the ring entrance/exit.
    *   *Warning:* Failing to mark the correct ports as "Ring Ports" will cause the MRP Manager to see the ring as "Open" permanently, disabling redundancy.

### 2.4 Commissioning Procedure: The Backbone
1.  **Physical Connection:** Connect the Green Ring cables *except one*.
2.  **Download:** Perform a hardware download to all devices (CPUs and Switches).
3.  **Close the Ring:** Connect the final cable.
4.  **Verification:**
    *   Go to **Online & Diagnostics** > **Profinet Interface** > **Media Redundancy** on the Primary CPU.
    *   **Status:** Should read **"Ring Closed"**.
    *   **Redundancy Status:** Should read **"Passive"** (meaning the ring is intact, and the Manager is blocking the redundant path to prevent loops).
5.  **Failure Test:** Unplug *one* cable in the Green Ring.
    *   **Status:** Should change to **"Ring Open"**.
    *   **Communication:** Verify PING to all switches and the Backup CPU remains uninterrupted.
    *   **Recovery:** Plug the cable back in. Status should return to "Ring Closed".


## Module 3: The Y-Switch (XF204-DNA) & MRP Domain 2

### 3.1 Learning Objectives
*   **Understand** the function of the XF204-DNA as a Y-Switch Proxy.
*   **Configure** MRP Domain 2 (Blue Ring) for the Subordinate S2 System.
*   **Implement** the "S2" System Redundancy topology.

### 3.2 Technical Deep Dive: The DNA Proxy
The **XF204-DNA** (Dual Network Access) is the critical link. Standard Profinet devices (like the IE/PB Link or standard ET200SP) generally have one interface. To connect them to a Redundant System (which has two PLCs and effectively two paths), we use the DNA.
*   **Upstream (Yellow Lines):** Connected to XC208-A and XC208-B. The DNA presents itself as a single S2 device to the R/H System.
*   **Downstream (Blue Lines):** Creates a *secondary* ring (`mrpdomain-2`). The DNA acts as the **MRP Manager** for this domain.
*   **Proxy Function:** It "hides" the complexity of the Blue Ring from the Main CPUs. The Main CPUs just see "Device X is reachable".

### 3.3 Configuration in TIA Portal (XF204-DNA)

#### Step 1: Network Integration
1.  Drag the **XF204-DNA** from the Catalog into the Network View.
2.  Connect Port 1 and Port 2 (typically) to the **XC208 switches** (Upstream).
3.  Connect Port 3 and Port 4 to the **Blue Ring** (Downstream).

#### Step 2: Parameterization
1.  **Properties** > **General**: Set the Device Name (e.g., `y-switch-dna`).
2.  **Media Redundancy (Upstream - Implicit):** The DNA supports S2 system redundancy automatically when connected to the R/H system. Ensure it is assigned to the Primary PLC.
3.  **Media Redundancy (Downstream - MRP Domain 2):**
    *   Navigate to **Media Redundancy** settings for the ports facing the Blue Ring.
    *   **Domain:** Create a NEW domain: `mrpdomain-2`.
    *   **Role:** Set to **Manager (Auto)**.
        *   *Note:* The DNA *must* be the Manager of the Blue Ring. The devices inside (ET200SP, PB Link) will be Clients.

### 3.4 Configuring Blue Ring Devices (S2 Devices)
Devices inside the Blue Ring (S2 ET200SP, IE/PB Link) are technically "S2" because they are reachable by both CPUs via the DNA.
1.  **ET200SP / IE/PB Link:**
    *   **Media Redundancy Role:** Client.
    *   **Domain:** `mrpdomain-2`.
2.  **IO Controller Assignment:** Assign these devices to the **Primary PLC**. The System Redundancy layer handles the switchover path automatically via the DNA.

### 3.5 Commissioning Procedure: The Y-Switch
1.  **Validation:** Ensure the DNA shows no errors on the device faceplate (LEDs).
2.  **MRP Check:**
    *   Go Online with the DNA. Check **MRP Status** for `mrpdomain-2`.
    *   It should be "Ring Closed" (assuming Blue cables are connected).
3.  **Path Verification:**
    *   Disconnect the cable between **XC208-A** and **DNA**.
    *   *Result:* The DNA should switch traffic to **XC208-B** instantly.
    *   *Observation:* The devices in the Blue Ring (e.g., S2 ET200SP) should **NOT** lose connection or go to Stop.


## Module 4: Device Integration (R1 vs S2) & PN/PN Coupler

### 4.1 Learning Objectives
*   **Integrate** R1 Devices for maximum availability (Backbone).
*   **Configure** the PN/PN Coupler for safe data exchange with Subordinate Networks.
*   **Differentiate** IO handling between R1 and S2 devices.

### 4.2 R1 Device Integration (The "R1 ET200SP")
**Concept:** An R1 device uses two Interface Modules (or a specialized R1 IM) to connect simultaneously to two different switches. This tolerates a failure of the switch itself, not just the cable.
*   **Topology Location:** Connected to the SEL1400 switches (Side A and Side B).
*   **Configuration:**
    1.  **Hardware:** Ensure the ET200SP has the correct IM (e.g., IM 155-6 PN R1).
    2.  **Connections:**
        *   Port 1 -> SEL1400 Side A.
        *   Port 2 -> SEL1400 Side B.
    3.  **TIA Portal:**
        *   Assign to **Both** CPUs (System Redundancy).
        *   Unlike S2 (which looks like one device), R1 is aware of the dual path at the Interface level.

### 4.3 S2 Device Integration (The "S2 ET200SP")
**Concept:** S2 devices support a logical connection to both CPUs but typically have a single physical attachment point (or a ring attachment). In this topology, they sit behind the **XF204-DNA**.
*   **Topology Location:** Inside the Blue Ring (`mrpdomain-2`).
*   **Data Flow:**
    *   Primary CPU sends data -> XC208 -> DNA -> S2 Device.
    *   If Primary CPU fails, Backup CPU sends data -> XC208 (other side) -> DNA -> S2 Device.
    *   *Note:* The S2 device maintains an Application Relation (AR) with *both* CPUs. One is Active, one is Backup.

### 4.4 PN/PN Coupler Configuration
The **PN/PN Coupler** bridges the "Blue" S2 network to the "White" Subordinate network.
*   **Purpose:** Allows the Redundant System to exchange data with the "Subordinate PLC" without merging their safety/sync domains.
*   **Configuration:**
    1.  **X1 Interface (Left):** Connects to the Blue Ring (`mrpdomain-2`).
        *   *Assignment:* Assign to the Redundant System (via DNA path).
        *   *Name:* `pnpn-coupler-x1`.
    2.  **X2 Interface (Right):** Connects to the Subordinate PLC.
        *   *Assignment:* Assign to the Subordinate PLC.
        *   *Name:* `pnpn-coupler-x2`.
    3.  **Transfer Area:** Create a generic IO module (e.g., 64 Bytes In/Out).
        *   *Mapping:* Data written to Output Byte 0 on X1 appears as Input Byte 0 on X2.

### 4.5 Commissioning Procedure: Cross-System Check
1.  **PN/PN Coupling:**
    *   Force an Output bit on the Redundant System side (X1).
    *   Monitor the Input bit on the Subordinate PLC side (X2).
    *   *Latency Check:* Ensure update times are acceptable (typically < 16ms).
2.  **R1 Resilience:**
    *   Power off **SEL1400 Side A**.
    *   *Result:* The R1 ET200SP should seamlessly switch to using **SEL1400 Side B**. The IO should *not* flicker.


## Module 5: Commissioning, Bumpless Transfer & Validation Plan

### 5.1 Learning Objectives
*   **Execute** the Redundancy Validation Plan.
*   **Verify** Bumpless Transfer (Cycle-synchronous switching).
*   **Analyze** Diagnostic Buffer events during failover.

### 5.2 Theoretical: The "Bumpless" Mechanism
Bumpless transfer relies on the **H-Sync** link.
1.  **Synchronization:** The Primary CPU sends a snapshot of its data/state to the Backup at the beginning of every cycle.
2.  **Switchover:** If the Primary fails, the Backup misses the sync frame. It waits for the configured *Redundancy Monitoring Time* (e.g., 300ms). If no signal, it promotes itself to Primary.
3.  **Output Hold:** During this gap, IO modules hold their last value.
4.  **Resume:** The new Primary resumes execution from the exact point of the last sync.

### 5.3 Redundancy Validation Plan (Step-by-Step)

#### Test A: The "Power Pull" (Primary CPU Failure)
**Objective:** Verify system survival when the Primary PLC loses power.
1.  **Setup:** System in RUN-Redundant mode (Both CPUs Green, LEDs "PRI" and "BACK"). Process running (e.g., a counter incrementing).
2.  **Action:** Pull the power plug on **CPU Side A** (Primary).
3.  **Observation:**
    *   CPU Side B immediately switches LED to "PRI".
    *   The "ERROR" LED might flash briefly.
    *   **Process Check:** Does the counter skip a beat? Does the HMI show "Connection Lost"? (It should not, or reconnect instantly).
4.  **Recovery:** Restore power to CPU Side A.
    *   It should boot up, Sync (Amber LEDs blinking), and enter RUN-Redundant as **Backup**.

#### Test B: The "Fiber Cut" (H-Sync Failure)
**Objective:** Verify behavior when synchronization is lost but both CPUs are healthy.
1.  **Setup:** System in RUN-Redundant.
2.  **Action:** Unplug **one** Fiber Sync cable.
3.  **Observation:**
    *   System drops to **System State: Solo**.
    *   The Primary CPU continues running.
    *   The Backup CPU goes to STOP (because it cannot sync).
    *   *Critical:* The process must NOT stop.

#### Test C: The "Backbone Break" (MRP Healing)
**Objective:** Verify MRP Domain 1 healing time.
1.  **Setup:** Ping the Backup CPU from a laptop connected to XC208-A.
2.  **Action:** Disconnect the cable between **XC208-A** and **SEL1400-A**.
3.  **Observation:**
    *   Ping sequence: You might see 1 dropped packet (approx 200ms interruption).
    *   Communication continues via the alternative ring path.

#### Test D: The "Y-Switch" Failover
**Objective:** Verify S2 Device continuity.
1.  **Setup:** Monitor an Input on the S2 ET200SP (Blue Ring).
2.  **Action:** Disconnect the "Yellow" cable connecting **XC208-A** to the **XF204-DNA**.
3.  **Observation:**
    *   The XF204-DNA should switch its upstream path to XC208-B.
    *   The Input on S2 ET200SP should remain True.
    *   Diagnostic Buffer on PLC will log a "Station Failure" followed immediately by "Station Return" (or just a port state change if seamless).

### 5.4 Final Handover Checklist
*   [ ] All Device Names assigned and labeled.
*   [ ] Firmware consistent across redundant pairs.
*   [ ] MRP Domains (1 & 2) "Ring Closed".
*   [ ] Redundancy State: "RUN-Redundant".
*   [ ] H-Sync Link Quality: Good (checked in TIA Online).
*   [ ] Validation Tests A-D passed and documented.
