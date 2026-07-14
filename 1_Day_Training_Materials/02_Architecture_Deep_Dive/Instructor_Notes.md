# Instructor Notes: Architecture Deep Dive

## Preparation Checklist
* **Diagrams:** Display the `Network Topology Diagram.png` from the repository root on the main screen.
* **Whiteboard:** Draw the MRP rings separately to show logical isolation.

## Key Discussion Points

### 1. The "Split Backbone" Topology
* **Concept:** Instead of one massive network ring, the R1 system utilizes two completely separate physical rings (Side A and Side B).
* **Why:** If one ring completely fails (e.g., massive power surge destroying a switch), the other ring is physically isolated and unaffected.
* **Connection:** The PLCs and the R1 IO stations act as the bridges between the two rings. There is NO copper cable directly connecting Switch A to Switch B.

### 2. Understanding MRP Domains and Masters
* **What is MRP?** Media Redundancy Protocol. It prevents broadcast storms in ring topologies.
* **Who is the Master?**
    * **Domain 1 (Side A):** `PLC_1A` is the Manager. `Switch-A` is a Client.
    * **Domain 2 (Side B):** `PLC_1B` is the Manager. `Switch-B` is a Client.
    * **Domain 3 (Blue Ring/Subordinate):** `YSwitch-A` is the Manager. `PNPNCoupler-B` and `ET200SP-B` are Clients.
* **Key Rule:** A port cannot belong to multiple MRP domains.

### 3. Redundancy Classifications: S1, S2, and R1
* **S1 (Standard):** Connects to only one controller. If that controller fails, the IO drops. (Show slides explaining Switched S1 optimization).
* **S2 (System Redundant):** A single physical network connection that maintains logical Application Relations (ARs) to *both* controllers. If the primary controller fails, it switches to the backup AR.
* **R1 (Redundant Interface):** Two distinct physical network interfaces on the device, connected to two separate networks, communicating with the two separate controllers. Maximum availability.

### 4. R1 plus Network Switches
* **Scenario Discussion:** Address the user prompt: "R1 plus network switches linking S2 devices across multiple panels."
* Explain that while you can plug an S2 device directly into the R1 backbone switches (Switch-A/B), doing so across multiple panels can create massive, unmanageable rings.
* **Solution:** Introduce the Y-Switch. The Y-Switch sits on the R1 backbone and creates a localized, isolated S2 ring (the Blue Ring) for downstream panels. This localizes faults.