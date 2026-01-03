# Siemens S7-1518HF Redundant System: Training & Integration Suite

## Module 1: System Architecture & Hardware Fundamentals

### 1.1 Learning Objectives
*   **Identify** and classify hardware components, distinguishing between Network (Profinet) and Power (Selectivity) devices.
*   **Analyze** the "Split Backbone" Topology: Two separate, non-interconnected R1 Rings.
*   **Differentiate** between Redundancy Roles: R1 (Physically Separated), S2 (System Redundant via Proxy), and the supporting infrastructure.
*   **Explain** the synchronization mechanism (H-Sync) which is the *only* link between the two redundant sides.

### 1.2 Hardware Inventory & Specifications
*   **Controllers:** 2x **CPU 1518HF-4 PN** (High Performance Fail-safe).
    *   *Role:* System Controllers.
    *   *Connectivity:* Each CPU manages its own separate Profinet Ring.
    *   *Sync:* Connected directly via Fiber Optic Patch Cables (H-Sync).
*   **Distribution Switches:** 2x **SCALANCE XC208**.
    *   *Role:* Backbone access switches.
    *   *Topology:* XC208-A belongs to Ring A; XC208-B belongs to Ring B. They are **not** interconnected.
*   **Network Management (Y-Switch):** 1x **SCALANCE XF204-DNA**.
    *   *Role:* Bridges the two separate Backbones to the Subordinate "Blue" Ring.
*   **Power Protection:** 2x **SEL1400 Selectivity Modules**.
    *   *Note:* These are **Electronic Circuit Breakers** for 24VDC distribution. They have **NO** Profinet network connection and are not part of the logical topology.
*   **Peripheral Devices:**
    *   **ET200SP (R1):** High-Availability IO connecting to *both* XC208-A and XC208-B.
    *   **ET200SP (S2):** Connected to the "Blue" Ring behind the DNA.
    *   **PN/PN Coupler:** Data exchange boundary.

### 1.3 Theoretical Architecture: The "Split Backbone"
Unlike a single large ring, this topology maximizes isolation.
*   **System A (Side A):** CPU A + XC208 A. Forms **Profinet Ring 1**.
*   **System B (Side B):** CPU B + XC208 B. Forms **Profinet Ring 2**.
*   **Isolation:** A catastrophic electrical failure or broadcast storm on Side A cannot physically propagate to Side B via the profinet copper cables, because there is no link.
*   **R1 Integration:** R1 devices bridge this gap by having two interface heads—one connected to Ring 1, one to Ring 2.

## Module 2: The Two R1 Backbones (mrpdomain-1)

### 2.1 Learning Objectives
*   **Configure** two independent MRP Rings in TIA Portal.
*   **Commission** the XC208 switches as the backbone for each side.
*   **Verify** total isolation between Side A and Side B networking.

### 2.2 Configuration in TIA Portal
**Concept:** You are configuring two identical but separate rings.

#### Step 1: CPU Configuration (Side A & B)
1.  **CPU A (Profinet X1):**
    *   **MRP Role:** Manager (Auto).
    *   **Domain:** `mrpdomain-1`.
    *   **Ports:** Select the ports creating the ring with XC208-A (e.g., P1 & P2).
2.  **CPU B (Profinet X1):**
    *   **MRP Role:** Manager (Auto).
    *   **Domain:** `mrpdomain-1` (Can share name as they are physically disjoint, or use `mrpdomain-1-b`).
    *   **Ports:** Select the ports creating the ring with XC208-B.

#### Step 2: Switch Configuration (XC208s)
*   **XC208-A:**
    *   Connects only to CPU A and Side A of R1 devices/DNA.
    *   **MRP Role:** Client.
    *   **Domain:** `mrpdomain-1`.
*   **XC208-B:**
    *   Connects only to CPU B and Side B of R1 devices/DNA.
    *   **MRP Role:** Client.
    *   **Domain:** `mrpdomain-1`.

### 2.3 Commissioning: The "Split" Verification
1.  **Physical Check:** Ensure NO ethernet cable connects XC208-A to XC208-B.
2.  **Online Check:**
    *   Go online with CPU A. View **Topology**.
    *   You should see a closed ring involving CPU A and XC208 A.
    *   CPU B should NOT be visible via the Profinet interface (only via H-Sync or if your PG is connected to both).

## Module 3: The Y-Switch (XF204-DNA) & MRP Domain 2

### 3.1 Learning Objectives
*   **Implement** the XF204-DNA as the bridge between the Split Backbones and the S2 Subordinate Ring.
*   **Configure** the Secondary Ring (`mrpdomain-2`).

### 3.2 The Y-Switch Connection
The XF204-DNA allows the single-interface devices in the "Blue Ring" to talk to the Redundant System.
*   **Port 1 (Upstream Left):** Connects to **XC208-A** (Side A).
*   **Port 2 (Upstream Right):** Connects to **XC208-B** (Side B).
*   **Function:** It acts as a client on *both* Backbone rings. If Ring A dies, it switches upstream traffic to Ring B.

### 3.3 Configuration in TIA Portal
1.  **Device Definition:** Add XF204-DNA.
2.  **Upstream (Ports 1 & 2):**
    *   These ports do NOT form a ring with each other. They connect to the separate Backbone rings.
    *   **MRP Role:** Client (for the upstream domains).
3.  **Downstream (Ports 3 & 4 - The Blue Ring):**
    *   **Domain:** `mrpdomain-2`.
    *   **Role:** **Manager (Auto)**.
    *   This creates the new ring for the S2 devices.

### 3.4 The Blue Ring (S2 Sub-System)
*   **Components:** ET200SP (S2), IE/PB Link, PN/PN Coupler (Side 1).
*   **Config:** All devices in the Blue Ring are **Clients** of `mrpdomain-2`.

## Module 4: Device Integration (R1 vs S2)

### 4.1 Learning Objectives
*   **Integrate** R1 Devices into the Split Topology.
*   **Configure** S2 Devices behind the DNA.

### 4.2 R1 Device Integration (The "True High Availability")
**Device:** ET200SP with 2x Interface Modules (IM) or R1-capable IM.
*   **Physical Connection:**
    *   **Left IM:** Connects to **XC208-A** (Ring 1).
    *   **Right IM:** Connects to **XC208-B** (Ring 2).
*   **Logic:** The R1 device is the *only* peripheral that physically spans both distinct networks. It can survive the loss of an entire Backbone side (e.g., XC208-A power failure) because it simply uses the Right IM connected to Side B.

### 4.3 S2 Device Integration
**Device:** Standard ET200SP in the Blue Ring.
*   **Connection:** Connected to the **XF204-DNA** (or other switches in Blue Ring).
*   **Logic:** It relies on the DNA to route its packets to either CPU A or CPU B.

### 4.4 PN/PN Coupler Integration
*   **Location:** Inside the Blue Ring (`mrpdomain-2`).
*   **Function:** Bridges the S2 network to the Subordinate "White" network.
*   **Wiring:**
    *   **X1 (Left):** Connects to the Blue Ring.
    *   **X2 (Right):** Connects to the external Subordinate PLC.

## Module 5: Redundancy Validation Plan

### 5.1 Validation Strategy
Since the networks are split, validation focuses on ensuring "Side A" and "Side B" can independently sustain the process.

### 5.2 Test A: The "Side A Blackout"
**Scenario:** Complete loss of the Primary Side infrastructure.
1.  **Action:** Power off **XC208-A**.
2.  **Impact:**
    *   **Backbone:** Ring A is dead.
    *   **R1 Devices:** Immediately switch to their Right IM (connected to XC208-B).
    *   **DNA:** Detects loss of Port 1 link, switches all upstream traffic to Port 2 (XC208-B).
    *   **S2 Devices:** Continue communication via DNA -> XC208-B -> CPU B.
    *   **CPUs:** If CPU A was Primary, H-Sync fails (or heartbeat lost), CPU B takes over.
3.  **Success Criteria:** Process continues without interruption.

### 5.3 Test B: The "DNA Split"
**Scenario:** Failure of one upstream path of the Y-Switch.
1.  **Action:** Unplug cable between **XC208-B** and **XF204-DNA**.
2.  **Impact:**
    *   DNA switches all traffic to XC208-A.
    *   **Status:** "Redundancy Loss" indicated on HMI/Diagnostics, but IO OK.

### 5.4 Test C: The "H-Sync" Cut
**Scenario:** Loss of sync cable between CPUs.
1.  **Action:** Unplug Fiber Sync cable.
2.  **Impact:**
    *   System enters "Solo" mode.
    *   Both CPUs might try to be Primary, but since networks are split, they can't fight over the same IO *except* for the R1/DNA devices.
    *   *Critical Check:* Ensure R1/DNA logic prevents "Input flickering" or double-driving outputs (handled by System Redundancy IDs).

### 5.5 Final Handover Checklist
*   [ ] **Power:** Confirmed SEL1400s are powering devices correctly (24VDC), unrelated to Profinet.
*   [ ] **Topology:** Confirmed "Split Backbone" (No cable between XC208 A and B).
*   [ ] **MRP:** `mrpdomain-1` closed on Side A and Side B independently. `mrpdomain-2` closed on Blue Ring.
*   [ ] **Validation:** Passed "Side A Blackout" test.
