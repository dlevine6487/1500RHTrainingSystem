# Lab 2: The Y-Switch & Subordinate S2 Networks

**Objective:** Configure the Scalance XF204-DNA (Y-Switch) to safely bridge the R1 highly available network to a standard S2 ring.

### Step-by-Step Configuration:

1. **The Subnet Refactoring Pinch Point (The Flat Network Requirement):**
   * Try to multi-assign the `YSwitch-A` to the redundant system in the `Network view`. Because your PLCs are on separate subnets (`PN/IE_1` and `PN/IE_2`), the S2 Multi-assignment *will fail*.
   * **The Lesson:** An S2 device (like the Y-Switch, and the devices behind it) requires a logical path to *both* CPUs simultaneously. This is impossible if the CPUs reside on disjoint PROFINET subnets. Therefore, introducing a Y-Switch—or setting up backbone switches as S2 devices—mandates a flat network view configuration across *one* PROFINET subnet.
   * **Action:**
     1. Refactor your network. Delete the disjoint subnets (`PN/IE_1` and `PN/IE_2`).
     2. Create a new, single PROFINET subnet and rename it to `PN/IE_Plant`.
     3. Assign both the Primary CPU and Backup CPU PROFINET interfaces to this unified `PN/IE_Plant` subnet.
     4. Re-assign `Switch-A` and `Switch-B` to `PN/IE_Plant`. Note that you can now multi-assign them as S2 devices if desired, granting them true PROFINET redundancy and diagnostic reporting!
     5. Re-verify your MRP domain assignments (`mrpdomain-1` and `mrpdomain-2`) are still correctly applied to the PLCs and Switches.

2. **Verifying Redundancy Modes in TIA Portal:**
   * After the network is refactored to a shared subnet and devices are multi-assigned, you can explicitly verify the connection types.
   * In the `Network view`, click on the `I/O communication` tab located above the tabular area.
   * Look at the `Mode` column. You should clearly see the distinction between your devices: The backbone ET200SP-A will be listed as `IO device(R1)`, while multi-assigned switches/Y-Switches will display as `IO device(S2)`, and single-assigned devices will show `IO device(S1)`.

3. **Enable DNA Redundancy:**
   * Locate the `YSwitch-A` device in the `Network view` (it is pre-positioned).
   * Select the device and go to `Properties` -> `General` -> `Module parameters`.
   * Locate the setting for `DNA redundancy` and explicitly enable it. This tells the switch it is acting as a Y-Switch, not a standard Scalance switch.

4. **Configure Ring Redundancy (MRP Manager):**
   * Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`.
   * Set the Media Redundancy Role to **MRP Manager**.
   * Under the MRP Domain drop-down, select `mrpdomain-3`.
   * *If `mrpdomain-3` does not exist:* Click the 'Domain settings' button, add Domain 3 to the project's MRP configurations, and ensure it is assigned to this switch's subnet.

5. **Map Physical Ports:**
   * Carefully check the properties of the Ring ports. Ensure they are mapped to the exact physical ports connecting to the subordinate S2 ring (typically P1.1 and P2.1, but verify against your physical wiring).
   * The uplink ports to the R1 backbone must *not* be part of `mrpdomain-3`.

> **Pro-Tip: Topology View Verification**
> The number one cause of Y-Switch commissioning failure is a mismatch between the physical wiring and the logical configuration in TIA Portal. Always use the `Topology view` in TIA Portal to explicitly draw the connections for the Y-Switch uplinks and the subordinate ring ports. If the graphical topology does not compile or match reality, the switch will likely fail to transition out of a fault state.