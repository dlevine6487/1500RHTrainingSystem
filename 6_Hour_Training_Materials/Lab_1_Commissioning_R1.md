# Lab 1: Commissioning the R1 Network

**Objective:** Configure the primary R1 backbone, consisting of the S7-1518HF redundant pair and the redundant remote I/O (ET200SP R1).

*Note: All topologies have been pre-configured in this starter project. All configuration steps should be performed within the `Network view`.*

### Step-by-Step Configuration:

1. **Initial Device Assignment:**
   * Open the provided starter project in TIA Portal. The generic hardware profiles for the primary/backup S7-1518HF CPUs and the ET200SP-A R1 node have been pre-positioned in the `Network view`.
   * Navigate to `Online access` in the project tree. Select your active network adapter and click `Update accessible devices`.
   * Locate the physical CPUs and ET200SP-A in the list. Use `Online & diagnostics` -> `Functions` -> `Assign PROFINET device name` and `Assign IP address` to set the name and IPs according to the documented architecture (e.g., PLC-A: 192.168.0.1, PLC-B: 192.168.0.2).

2. **Initial Subnet Strategy:**
   * In the starter project, the default subnets are labeled `PN/IE_PlantA` and `PN/IE_PlantB`. For this learning exercise, either rename them or create new ones labeled `PN/IE_1` and `PN/IE_2`.
   * Assign the primary CPU interface to `PN/IE_1`.
   * Assign the backup CPU interface to `PN/IE_2`.

3. **Establish MRP Domains & Configure the R1 Station:**
   * First, establish the MRP Domains on the Redundant System. Select `PLC_1`, go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`. Assign PLC-A to manage `mrpdomain-1` and PLC-B to manage `mrpdomain-2`.
   * Locate `ET200SP-A` (the R1 device). Click "Not assigned" and select `PLC_1` to multi-assign it.
   * **Crucial Step:** You must explicitly configure each IM individually. Select IM 1 (Side A), navigate to PROFINET properties, and assign it to `mrpdomain-1`. Assign IM 2 (Side B) to `mrpdomain-2`.
   * *Why?* Even if you are not utilizing full physical MRP rings, this logical domain separation is absolutely required in the PLC to correctly split the R1 system paths.

4. **The R1 Compilation Check:**
   * Before touching the network switches, click on the `PLC_1` redundant system and click **Compile (Hardware)**.
   * **Observation:** The project should compile perfectly with zero errors. This proves that a true R1 device (like the ET200SP R1) can successfully span two completely separate, disjoint subnets (`PN/IE_1` and `PN/IE_2`).

5. **Pivot Point 1: Expanding the Portfolio (ET200SP HA):**
   * Pause lab work.
   * **Guest Presentation (15 Min):** A presenter will discuss the ET200SP HA. While not utilized in this specific lab environment, it serves as a point-in-case that there are other robust, purpose-built R1 devices available in the Siemens portfolio tailored for high-availability setups.

6. **The Switch Compilation Pinch Point (The Flat Network Requirement):**
   * Now, locate the backbone network switches (`Switch-A` and `Switch-B`).
   * As a first-time user, it makes intuitive sense to assign `Switch-A` to `PN/IE_1` and `Switch-B` to `PN/IE_2`, and then set up their respective MRP Client configurations to match the domains. Go ahead and try to set them up this way, attempting to multi-assign them to the redundant system if possible.
   * **Compile the Hardware.**
   * **The Lesson:** It fails! A first-time user expects this to work based on the previous R1 success, but the PLC will not compile. Why? To function correctly in a redundant scenario (and to be properly multi-assigned as S2 devices), standard network switches like the XC208 require a single, unified PROFINET IO network. They cannot span disjoint networks like a true R1 IO station can.
   * **Action (Refactor the Network):**
     1. Delete the disjoint subnets (`PN/IE_1` and `PN/IE_2`).
     2. Create a new, single PROFINET subnet and name it `PN/IE_Plant`.
     3. Assign both the Primary and Backup CPU PROFINET interfaces to this unified `PN/IE_Plant` subnet.
     4. Re-assign `Switch-A` and `Switch-B`, and the ET200SP-A interfaces to `PN/IE_Plant`.
     5. Notice that you can now successfully multi-assign the switches as S2 devices, granting them true PROFINET redundancy. Re-verify your MRP domain assignments (`mrpdomain-1` and `mrpdomain-2`). Compile again to ensure success.

> **Pro-Tip: Avoiding Split Brains**
> When setting up the MRP rings, ensure that Domain 1 and Domain 2 are physically and logically completely isolated. Do not cross-connect the switches on Side A to Side B. The only device that should communicate across both is the R1 IO node or the Y-Switch. A cross-connection can cause an MRP storm or lead to a "split-brain" scenario where both CPUs attempt to assume the Primary role.