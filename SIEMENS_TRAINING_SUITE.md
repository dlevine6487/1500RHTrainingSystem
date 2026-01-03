# Siemens S7-1518HF Redundant System: Training & Integration Suite

## Module 1: System Architecture & Hardware Fundamentals

### 1.1 Learning Objectives
*   **Identify** the specific MRP Domain structure (Domains 1, 2, & 3) for the Split Backbone topology.
*   **Differentiate** between Redundancy Roles: R1 (Physically Separated), S2 (System Redundant via Proxy), and Power components (SEL1400).
*   **Explain** the synchronization mechanism (H-Sync) which is the *only* link between the two redundant sides.

### 1.2 Hardware Inventory & Specifications
*   **Controllers:** 2x **CPU 1518HF-4 PN**.
    *   *PLC A:* Manages MRP Domain 1 (Side A).
    *   *PLC B:* Manages MRP Domain 2 (Side B).
    *   *Sync:* Connected directly via Fiber Optic Patch Cables (H-Sync).
*   **Distribution Switches:** 2x **SCALANCE XC208**.
    *   *XC208-A:* Part of MRP Domain 1.
    *   *XC208-B:* Part of MRP Domain 2.
*   **Network Management (Y-Switch):** 1x **SCALANCE XF204-DNA**.
    *   *Upstream:* Connects to Domain 1 and Domain 2.
    *   *Downstream:* Manages MRP Domain 3 (Blue Ring).
*   **Power Protection:** 2x **SEL1400 Selectivity Modules**.
    *   *Note:* Electronic Circuit Breakers (24VDC). No Profinet connection.
*   **Peripheral Devices:**
    *   **ET200SP (R1):** Connects to Domain 1 and Domain 2 simultaneously.
    *   **ET200SP (S2):** Connected to Domain 3.

### 1.3 Theoretical Architecture: The Three Domains
The topology is segmented into three distinct Media Redundancy (MRP) Domains:
1.  **MRP Domain 1:** The "Side A" Backbone (Ring 1).
2.  **MRP Domain 2:** The "Side B" Backbone (Ring 2).
3.  **MRP Domain 3:** The "Subordinate" S2 Ring (Ring 3).

## Module 2: The Two R1 Backbones (MRP Domains 1 & 2)

### 2.1 Learning Objectives
*   **Configure** distinct MRP Domains for Side A and Side B.
*   **Commission** the separate backbone rings.

### 2.2 Configuration in TIA Portal

#### Step 1: PLC A (Side A) - MRP Domain 1
1.  Open **Device Configuration** > **PLC A** > **Profinet Interface**.
2.  **Media Redundancy:**
    *   **Domain:** Create/Select `mrpdomain-1`.
    *   **Role:** **Manager (Auto)**.
    *   **Ring Ports:** Select ports connected to XC208-A.

#### Step 2: PLC B (Side B) - MRP Domain 2
1.  Open **Device Configuration** > **PLC B** > **Profinet Interface**.
2.  **Media Redundancy:**
    *   **Domain:** Create/Select `mrpdomain-2` (Must be distinct from Domain 1).
    *   **Role:** **Manager (Auto)**.
    *   **Ring Ports:** Select ports connected to XC208-B.

#### Step 3: Switch Configuration
*   **XC208-A:**
    *   **Domain:** `mrpdomain-1`.
    *   **Role:** Client.
*   **XC208-B:**
    *   **Domain:** `mrpdomain-2`.
    *   **Role:** Client.

### 2.3 Commissioning Verification
*   **Check 1:** Go online with PLC A. Verify `mrpdomain-1` is "Ring Closed".
*   **Check 2:** Go online with PLC B. Verify `mrpdomain-2` is "Ring Closed".
*   *Note:* These two rings operate completely independently. A break in Domain 1 does not affect Domain 2 status.

## Module 3: The Y-Switch (XF204-DNA) & MRP Domain 3

### 3.1 Learning Objectives
*   **Configure** the XF204-DNA to participate in three different domains.
*   **Manage** the S2 Subordinate Ring (Domain 3).

### 3.2 The Y-Switch Connections
The XF204-DNA is a unique device that sits at the intersection of all three domains.
*   **Port 1 (Upstream Left):** Connects to **XC208-A**.
    *   *Configuration:* **Client** in **MRP Domain 1**.
*   **Port 2 (Upstream Right):** Connects to **XC208-B**.
    *   *Configuration:* **Client** in **MRP Domain 2**.
*   **Ports 3 & 4 (Downstream):** Form the **Blue Ring**.
    *   *Configuration:* **Manager (Auto)** in **MRP Domain 3**.

### 3.3 Configuration Steps
1.  **Add XF204-DNA** to the project.
2.  **Upstream Ports:**
    *   Assign Port 1 to the subnet of PLC A. Set Media Redundancy to Client (`mrpdomain-1`).
    *   Assign Port 2 to the subnet of PLC B. Set Media Redundancy to Client (`mrpdomain-2`).
3.  **Downstream Ports:**
    *   Create a new domain: `mrpdomain-3`.
    *   Set Media Redundancy to **Manager (Auto)**.

### 3.4 The Blue Ring (S2 Sub-System)
*   **Components:** ET200SP (S2), IE/PB Link, PN/PN Coupler.
*   **Config:** All devices in the Blue Ring must be set to **Client** in **MRP Domain 3**.

## Module 4: Device Integration (R1 vs S2)

### 4.1 R1 Device Integration (Dual Domain)
**Device:** ET200SP with High Availability IM.
*   **Left Interface (IM 1):** Connects to **XC208-A**.
    *   *MRP:* Client in **Domain 1**.
*   **Right Interface (IM 2):** Connects to **XC208-B**.
    *   *MRP:* Client in **Domain 2**.
*   **Result:** The device is reachable via either domain.

### 4.2 S2 Device Integration (Single Domain)
**Device:** Standard ET200SP behind DNA.
*   **Connection:** Ring Port 1 / Ring Port 2 connected to Domain 3.
*   **MRP:** Client in **Domain 3**.
*   **Result:** Reachable by both PLCs via the DNA Proxy.

## Module 5: Redundancy Validation Plan

### 5.1 Validation Strategy
Validate the independence of the three domains.

### 5.2 Test A: Domain 1 Failure
**Action:** Break the ring in MRP Domain 1 (Unplug cable XC208-A <-> PLC A).
**Observation:**
*   PLC A reports `mrpdomain-1` Ring Open.
*   PLC B reports `mrpdomain-2` Ring Closed (Unaffected).
*   Process continues.

### 5.3 Test B: Domain 2 Failure
**Action:** Power off XC208-B.
**Observation:**
*   `mrpdomain-2` fails completely.
*   R1 Devices switch to Left Interface (Domain 1).
*   DNA switches upstream traffic to Port 1 (Domain 1).
*   Process continues.

### 5.4 Test C: Domain 3 (Blue Ring) Break
**Action:** Unplug cable in the Blue Ring.
**Observation:**
*   XF204-DNA reports `mrpdomain-3` Ring Open.
*   S2 Devices remain reachable via the alternative path in Domain 3.
*   Upstream Backbone (Domains 1 & 2) remains Closed/Unaffected.

### 5.5 Final Handover Checklist
*   [ ] **Domain 1:** Configured and Closed (Side A).
*   [ ] **Domain 2:** Configured and Closed (Side B).
*   [ ] **Domain 3:** Configured and Closed (Blue Ring/DNA).
*   [ ] **DNA:** Verified as Client in D1/D2 and Manager in D3.
*   [ ] **R1 Devices:** Verified connections to both D1 and D2.
