# Lab Instructions: 6-Hour S7-1500R/H Commissioning Course

Welcome to the hands-on labs for the S7-1500R/H System. These labs are designed to walk you through the practical commissioning of a highly available redundant system using a Split Backbone topology and an S2 subordinate network.

## Lab 1: Commissioning the R1 Network

**Objective:** Configure the primary R1 backbone, consisting of the S7-1518HF redundant pair and the redundant remote I/O (ET200SP R1).

### Step-by-Step Configuration:

1. **Initial Device Assignment:**
   * Open the provided starter project in TIA Portal. The generic hardware profiles for the primary/backup S7-1518HF CPUs and the ET200SP-A R1 node have been pre-positioned in the `Network view`.
   * Navigate to `Online access` in the project tree. Select your active network adapter and click `Update accessible devices`.
   * Locate the physical CPUs and ET200SP-A in the list. Use `Online & diagnostics` -> `Functions` -> `Assign PROFINET device name` and `Assign IP address` to set the name and IPs according to the documented architecture (e.g., PLC-A: 192.168.0.1, PLC-B: 192.168.0.2).

2. **Initial Subnet Strategy & S1 Switch Setup:**
   * Initially, we will build this network using two entirely separate PROFINET IO subnets (e.g., `PN/IE_1` for Side A and `PN/IE_2` for Side B). Create these subnets and assign the primary CPU to subnet 1, and the backup CPU to subnet 2.
   * Locate the backbone network switches (`Switch-A` and `Switch-B`). Configure them for **S1 PROFINET usage**. Assign them to their respective subnets.

3. **Configure MRP Rings:**
   * In `Network view`, select the `PLC_1` (Redundant system). Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`.
   * Assign PLC-A to manage `mrpdomain-1` (Side A) and PLC-B to manage `mrpdomain-2` (Side B).
   * **Note:** Ensure the backbone switches (`Switch-A` and `Switch-B`) are configured as MRP Clients in their respective domains.

4. **Multi-Assignment of R1 Devices:**
   * Locate `ET200SP-A` in the `Network view`.
   * Click the "Not assigned" link on its PROFINET interface.
   * Select the redundant `PLC_1` system. You will see the network lines visually connect to both rings. This signifies the device is now Multi-assigned to both the primary and backup CPUs.

> **Pro-Tip: Avoiding Split Brains**
> When setting up the MRP rings, ensure that Domain 1 and Domain 2 are physically and logically completely isolated. Do not cross-connect the switches on Side A to Side B. The only device that should communicate across both is the R1 IO node or the Y-Switch. A cross-connection can cause an MRP storm or lead to a "split-brain" scenario where both CPUs attempt to assume the Primary role.

---

## Lab 2: The Y-Switch & Subordinate S2 Networks

**Objective:** Configure the Scalance XF204-DNA (Y-Switch) to safely bridge the R1 highly available network to a standard S2 ring.

### Step-by-Step Configuration:

1. **The Subnet Refactoring Pinch Point:**
   * Try to assign the `YSwitch-A` to the redundant system. You will likely encounter a configuration limitation.
   * **The Lesson:** While a pure R1 design can utilize two separate subnets, integrating a Y-Switch in an S7-1500RH design *requires* configuring one PROFINET IO subnet with multiple domains.
   * **Action:** Refactor your network. Delete the second subnet (`PN/IE_2`) and assign both CPU interfaces and all backbone switches back to the single `PN/IE_1` subnet. Re-verify your MRP domain assignments (Domain 1 and 2) are still correct. This decision must be made early in real projects to avoid massive rework!

2. **Enable DNA Redundancy:**
   * Locate the `YSwitch-A` device in the `Network view` (it is pre-positioned).
   * Select the device and go to `Properties` -> `General` -> `Module parameters`.
   * Locate the setting for `DNA redundancy` and explicitly enable it. This tells the switch it is acting as a Y-Switch, not a standard Scalance switch.

2. **Configure Ring Redundancy (MRP Manager):**
   * Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`.
   * Set the Media Redundancy Role to **MRP Manager**.
   * Under the MRP Domain drop-down, select `mrpdomain-3`.
   * *If `mrpdomain-3` does not exist:* Click the 'Domain settings' button, add Domain 3 to the project's MRP configurations, and ensure it is assigned to this switch's subnet.

3. **Map Physical Ports:**
   * Carefully check the properties of the Ring ports. Ensure they are mapped to the exact physical ports connecting to the subordinate S2 ring (typically P1.1 and P2.1, but verify against your physical wiring).
   * The uplink ports to the R1 backbone must *not* be part of `mrpdomain-3`.

> **Pro-Tip: Topology View Verification**
> The number one cause of Y-Switch commissioning failure is a mismatch between the physical wiring and the logical configuration in TIA Portal. Always use the `Topology view` in TIA Portal to explicitly draw the connections for the Y-Switch uplinks and the subordinate ring ports. If the graphical topology does not compile or match reality, the switch will likely fail to transition out of a fault state.

---

## Lab 3: Populating the S2 Ring & Watchdog Tuning

**Objective:** Connect S2 devices (Standard ET200SP, IE/PB Link HA, PN/PN Coupler) to the subordinate ring and tune watchdogs to survive a failover.

### Step-by-Step Configuration:

1. **S2 Multi-Assignment:**
   * In `Network view`, locate the pre-positioned S2 devices: `ET200SP-B`, `IEPBLinkHA-A`, and `PNPNCoupler-B`.
   * Just like the R1 device, click the "Not assigned" link on each PROFINET interface and select the redundant `PLC_1` system.
   * Notice that visually, TIA Portal routes their connection through the `YSwitch-A`. This is the hallmark of an S2 multi-assignment.

2. **Templated Device Verification (Gateway/Coupler):**
   * **IE/PB Link HA:** Verify it has a Profibus network assigned. The network gateway parameter should be set to 'local download required'. *Note: These gateways require an independent hardware download after the main PLC is loaded.*
   * **PN/PN Coupler:** Verify that the Transfer Areas (F-CD or F-MS) are present. For Safety communication to work, the F-Address must match exactly on both the Sender and Receiver sides. This configuration is mostly pre-templated for you.

3. **Manual Watchdog Tuning (Pinch Point):**
   * The default Watchdog update cycle count (typically 3 cycles, ~6ms) is insufficient for an S7-1500RH system to survive a switchover. If left at default, the IO will drop out during a failover event.
   * **Manual Method (Primary):** Select `ET200SP-B`. Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Real time settings` -> `IO cycle`.
   * Change the `Update time` to a stable manual value (e.g., 2.0ms or 4.0ms) instead of "Automatic".
   * Change the `Accepted update cycles without IO data` to a larger number (e.g., 100 or 150) so that the calculated `Watchdog time` exceeds 300ms (aim for ~400ms).
   * **Alternative Method:** If installed, use the 'PN Watchdog' TIA Portal Add-in to bulk update these settings across all S2 devices.

> **Pro-Tip: Watchdog Tuning - Don't Guess!**
> Do not just guess watchdog timers. The necessary watchdog time is directly correlated to the maximum system switchover time, which is influenced by the amount of retentive data, the distance between CPUs, and the number of PROFINET nodes. For a standard 1518HF demo, >300ms is usually safe. If your real-world process cannot tolerate a 300ms data freeze, the 1500R/H system might not be the correct hardware architecture.

---

## Lab 4: Failure Scenarios & Diagnostics

**Objective:** Download the full configuration and validate the system's resilience by intentionally causing faults.

### Step-by-Step Configuration:

1. **Compile and Download:**
   * Ensure both primary and backup CPUs are in STOP mode.
   * Compile the hardware and software. Download the project to the primary CPU.
   * Switch the primary CPU to RUN.
   * Switch the backup CPU to RUN. Wait for the SYNCUP phase to complete. Both CPUs should indicate solid green RUN LEDs (RUN-Redundant state).
   * *Note:* You must perform a separate, independent download to the IE/PB Link HA.

2. **Network Failover Test:**
   * Disconnect the PROFINET cable between `Switch-A` and the `ET200SP-A` R1 Interface.
   * **Observation:** The IO should continue to operate without interruption, seamlessly failing over to Side B.
   * **Diagnostics:** Go online and open the diagnostic buffer of the CPU. You should see an entry indicating peripheral redundancy loss (calling OB70).

3. **CPU Failover Test (Bumpless Transfer):**
   * Reconnect the network cable and wait for the system to return to a healthy state.
   * Physically turn off the power supply to the Primary CPU (or flip its switch to STOP).
   * **Observation:** The Backup CPU instantly assumes the Primary role. The process outputs (e.g., indicator lamps) should remain stable (Bumpless Transfer).
   * **Diagnostics:** Check the diagnostic buffer of the new Primary CPU. You will see an entry for system redundancy loss (calling OB72).

> **Pro-Tip: Gateway Startup Issues**
> If the IE/PB Link HA remains in STOP mode after its initial download, it often requires a manual push. You can transition it to RUN mode via TIA Portal's 'Online access' -> 'Online & diagnostics' -> 'Online tools'. Additionally, always verify the mandatory C-PLUG (Configuration Plug) is securely inserted behind the front cover.
