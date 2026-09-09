# Lab 1: Commissioning the R1 Network

**Objective:** Configure the primary R1 backbone, consisting of the S7-1518HF redundant pair and the redundant remote I/O (ET200SP R1).

*Note: All topologies have been pre-configured in this starter project. All configuration steps should be performed within the `Network view`.*

### Step-by-Step Configuration:

1. **Initial Device Assignment:**
   * Open the provided starter project in TIA Portal. The generic hardware profiles for the primary/backup S7-1518HF CPUs and the ET200SP-A R1 node have been pre-positioned in the `Network view`.
   * Navigate to `Online access` in the project tree. Select your active network adapter and click `Update accessible devices`.
   * Locate the physical CPUs and ET200SP-A in the list. Use `Online & diagnostics` -> `Functions` -> `Assign PROFINET device name` and `Assign IP address` to set the name and IPs according to the documented architecture (e.g., PLC-A: 192.168.0.1, PLC-B: 192.168.0.2).

2. **Initial Subnet Strategy:**
   * Initially, we will build this network using two entirely separate PROFINET IO subnets (e.g., `PN/IE_1` for Side A and `PN/IE_2` for Side B). Create these subnets in the `Network view`.
   * Assign the primary CPU interface to `PN/IE_1`.
   * Assign the backup CPU interface to `PN/IE_2`.

3. **Establish MRP Domains (Before Switch Configuration):**
   * Before touching the network switches, we must establish the MRP Domains on the Redundant System.
   * In `Network view`, select the `PLC_1` (Redundant system). Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`.
   * Assign PLC-A to manage `mrpdomain-1` (Side A).
   * Assign PLC-B to manage `mrpdomain-2` (Side B).

4. **S1 Switch Setup & MRP Client Configuration:**
   * Now locate the backbone network switches (`Switch-A` and `Switch-B`).
   * Configure them for **S1 PROFINET usage**. Assign `Switch-A` to `PN/IE_1` and `Switch-B` to `PN/IE_2`.
   * Navigate to their Media Redundancy settings. Ensure `Switch-A` is configured as an MRP Client in `mrpdomain-1` and `Switch-B` is an MRP Client in `mrpdomain-2`.

5. **Multi-Assignment of R1 Devices:**
   * Locate `ET200SP-A` in the `Network view`.
   * Click the "Not assigned" link on its PROFINET interface.
   * Select the redundant `PLC_1` system. You will see the network lines visually connect to both rings. This signifies the device is now Multi-assigned to both the primary and backup CPUs.

> **Pro-Tip: Avoiding Split Brains**
> When setting up the MRP rings, ensure that Domain 1 and Domain 2 are physically and logically completely isolated. Do not cross-connect the switches on Side A to Side B. The only device that should communicate across both is the R1 IO node or the Y-Switch. A cross-connection can cause an MRP storm or lead to a "split-brain" scenario where both CPUs attempt to assume the Primary role.