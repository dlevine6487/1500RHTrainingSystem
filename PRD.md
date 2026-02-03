# Product Requirements Document (PRD)
## Siemens S7-1518HF Redundant System Training Suite

### 1. Overview
The **Siemens S7-1518HF Training Suite** is a comprehensive documentation and integration project designed to demonstrate the architecture, configuration, and commissioning of a high-availability industrial control system. It leverages the S7-1518HF-4 PN controller to establish a redundant Profinet network handling Standard (S1), System Redundant (S2), and Redundant Interface (R1) devices.

### 2. Objectives
*   **Demonstrate Redundancy:** Validate bumpless transfer during CPU or network failures.
*   **Standardize Configuration:** Provide clear guidelines for hardware tagging, MRP domains, and watchdog tuning.
*   **Integrate Safety:** Implement failsafe logic and cross-system safety communication (F-Link).
*   **Implement High Availability IO:** Utilize library blocks for component-level redundancy.

### 3. Functional Requirements

#### 3.1 Network Architecture
*   **Split Backbone:** The network must consist of two independent rings (Side A / Side B) managed by separate MRP Domains (Domain 1 & 2).
*   **Y-Switch Integration:** An XF204-DNA must bridge the S2 subordinate ring (Domain 3) to the R1 backbone.
*   **Device Roles:**
    *   **R1 Devices:** ET200SP-A and ET200SP-B connect via redundant interfaces.
    *   **S2 Devices:** Connected via the Y-Switch.
    *   **PN/PN Coupler:** Facilitates data exchange with subordinate systems (PLC-C).

#### 3.2 Hardware Configuration & Tagging
*   **IO Stations:** Two remote stations (ET200SP-A/B) must be configured with specific slot layouts (DI, DQ, F-DI, F-DQ).
*   **Tagging Convention:** Tags must use descriptive names (Function_Side_Channel) and be managed via **Project Library** master copies to prevent manual errors.
*   **Layouts:**
    *   *ET200SP-A:* Slots 4-5 (DI), 6-7 (DQ), 8-9 (F-DI), 10-11 (F-DQ).
    *   *ET200SP-B:* Slots 3-4 (DI), 5-6 (DQ), 7-8 (F-DI), 9-10 (F-DQ).

#### 3.3 Safety Systems
*   **Failsafe Logic:** E-Stop logic must be implemented using `ESTOP1` blocks in `Main_Safety_RTG` (OB123).
*   **Cross-System Safety:**
    *   Safety data must be exchanged with a subordinate **PLC-C** via a PN/PN Coupler.
    *   Transfer Areas must be mirrored (In/Out) with matching F-Destination Addresses.
    *   Communication uses `SENDDP` and `RCVDP` blocks.

#### 3.4 High Availability IO (LRedIO)
*   **Input Redundancy:** Digital Inputs must utilize `LRedIO_DI` blocks for 1oo2 voting between Side A and Side B.
*   **Output Redundancy:** Digital Outputs must utilize `LRedIO_DQ` blocks to drive both sides simultaneously.
*   **Analog Support:** No Analog points are included in this demo scope.

### 4. Non-Functional Requirements
*   **Bumpless Transfer:** System switchover time must be covered by Watchdog Timers.
*   **Timing constraints:**
    *   Profinet Watchdog: > 300ms.
    *   F-Monitoring Time: > 350ms.
*   **Documentation:** All procedures must be modularized into 6 distinct training modules.

### 5. Documentation Modules
1.  **Module 1:** R1 Backbone Architecture & Station Integration.
2.  **Module 2:** S2 Subordinate System & Y-Switch.
3.  **Module 3:** Hardware Tagging & IO Configuration (Library Workflow).
4.  **Module 4:** Failsafe Programming & Cross-System Safety (PLC-C Commissioning).
5.  **Module 5:** High Availability IO with LRedIO.
6.  **Module 6:** Commissioning & Validation Plan.
