# Knowledge Base from Slides

## Configuration of System Redundancy R1 - Option 1: Subnet separation
* **Concept:** 2 separate subnets (e.g., Subnet 1 and Subnet 2) are used between the controller and the IO device.
* **Advantage:** Same (symmetric) IP addresses in both networks can be used.
* **Disadvantage:** No double-sided connection of S1 or S2 devices possible (via Y-Switch).

## Configuration of System Redundancy R1 - Option 2: Shared Subnet
* **Concept:** Controller and IO-devices are connected to a common subnet (e.g., Subnet 1).
* **Advantage:** Allows two-sided connection of S1 or S2 devices via Y-Switch.
* **Disadvantage:** No symmetric IP addresses possible.

## Configuration of System Redundancy R1 - Option 2: Shared subnet - configuration with S1 and S2 IO-devices
* **Combination of R1- with S2- and/or S1-Devices:** S2 and S1 devices can be connected to a R1 system by using the Y-Switch.
* The topology shows a shared subnet (Subnet 1) connecting the S7-1500R/H system to an R1-Device (ET 200SP), a Y-Switch (SCALANCE XF204-DNA), and an S2-Device (ET 200SP), all multi-assigned.

## Configuration of System Redundancy - Display of the redundancy mode in the TIA Portal
* In the Network view, S1, S2, and R1 devices are displayed graphically with multiple network connections indicating they are "Multi-Assigned".
* In the Network view's "I/O communication" tab/table, the exact distinction between S1, S2, and R1 is explicitly displayed in the "Mode" column for each device (e.g., `IO device(R1)`, `IO device(S1)`, `IO device(S2)`).

## Network Configuration Examples S7-1500H - Example with line topology
* **Line topology with R1 Redundancy:** Line topology is also supported for R1 setup, allowing up to 512 devices to be connected.
* **Feeding Strategy:** The feeding of the PN (PROFINET) line from different sides increases availability in case of a double line interruption.
    * **Single-sided feeding:** If both lines are interrupted at the same point, devices behind the interruption fail.
    * **Feeding from two sides:** If both lines are interrupted at the same point, all devices can still be reached because network traffic routes from both ends of the line.
