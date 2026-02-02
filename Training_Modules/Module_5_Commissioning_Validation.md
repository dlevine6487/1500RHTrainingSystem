## Module 5: Commissioning & Validation Plan

### 5.1 Reference Documents
*   `Application_Examples_and_Docs/Diagnostics_and_Communication/109763768_Diag_S7_1500RH_DOC_V2_0_en.pdf`

### 5.2 Validation Strategy
Validate the independence of the three domains and the bumpless behavior.

### 5.3 Test A: Domain 1 Failure (Backbone A)
*   **Action:** Break `mrpdomain-1` (Cable pull).
*   **Expectation:** R1 Station continues operation via Interface 2 (Side B).

### 5.4 Test B: Domain 2 Failure (Backbone B)
*   **Action:** Power off `switch-backbone-b`.
*   **Expectation:** R1 Station switches to Interface 1 (Side A). DNA switches upstream to Port 1.

### 5.5 Test C: Primary CPU Failure (Bumpless Check)
*   **Action:** Power cut Primary PLC.
*   **Expectation:**
    *   Backup PLC becomes Primary.
    *   **R1 Outputs:** DO NOT FLICKER. (Verifies Watchdog > 300ms).
    *   **S2 Outputs:** DO NOT FLICKER.

### 5.6 Test D: Domain 3 (Blue Ring)
*   **Action:** Break Blue Ring cable.
*   **Expectation:** DNA Manager heals ring; S2 devices remain connected.

### 5.7 Final Handover Checklist
*   [ ] **Backbone:** XC208s configured as Clients in D1/D2.
*   [ ] **R1 & S2 Watchdogs:** Calculated and set > 300ms (e.g., 2ms * 150 cycles).
*   [ ] **Topology:** Verified Split Backbone with R1 bridging.
*   [ ] **Validation:** All failover tests (A-D) passed without process interruption.
