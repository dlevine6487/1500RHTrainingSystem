# Siemens S7-1518HF Redundant System: 1-Day Engineering Training Course

## Summary of the "1500RH Redundant Training Demo"
The "1500RH redundant training demo" is an advanced training suite for Siemens high-availability architectures. It demonstrates a robust "Split Backbone" topology utilizing two independent PROFINET rings (Side A/Domain 1 and Side B/Domain 2) managed by primary and backup S7-1518HF-4 PN controllers. The architecture seamlessly integrates standard (S1), system redundant (S2), and redundant interface (R1) devices. Critical components include the XF204-DNA Y-Switch bridging the highly available R1 backbone to an S2 subordinate "Blue Ring," and redundant ET200SP remote I/O stations. The demo emphasizes component-level I/O redundancy via the `LRedIO` library (1oo2 voting and dual-drive outputs), cross-system PROFIsafe integration via a PN/PN Coupler, and precisely tuned watchdog timers (>300ms) to ensure bumpless failover during primary-to-backup switchovers.

---

## Course Title & Abstract
**Course Title:** High-Availability Architecture & Commissioning with Siemens S7-1518HF Redundant Systems
**Abstract:** This intensive 1-day course empowers mid-level engineers to design, deploy, and maintain robust high-availability (HA) automation systems. Focused on the Siemens S7-1518HF redundant architecture, participants will explore the mechanics of "bumpless" switchovers, split-backbone PROFINET topologies, system-level safety integration, and advanced redundancy troubleshooting. Through hands-on labs using the 1500RH redundant training demo, engineers will configure MRP rings, deploy the LRedIO library for I/O redundancy, and validate system resilience against simulated hardware and network failures.

---

## Prerequisites (Software/Hardware Needed)
**Software:**
* TIA Portal V17 (or newer) with Step 7 Professional and Safety Advanced.
* PN Watchdog Add-In installed in the TIA Portal `AddIns` directory.
* Git client (for cloning the repository).
* S7-PLCSIM Advanced (for virtualized hardware simulation, if physical hardware is unavailable).

**Hardware (for physical labs):**
* 1x S7-1518HF-4 PN Controller Pair (Primary & Backup).
* 2x Scalance XC208 Switches (Switch-A, Switch-B).
* 1x Scalance XF204-DNA (Y-Switch).
* 2x ET200SP Remote I/O Stations equipped with IM 155-6 PN R1 interface modules, DI, DQ, F-DI, and F-DQ cards.
* 1x PN/PN Coupler.
* Connecting PROFINET Ethernet cables.

---

## Hourly Agenda (09:00 - 15:00)

**Morning Session: Architecture & Fundamentals**
* **09:00 - 10:00 | Introduction & Architecture Deep Dive:** What is the 1500RH system? The business case for HA. "Split Backbone" topology, R1 vs. S2, and MRP Domains (Who's the master, domains, linking S2 across panels).
* **10:00 - 10:45 | The 1500RH Standard & Commissioning:** Operational modes (RUN-Solo, RUN-Redundant, SYNCUP), single mode, and crucial HA configurations (Profinet Watchdog Timers).
* **10:45 - 11:00 | Coffee Break**
* **11:00 - 12:00 | Lab 1: Configuring the Split Backbone & Pinch Points:** Multi-assigning devices, Y-Switch setup, and a forced Watchdog failure scenario to demonstrate TIA Portal fault diagnostics.

**12:00 - 12:45 | Lunch Break**

**Afternoon Session: Hands-On Labs & Safety Integration**
* **12:45 - 13:30 | Lab 2: Component-Level Redundancy with LRedIO:** Deploying LRedIO for 1oo2 voting and Dual-Drive. Exploring discrepancy time "Pinch Points" and an instructor demo on V21 Openness.
* **13:30 - 14:00 | Cross-System Safety (F-Link):** Safety communication (PN/PN Coupler), "When to PN Couple or not to PN Couple?", mirrored transfer areas, and SENDDP/RCVDP.
* **14:00 - 15:00 | Lab 3: System Validation & Failover Simulation:** Executing CPU/Network failover scenarios, exploring H-CiR limitations (Pinch Point), evaluating diagnostic buffers (OB70/OB72), and final Q&A.

---

## Lab Instructions

### Lab 1: Configuring the Split Backbone & Y-Switch Integration
**Objective:** Construct the R1 network and integrate the S2 subordinate ring, while discussing Y-Switch configuration do's and do not's.
1. **Clone & Explore:** Clone the repository and open the TIA Portal project. Navigate to `Network View`.
2. **Configure MRP Clients:** Configure `Switch-A` as an MRP Client in `mrpdomain-1` and `Switch-B` as an MRP Client in `mrpdomain-2`. Ensure backbone ports are assigned to the rings.
3. **Multi-Assignment:** Click the "Not Assigned" link on the `ET200SP-A` PROFINET interface and assign it to the redundant `PLC_1` system, making it "Multi-assigned." Repeat for the `X1` interface of `PNPNCoupler-B`.
4. **Watchdog Tuning:** Using either the manual method (IO Cycle settings) or the PN Watchdog Add-in, adjust the Update Time and Accepted Update Cycles for `ET200SP-A` and `PNPNCoupler-B` so the Watchdog Time is > 300ms (e.g., 400ms).

### Lab 2: Component-Level Redundancy with LRedIO
**Objective:** Implement software redundancy for critical I/O across physical R1 stations.
1. **Library Import:** Open the Project Library, navigate to Master Copies, and drag the `LRedIO` blocks into your program blocks folder.
2. **1oo2 Voting (Inputs):** In OB1 (or your main cyclic block), call `LRedIO_DI_1oo2`. Map `Input_A` to `%I120.0` ("BlueResetButton_SideA_1") and `Input_B` to `%I110.0` ("BlueResetButton_SideA_2"). Set Discrepancy Time to 500ms.
3. **Dual Drive (Outputs):** Call `LRedIO_DQ`. Map your control logic command to `In`. Map `Output_A` to `%Q100.0` ("BlueResetLamp_SideA_1") and `Output_B` to `%Q110.0` ("BlueResetLamp_SideA_2").
4. **Compile & Download:** Compile the logic and perform an online download (changes only).
5. **Instructor Demo (Extra):** Exploring LRedIO automation using TIA Portal V21 Openness.

### Lab 3: System Validation & Failover Simulation
**Objective:** Prove system resilience against hardware and network faults.
1. **Achieve RUN-Redundant:** Ensure both the Primary and Backup 1518HF PLCs indicate solid green RUN LEDs.
2. **CPU Failover Test:** Physically disconnect the power or flip the STOP switch on the Primary PLC.
3. **Validation:** Observe the "BlueResetLamp". It must remain lit (bumpless transfer) as the Backup PLC assumes the Primary role.
4. **Network Failover Test:** Re-establish the RUN-Redundant state. Disconnect the PROFINET cable between `Switch-A` and the `ET200SP-A` R1 Interface.
5. **Diagnostics:** Check the TIA Portal diagnostics buffer. The system should report a network disruption (calling OB70 for peripheral redundancy loss or OB72 for H-Sync loss), but process logic must continue uninterrupted utilizing Side B.

---

## Key Takeaways / Assessment Questions

**Key Takeaways:**
* A successful high-availability system relies on both physical topology design (isolated rings) and strict parameterization (watchdog timers).
* S2 multi-assignment allows a single physical network connection to logically communicate with both redundant CPU modules.
* LRedIO bridges the gap between network redundancy and sensor/actuator level failures.
* Safety across networks requires mirrored configurations and matched F-Destination addresses.

**Assessment Questions:**
1. What is the fundamental difference between an R1 device connection and an S2 device connection?
2. If the standard switchover latency for a 1518HF system is ~300ms, what happens if an ET200SP IO Station's Watchdog timer is left at the default 6ms? How do you resolve this?
3. In the demo architecture, why is the XF204-DNA Y-Switch necessary for connecting the Blue Ring to the main redundant system?
4. When configuring the PN/PN Coupler for PROFIsafe communication, what critical parameter must be identical on both the X1 (Sender) and X2 (Receiver) transfer areas to prevent safety faults?
5. Explain the concept of 1oo2 voting as implemented by the `LRedIO_DI` block. What failure scenarios does this protect against?