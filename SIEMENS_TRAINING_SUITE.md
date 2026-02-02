# Siemens S7-1518HF Redundant System: Training & Integration Suite

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
    *   Connect Port 1 to `switch-backbone-a` -> **Client** of `mrpdomain-1`.
    *   Connect Port 2 to `switch-backbone-b` -> **Client** of `mrpdomain-2`.
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

## Module 3: Commissioning & Validation Plan

### 3.1 Reference Documents
*   `Application_Examples_and_Docs/Diagnostics_and_Communication/109763768_Diag_S7_1500RH_DOC_V2_0_en.pdf`

### 3.2 Validation Strategy
Validate the independence of the three domains and the bumpless behavior.

### 3.3 Test A: Domain 1 Failure (Backbone A)
*   **Action:** Break `mrpdomain-1` (Cable pull).
*   **Expectation:** R1 Station continues operation via Interface 2 (Side B).

### 3.4 Test B: Domain 2 Failure (Backbone B)
*   **Action:** Power off `switch-backbone-b`.
*   **Expectation:** R1 Station switches to Interface 1 (Side A). DNA switches upstream to Port 1.

### 3.5 Test C: Primary CPU Failure (Bumpless Check)
*   **Action:** Power cut Primary PLC.
*   **Expectation:**
    *   Backup PLC becomes Primary.
    *   **R1 Outputs:** DO NOT FLICKER. (Verifies Watchdog > 300ms).
    *   **S2 Outputs:** DO NOT FLICKER.

### 3.6 Test D: Domain 3 (Blue Ring)
*   **Action:** Break Blue Ring cable.
*   **Expectation:** DNA Manager heals ring; S2 devices remain connected.

### 3.7 Final Handover Checklist
*   [ ] **Backbone:** XC208s configured as Clients in D1/D2.
*   [ ] **R1 & S2 Watchdogs:** Calculated and set > 300ms (e.g., 2ms * 150 cycles).
*   [ ] **Topology:** Verified Split Backbone with R1 bridging.
*   [ ] **Validation:** All failover tests (A-D) passed without process interruption.

## Module 5: Hardware Tagging & IO Configuration

This module outlines the IO tag placeholders for the two Remote IO stations, ET200SP-A and ET200SP-B.

### 5.1 ET200SP-A Tag List
**Rack 0 Configuration:**
*   Slot 4: DI 8x24VDC HF
*   Slot 5: DI 8x24VDC HF
*   Slot 6: DQ 8x24VDC/0.5A
*   Slot 7: DQ 8x24VDC/0.5A
*   Slot 8: F-DI 8x24VDC HF
*   Slot 9: F-DI 8x24VDC HF
*   Slot 10: F-DQ 4x24VDC/2A
*   Slot 11: F-DQ 4x24VDC/2A

#### Slot 4: DI 8x24VDC HF (Digital Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I0.0 | ET200SP_A_DI_Slot4_Ch0 | Bool | Placeholder |
| 1 | %I0.1 | ET200SP_A_DI_Slot4_Ch1 | Bool | Placeholder |
| 2 | %I0.2 | ET200SP_A_DI_Slot4_Ch2 | Bool | Placeholder |
| 3 | %I0.3 | ET200SP_A_DI_Slot4_Ch3 | Bool | Placeholder |
| 4 | %I0.4 | ET200SP_A_DI_Slot4_Ch4 | Bool | Placeholder |
| 5 | %I0.5 | ET200SP_A_DI_Slot4_Ch5 | Bool | Placeholder |
| 6 | %I0.6 | ET200SP_A_DI_Slot4_Ch6 | Bool | Placeholder |
| 7 | %I0.7 | ET200SP_A_DI_Slot4_Ch7 | Bool | Placeholder |

#### Slot 5: DI 8x24VDC HF (Digital Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I1.0 | ET200SP_A_DI_Slot5_Ch0 | Bool | Placeholder |
| 1 | %I1.1 | ET200SP_A_DI_Slot5_Ch1 | Bool | Placeholder |
| 2 | %I1.2 | ET200SP_A_DI_Slot5_Ch2 | Bool | Placeholder |
| 3 | %I1.3 | ET200SP_A_DI_Slot5_Ch3 | Bool | Placeholder |
| 4 | %I1.4 | ET200SP_A_DI_Slot5_Ch4 | Bool | Placeholder |
| 5 | %I1.5 | ET200SP_A_DI_Slot5_Ch5 | Bool | Placeholder |
| 6 | %I1.6 | ET200SP_A_DI_Slot5_Ch6 | Bool | Placeholder |
| 7 | %I1.7 | ET200SP_A_DI_Slot5_Ch7 | Bool | Placeholder |

#### Slot 6: DQ 8x24VDC/0.5A (Digital Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q0.0 | ET200SP_A_DQ_Slot6_Ch0 | Bool | Placeholder |
| 1 | %Q0.1 | ET200SP_A_DQ_Slot6_Ch1 | Bool | Placeholder |
| 2 | %Q0.2 | ET200SP_A_DQ_Slot6_Ch2 | Bool | Placeholder |
| 3 | %Q0.3 | ET200SP_A_DQ_Slot6_Ch3 | Bool | Placeholder |
| 4 | %Q0.4 | ET200SP_A_DQ_Slot6_Ch4 | Bool | Placeholder |
| 5 | %Q0.5 | ET200SP_A_DQ_Slot6_Ch5 | Bool | Placeholder |
| 6 | %Q0.6 | ET200SP_A_DQ_Slot6_Ch6 | Bool | Placeholder |
| 7 | %Q0.7 | ET200SP_A_DQ_Slot6_Ch7 | Bool | Placeholder |

#### Slot 7: DQ 8x24VDC/0.5A (Digital Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q1.0 | ET200SP_A_DQ_Slot7_Ch0 | Bool | Placeholder |
| 1 | %Q1.1 | ET200SP_A_DQ_Slot7_Ch1 | Bool | Placeholder |
| 2 | %Q1.2 | ET200SP_A_DQ_Slot7_Ch2 | Bool | Placeholder |
| 3 | %Q1.3 | ET200SP_A_DQ_Slot7_Ch3 | Bool | Placeholder |
| 4 | %Q1.4 | ET200SP_A_DQ_Slot7_Ch4 | Bool | Placeholder |
| 5 | %Q1.5 | ET200SP_A_DQ_Slot7_Ch5 | Bool | Placeholder |
| 6 | %Q1.6 | ET200SP_A_DQ_Slot7_Ch6 | Bool | Placeholder |
| 7 | %Q1.7 | ET200SP_A_DQ_Slot7_Ch7 | Bool | Placeholder |

#### Slot 8: F-DI 8x24VDC HF (Failsafe Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I10.0 | ET200SP_A_FDI_Slot8_Ch0 | Bool | Placeholder |
| 1 | %I10.1 | ET200SP_A_FDI_Slot8_Ch1 | Bool | Placeholder |
| 2 | %I10.2 | ET200SP_A_FDI_Slot8_Ch2 | Bool | Placeholder |
| 3 | %I10.3 | ET200SP_A_FDI_Slot8_Ch3 | Bool | Placeholder |
| 4 | %I10.4 | ET200SP_A_FDI_Slot8_Ch4 | Bool | Placeholder |
| 5 | %I10.5 | ET200SP_A_FDI_Slot8_Ch5 | Bool | Placeholder |
| 6 | %I10.6 | ET200SP_A_FDI_Slot8_Ch6 | Bool | Placeholder |
| 7 | %I10.7 | ET200SP_A_FDI_Slot8_Ch7 | Bool | Placeholder |

#### Slot 9: F-DI 8x24VDC HF (Failsafe Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I11.0 | ET200SP_A_FDI_Slot9_Ch0 | Bool | Placeholder |
| 1 | %I11.1 | ET200SP_A_FDI_Slot9_Ch1 | Bool | Placeholder |
| 2 | %I11.2 | ET200SP_A_FDI_Slot9_Ch2 | Bool | Placeholder |
| 3 | %I11.3 | ET200SP_A_FDI_Slot9_Ch3 | Bool | Placeholder |
| 4 | %I11.4 | ET200SP_A_FDI_Slot9_Ch4 | Bool | Placeholder |
| 5 | %I11.5 | ET200SP_A_FDI_Slot9_Ch5 | Bool | Placeholder |
| 6 | %I11.6 | ET200SP_A_FDI_Slot9_Ch6 | Bool | Placeholder |
| 7 | %I11.7 | ET200SP_A_FDI_Slot9_Ch7 | Bool | Placeholder |

#### Slot 10: F-DQ 4x24VDC/2A (Failsafe Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q10.0 | ET200SP_A_FDQ_Slot10_Ch0 | Bool | Placeholder |
| 1 | %Q10.1 | ET200SP_A_FDQ_Slot10_Ch1 | Bool | Placeholder |
| 2 | %Q10.2 | ET200SP_A_FDQ_Slot10_Ch2 | Bool | Placeholder |
| 3 | %Q10.3 | ET200SP_A_FDQ_Slot10_Ch3 | Bool | Placeholder |

#### Slot 11: F-DQ 4x24VDC/2A (Failsafe Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q11.0 | ET200SP_A_FDQ_Slot11_Ch0 | Bool | Placeholder |
| 1 | %Q11.1 | ET200SP_A_FDQ_Slot11_Ch1 | Bool | Placeholder |
| 2 | %Q11.2 | ET200SP_A_FDQ_Slot11_Ch2 | Bool | Placeholder |
| 3 | %Q11.3 | ET200SP_A_FDQ_Slot11_Ch3 | Bool | Placeholder |

### 5.2 ET200SP-B Tag List
**Rack 0 Configuration:** Identical to ET200SP-A.

#### Slot 4: DI 8x24VDC HF (Digital Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I20.0 | ET200SP_B_DI_Slot4_Ch0 | Bool | Placeholder |
| 1 | %I20.1 | ET200SP_B_DI_Slot4_Ch1 | Bool | Placeholder |
| 2 | %I20.2 | ET200SP_B_DI_Slot4_Ch2 | Bool | Placeholder |
| 3 | %I20.3 | ET200SP_B_DI_Slot4_Ch3 | Bool | Placeholder |
| 4 | %I20.4 | ET200SP_B_DI_Slot4_Ch4 | Bool | Placeholder |
| 5 | %I20.5 | ET200SP_B_DI_Slot4_Ch5 | Bool | Placeholder |
| 6 | %I20.6 | ET200SP_B_DI_Slot4_Ch6 | Bool | Placeholder |
| 7 | %I20.7 | ET200SP_B_DI_Slot4_Ch7 | Bool | Placeholder |

#### Slot 5: DI 8x24VDC HF (Digital Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I21.0 | ET200SP_B_DI_Slot5_Ch0 | Bool | Placeholder |
| 1 | %I21.1 | ET200SP_B_DI_Slot5_Ch1 | Bool | Placeholder |
| 2 | %I21.2 | ET200SP_B_DI_Slot5_Ch2 | Bool | Placeholder |
| 3 | %I21.3 | ET200SP_B_DI_Slot5_Ch3 | Bool | Placeholder |
| 4 | %I21.4 | ET200SP_B_DI_Slot5_Ch4 | Bool | Placeholder |
| 5 | %I21.5 | ET200SP_B_DI_Slot5_Ch5 | Bool | Placeholder |
| 6 | %I21.6 | ET200SP_B_DI_Slot5_Ch6 | Bool | Placeholder |
| 7 | %I21.7 | ET200SP_B_DI_Slot5_Ch7 | Bool | Placeholder |

#### Slot 6: DQ 8x24VDC/0.5A (Digital Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q20.0 | ET200SP_B_DQ_Slot6_Ch0 | Bool | Placeholder |
| 1 | %Q20.1 | ET200SP_B_DQ_Slot6_Ch1 | Bool | Placeholder |
| 2 | %Q20.2 | ET200SP_B_DQ_Slot6_Ch2 | Bool | Placeholder |
| 3 | %Q20.3 | ET200SP_B_DQ_Slot6_Ch3 | Bool | Placeholder |
| 4 | %Q20.4 | ET200SP_B_DQ_Slot6_Ch4 | Bool | Placeholder |
| 5 | %Q20.5 | ET200SP_B_DQ_Slot6_Ch5 | Bool | Placeholder |
| 6 | %Q20.6 | ET200SP_B_DQ_Slot6_Ch6 | Bool | Placeholder |
| 7 | %Q20.7 | ET200SP_B_DQ_Slot6_Ch7 | Bool | Placeholder |

#### Slot 7: DQ 8x24VDC/0.5A (Digital Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q21.0 | ET200SP_B_DQ_Slot7_Ch0 | Bool | Placeholder |
| 1 | %Q21.1 | ET200SP_B_DQ_Slot7_Ch1 | Bool | Placeholder |
| 2 | %Q21.2 | ET200SP_B_DQ_Slot7_Ch2 | Bool | Placeholder |
| 3 | %Q21.3 | ET200SP_B_DQ_Slot7_Ch3 | Bool | Placeholder |
| 4 | %Q21.4 | ET200SP_B_DQ_Slot7_Ch4 | Bool | Placeholder |
| 5 | %Q21.5 | ET200SP_B_DQ_Slot7_Ch5 | Bool | Placeholder |
| 6 | %Q21.6 | ET200SP_B_DQ_Slot7_Ch6 | Bool | Placeholder |
| 7 | %Q21.7 | ET200SP_B_DQ_Slot7_Ch7 | Bool | Placeholder |

#### Slot 8: F-DI 8x24VDC HF (Failsafe Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I30.0 | ET200SP_B_FDI_Slot8_Ch0 | Bool | Placeholder |
| 1 | %I30.1 | ET200SP_B_FDI_Slot8_Ch1 | Bool | Placeholder |
| 2 | %I30.2 | ET200SP_B_FDI_Slot8_Ch2 | Bool | Placeholder |
| 3 | %I30.3 | ET200SP_B_FDI_Slot8_Ch3 | Bool | Placeholder |
| 4 | %I30.4 | ET200SP_B_FDI_Slot8_Ch4 | Bool | Placeholder |
| 5 | %I30.5 | ET200SP_B_FDI_Slot8_Ch5 | Bool | Placeholder |
| 6 | %I30.6 | ET200SP_B_FDI_Slot8_Ch6 | Bool | Placeholder |
| 7 | %I30.7 | ET200SP_B_FDI_Slot8_Ch7 | Bool | Placeholder |

#### Slot 9: F-DI 8x24VDC HF (Failsafe Inputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %I31.0 | ET200SP_B_FDI_Slot9_Ch0 | Bool | Placeholder |
| 1 | %I31.1 | ET200SP_B_FDI_Slot9_Ch1 | Bool | Placeholder |
| 2 | %I31.2 | ET200SP_B_FDI_Slot9_Ch2 | Bool | Placeholder |
| 3 | %I31.3 | ET200SP_B_FDI_Slot9_Ch3 | Bool | Placeholder |
| 4 | %I31.4 | ET200SP_B_FDI_Slot9_Ch4 | Bool | Placeholder |
| 5 | %I31.5 | ET200SP_B_FDI_Slot9_Ch5 | Bool | Placeholder |
| 6 | %I31.6 | ET200SP_B_FDI_Slot9_Ch6 | Bool | Placeholder |
| 7 | %I31.7 | ET200SP_B_FDI_Slot9_Ch7 | Bool | Placeholder |

#### Slot 10: F-DQ 4x24VDC/2A (Failsafe Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q30.0 | ET200SP_B_FDQ_Slot10_Ch0 | Bool | Placeholder |
| 1 | %Q30.1 | ET200SP_B_FDQ_Slot10_Ch1 | Bool | Placeholder |
| 2 | %Q30.2 | ET200SP_B_FDQ_Slot10_Ch2 | Bool | Placeholder |
| 3 | %Q30.3 | ET200SP_B_FDQ_Slot10_Ch3 | Bool | Placeholder |

#### Slot 11: F-DQ 4x24VDC/2A (Failsafe Outputs)
| Channel | Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- | :--- |
| 0 | %Q31.0 | ET200SP_B_FDQ_Slot11_Ch0 | Bool | Placeholder |
| 1 | %Q31.1 | ET200SP_B_FDQ_Slot11_Ch1 | Bool | Placeholder |
| 2 | %Q31.2 | ET200SP_B_FDQ_Slot11_Ch2 | Bool | Placeholder |
| 3 | %Q31.3 | ET200SP_B_FDQ_Slot11_Ch3 | Bool | Placeholder |
