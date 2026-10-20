# Lab 2: The Y-Switch & Subordinate S2 Networks

**Objective:** Configure the Scalance XF204-DNA (Y-Switch) to safely bridge the R1 highly available network to a standard S2 ring.

*Note: It is assumed that the physical Y-Switch device has already received its PROFINET name and IP Address during the initial device assignment sweep in Lab 1. If not, perform that step via `Online access` now.*

### Step-by-Step Configuration:

1. **TIA Portal Projection: S2 Multi-assignment:**
   * In Lab 1, we learned that introducing S2 devices (like standard switches) requires a single unified PROFINET subnet (`PN/IE_Plant`). Because we have already refactored our network to meet this requirement, introducing the Y-Switch will be a straightforward process.
   * Locate the `YSwitch-A` device in the `Network view` (it is pre-positioned). Click "Not assigned" on its PROFINET interface and select `PLC_1` to multi-assign it as an S2 device. Because the network is already flat, this will succeed.

2. **TIA Portal Projection: Enable DNA & MRP Domains:**
   * Select the `YSwitch-A` device and go to `Properties` -> `General` -> `Module parameters`.
   * Locate the setting for `DNA redundancy` and explicitly enable it. This is the crucial step that tells the switch it is acting as a Y-Switch and not a standard Scalance switch.
   * Next, go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Media redundancy`.
   * Set the Media Redundancy Role to **MRP Manager**.
   * Under the MRP Domain drop-down, select `mrpdomain-3`. *(If `mrpdomain-3` does not exist: Click the 'Domain settings' button, add Domain 3 to the project's MRP configurations, and ensure it is assigned to this switch's subnet).*
   * Carefully check the properties of the Ring ports. Ensure they are mapped to the exact physical ports connecting to the subordinate S2 ring (typically P1.1 and P2.1). The uplink ports to the R1 backbone must *not* be part of `mrpdomain-3`.

3. **Verifying Redundancy Modes in TIA Portal:**
   * Before downloading, you can explicitly verify the connection types. In the `Network view`, click on the `I/O communication` tab located above the tabular area. Look at the `Mode` column. You should clearly see the distinction between your devices: The backbone ET200SP-A will be listed as `IO device(R1)`, while your multi-assigned switches and the `YSwitch-A` will display as `IO device(S2)`.

4. **Physical Switch Preparation: Web Server Initial Setup:**
   * Before we can download the TIA Portal hardware configuration to the physical switch, we must perform the initial security setup via the switch's Web Management (WBM).
   * Open a web browser and navigate to the IP address you assigned to `YSwitch-A` in Lab 1.
   * Log in using the default credentials (`admin` / `admin`).
   * The switch will immediately prompt you to change the password. Change it to the standard training password: `SiemensAdmin` / `Siemens1!`.
   * *Note: While in the WBM, you do not need to configure PROFINET or MRP settings here; TIA Portal will overwrite them during the download.*

5. **Hardware Download:**
   * With the TIA Portal configuration complete and the physical switch initialized, compile your project.
   * Highlight the `PLC_1` redundant system and perform a Hardware Download.
   * Since this includes network infrastructure changes (PROFINET and MRP roles), the PLCs must be in STOP mode to accept the configuration. Transition them to STOP, complete the download, and start them back up.

> **Pro-Tip: Topology View Verification**
> The number one cause of Y-Switch commissioning failure is a mismatch between the physical wiring and the logical configuration in TIA Portal. Always use the `Topology view` in TIA Portal to explicitly draw the connections for the Y-Switch uplinks and the subordinate ring ports. If the graphical topology does not compile or match reality, the switch will likely fail to transition out of a fault state.