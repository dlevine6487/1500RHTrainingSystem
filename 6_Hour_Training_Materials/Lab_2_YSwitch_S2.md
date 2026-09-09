# Lab 2: The Y-Switch & Subordinate S2 Networks

**Objective:** Configure the Scalance XF204-DNA (Y-Switch) to safely bridge the R1 highly available network to a standard S2 ring.

### Step-by-Step Configuration:

1. **The Subnet Refactoring Pinch Point:**
   * Try to assign the `YSwitch-A` to the redundant system in the `Network view`. You will likely encounter a configuration limitation.
   * **The Lesson:** While a pure R1 design can utilize two separate subnets, integrating a Y-Switch in an S7-1500RH design *requires* configuring one PROFINET IO subnet with multiple domains.
   * **Action:** Refactor your network. Delete the second subnet (`PN/IE_2`) and assign both CPU interfaces and all backbone switches back to the single `PN/IE_1` subnet. Re-verify your MRP domain assignments (Domain 1 and 2) are still correct. This decision must be made early in real projects to avoid massive rework!

2. **Enable DNA Redundancy:**
   * Locate the `YSwitch-A` device in the `Network view` (it is pre-positioned).
   * Select the device and go to `Properties` -> `General` -> `Module parameters`.
   * Locate the setting for `DNA redundancy` and explicitly enable it. This tells the switch it is acting as a Y-Switch, not a standard Scalance switch.

3. **Configure Ring Redundancy (MRP Manager):**
   * Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`.
   * Set the Media Redundancy Role to **MRP Manager**.
   * Under the MRP Domain drop-down, select `mrpdomain-3`.
   * *If `mrpdomain-3` does not exist:* Click the 'Domain settings' button, add Domain 3 to the project's MRP configurations, and ensure it is assigned to this switch's subnet.

4. **Map Physical Ports:**
   * Carefully check the properties of the Ring ports. Ensure they are mapped to the exact physical ports connecting to the subordinate S2 ring (typically P1.1 and P2.1, but verify against your physical wiring).
   * The uplink ports to the R1 backbone must *not* be part of `mrpdomain-3`.

> **Pro-Tip: Topology View Verification**
> The number one cause of Y-Switch commissioning failure is a mismatch between the physical wiring and the logical configuration in TIA Portal. Always use the `Topology view` in TIA Portal to explicitly draw the connections for the Y-Switch uplinks and the subordinate ring ports. If the graphical topology does not compile or match reality, the switch will likely fail to transition out of a fault state.