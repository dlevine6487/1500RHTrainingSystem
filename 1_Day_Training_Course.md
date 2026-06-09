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

## Hourly Agenda (09:00 - 17:00)

**Morning Session: Architecture & Fundamentals**
* **09:00 - 09:30 | Introduction & System Overview:** What is the 1500RH system? The business case for High Availability and the concept of "bumpless" transfer.
* **09:30 - 10:30 | Architecture Deep Dive:** The "Split Backbone" topology. Detailed breakdown of R1 (Redundant Interface) vs. S2 (System Redundancy). Understanding MRP Domains (1, 2, and 3).
* **10:30 - 10:45 | Coffee Break**
* **10:45 - 11:45 | The 1500RH Standard/Unit:** Capabilities, constraints, and operational modes (STOP, RUN-Solo, RUN-Redundant, SYNCUP). System limits and maintenance considerations.
* **11:45 - 12:30 | Network Commissioning & Watchdog Tuning:** Crucial configurations for HA systems. Calculating and assigning Profinet Watchdog Timers (>300ms latency) using manual and Add-In methods.

**12:30 - 13:30 | Lunch Break**

**Afternoon Session: Hands-On Labs & Safety Integration**
* **13:30 - 14:30 | Lab 1: Configuring the Split Backbone & Y-Switch Integration:** Multi-assigning R1/S2 devices, configuring MRP clients, and integrating the XF204-DNA Y-Switch to bridge the subordinate S2 ring.
* **14:30 - 15:30 | Lab 2: Component-Level Redundancy with LRedIO:** Deploying the LRedIO library for 1oo2 voting on Digital Inputs and Dual-Drive on Digital Outputs across isolated ET200SP stations.
* **15:30 - 15:45 | Coffee Break**
* **15:45 - 16:30 | Cross-System Safety (F-Link):** Implementing safety communication between the 1518HF and a subordinate PLC (PLC-C) using the PN/PN Coupler, mirrored transfer areas, and SENDDP/RCVDP instructions.
* **16:30 - 17:00 | Lab 3: System Validation & Failover Simulation:** Executing failover scenarios, evaluating diagnostic buffers, and final assessment. Q&A.

---

## Lab Instructions

### Lab 1: Configuring the Split Backbone & Y-Switch Integration
**Objective:** Construct the R1 network and integrate the S2 subordinate ring.
1. **Clone & Explore:** Clone the repository and open the TIA Portal project. Navigate to `Network View`.
2. **Configure MRP Clients:** Configure `Switch-A` as an MRP Client in `mrpdomain-1` and `Switch-B` as an MRP Client in `mrpdomain-2`. Ensure backbone ports are assigned to the rings.
3. **Multi-Assignment:** Click the "Not Assigned" link on the `ET200SP-A` PROFINET interface and assign it to the redundant `PLC_1` system, making it "Multi-assigned." Repeat for the `X1` interface of `PNPNCoupler-B`.
4. **Watchdog Tuning:** Using either the manual method (IO Cycle settings) or the PN Watchdog Add-in, adjust the Update Time and Accepted Update Cycles for `ET200SP-A` and `PNPNCoupler-B` so the Watchdog Time is > 300ms (e.g., 400ms).

### Lab 2: Component-Level Redundancy with LRedIO
**Objective:** Implement software redundancy for critical I/O across physical R1 stations.
1. **Library Import:** Open the Project Library, navigate to Master Copies, and drag the `LRedIO` blocks into your program blocks folder.
2. **1oo2 Voting (Inputs):** In OB1 (or your main cyclic block), call `LRedIO_DI_1oo2`. Map `Input_A` to `%I120.0` ("BlueResetButton_SideA_1") and `Input_B` to `%I63.0` ("BlueResetButton_SideB_1"). Set Discrepancy Time to 500ms.
3. **Dual Drive (Outputs):** Call `LRedIO_DQ`. Map your control logic command to `In`. Map `Output_A` to `%Q100.0` ("BlueResetLamp_SideA_1") and `Output_B` to `%Q12.0` ("BlueResetLamp_SideB_1").
4. **Compile & Download:** Compile the logic and perform an online download (changes only).

### Lab 3: System Validation & Failover Simulation
**Objective:** Prove system resilience against hardware and network faults.
1. **Achieve RUN-Redundant:** Ensure both the Primary and Backup 1518HF PLCs indicate solid green RUN LEDs.
2. **CPU Failover Test:** Physically disconnect the power or flip the STOP switch on the Primary PLC.
3. **Validation:** Observe the "BlueResetLamp". It must remain lit (bumpless transfer) as the Backup PLC assumes the Primary role.
4. **Network Failover Test:** Re-establish the RUN-Redundant state. Disconnect the PROFINET cable between `Switch-A` and the `ET200SP-A` R1 Interface.
5. **Diagnostics:** Check the TIA Portal diagnostics buffer. The system should report a network disruption but process logic must continue uninterrupted utilizing Side B.

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