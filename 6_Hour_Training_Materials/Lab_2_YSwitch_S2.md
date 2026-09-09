# Lab 2: The Y-Switch & Subordinate S2 Networks

**Objective:** Configure the Scalance XF204-DNA (Y-Switch) to safely bridge the R1 highly available network to a standard S2 ring.

### Step-by-Step Configuration:

1. **Introduction to the Y-Switch & Subordinate Networks:**
   * In Lab 1, we learned that introducing S2 devices (like standard switches) requires a single unified PROFINET subnet (`PN/IE_Plant`). Because we have already refactored our network to meet this requirement, introducing the Y-Switch will be a straightforward process.
   * The Y-Switch allows us to take a standard device that does not support S2 redundancy natively, and place it behind an S2 capable device. The Y-Switch itself connects to both R1 backbone rings (Side A and Side B) and manages a subordinate MRP ring for the standard devices.
   * Locate the `YSwitch-A` device in the `Network view` (it is pre-positioned). Click "Not assigned" on its PROFINET interface and select `PLC_1` to multi-assign it as an S2 device. Because the network is already flat, this will succeed.

2. **Verifying Redundancy Modes in TIA Portal:**
   * Now that the Y-Switch is multi-assigned, you can explicitly verify the connection types.
   * In the `Network view`, click on the `I/O communication` tab located above the tabular area.
   * Look at the `Mode` column. You should clearly see the distinction between your devices: The backbone ET200SP-A will be listed as `IO device(R1)`, while your multi-assigned switches and the `YSwitch-A` will display as `IO device(S2)`.

3. **Enable DNA Redundancy:**
   * Select the `YSwitch-A` device and go to `Properties` -> `General` -> `Module parameters`.
   * Locate the setting for `DNA redundancy` and explicitly enable it. This is the crucial step that tells the switch it is acting as a Y-Switch and not a standard Scalance switch.

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