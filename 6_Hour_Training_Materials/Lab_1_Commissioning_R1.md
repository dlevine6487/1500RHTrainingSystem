# Lab 1: Commissioning the R1 Network

**Objective:** Configure the primary R1 backbone, consisting of the S7-1518HF redundant pair and the redundant remote I/O (ET200SP R1).

*Note: All topologies have been pre-configured in this starter project. All configuration steps should be performed within the `Network view`.*

### Step-by-Step Configuration:

1. **Initial Device Assignment:**
   * Open the provided starter project in TIA Portal. The generic hardware profiles for the primary/backup S7-1518HF CPUs and the ET200SP-A R1 node have been pre-positioned in the `Network view`.
   * Navigate to `Online access` in the project tree. Select your active network adapter and click `Update accessible devices`.
   * Locate the physical CPUs and ET200SP-A in the list. Use `Online & diagnostics` -> `Functions` -> `Assign PROFINET device name` and `Assign IP address` to set the name and IPs according to the documented architecture (e.g., PLC-A: 192.168.0.1, PLC-B: 192.168.0.2).

2. **Initial Subnet Strategy:**
   * In the starter project they are labeled `PN/IE_PlantA` and `PN/IE_PlantB`. You need to rename them or create new ones.
   * Assign the primary CPU interface to `PN/IE_1`.
   * Assign the backup CPU interface to `PN/IE_2`.

3. **Establish MRP Domains (Before Switch Configuration):**
   * Before touching the network switches, we must establish the MRP Domains on the Redundant System.
   * In `Network view`, select the `PLC_1` (Redundant system). Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`.
   * Assign PLC-A to manage `mrpdomain-1` (Side A).
   * Assign PLC-B to manage `mrpdomain-2` (Side B).

4. **Switch Setup: S1 vs. S2 Configuration:**
   * Locate the backbone network switches (`Switch-A` and `Switch-B`).
   * Currently, because our PLCs are on separate subnets (`PN/IE_1` and `PN/IE_2`), you can only assign a switch to *one* PLC subnet.
   * Try clicking "Not Assigned" on `Switch-A`. You can only select PLC_1 via `PN/IE_1`. This forces the switch into an **S1 PROFINET configuration**.
   * **The Reality Check:** In this S1 configuration, the switch is *not considered redundant at all*. It is simply acting as a standard IO Device "along for the ride" on that specific subnet. If the PLC attached to that subnet fails, the switch loses its PROFINET controller connection. While it functions as a Layer 2 access point, it does not provide true PROFINET system redundancy (reaction and diagnostics) for the switch itself.
   * *To gain S2 functionality on a standard switch (where it logically talks to both CPUs), the network topology rules must change, which we will discover in Lab 2.*
   * For now, proceed with the S1 assignment: Assign `Switch-A` to `PN/IE_1` and `Switch-B` to `PN/IE_2`. Navigate to their Media Redundancy settings and ensure `Switch-A` is an MRP Client in `mrpdomain-1` and `Switch-B` is an MRP Client in `mrpdomain-2`.

5. **Multi-Assignment & IM Configuration of R1 Devices:**
   * Locate `ET200SP-A` in the `Network view`. This R1 node contains two separate Interface Modules (IMs).
   * First, click the "Not assigned" link on the PROFINET interface for the station. Select the redundant `PLC_1` system to multi-assign it. You will see the network lines visually connect to both rings.
   * **Crucial Step:** You must explicitly configure each IM individually within the station properties. Select IM 1 (Side A) and navigate to its PROFINET interface properties. Assign it explicitly to `mrpdomain-1`.
   * Then, select IM 2 (Side B) and assign it explicitly to `mrpdomain-2`.
   * *Why?* Even if you are not utilizing full MRP (Media Redundancy Protocol) for physical ring topologies in a specific installation, this logical domain separation is absolutely required in the PLC controller to correctly split the R1 system and manage communication paths during a failover.

> **Pro-Tip: Avoiding Split Brains**
> When setting up the MRP rings, ensure that Domain 1 and Domain 2 are physically and logically completely isolated. Do not cross-connect the switches on Side A to Side B. The only device that should communicate across both is the R1 IO node or the Y-Switch. A cross-connection can cause an MRP storm or lead to a "split-brain" scenario where both CPUs attempt to assume the Primary role.