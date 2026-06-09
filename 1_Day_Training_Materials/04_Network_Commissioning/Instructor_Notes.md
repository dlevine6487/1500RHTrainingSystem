# Instructor Notes: Network Commissioning

## Preparation Checklist
* **Software:** TIA Portal open, Network View displayed.
* **Add-In:** Ensure the `PN Watchdog Add-In` is installed and activated on the instructor PC to demonstrate.

## Key Discussion Points

### 1. Assigning Names and IPs
* **The Rule:** In PROFINET, the "Name of Station" is king. IPs are assigned based on the name.
* **Redundancy Specifics:** The Redundant Controllers (Primary and Backup) use the exact same TIA Portal project. Therefore, they look for the exact same device names on the network.
* **Procedure:** Demonstrate using "Online Access" -> "Update Accessible Devices" -> "Online & Diagnostics" to assign names to the bare switches and IO stations.

### 2. The Watchdog Dilemma
* Write on the whiteboard: `Switchover Time = ~300ms`
* Ask the class: "If the default IO watchdog is 6ms, what happens during a 300ms switchover?" (Answer: The IO drops, outputs turn off, valves close).
* This is the number one reason HA systems fail during commissioning.

### 3. Calculating the Watchdog (Manual Method)
* Formula: `Watchdog Time = Update Time * Accepted Update Cycles`
* Walk through an example: If update time is 2ms, we need at least 150 cycles (300ms). To be safe, use 200 cycles (400ms).
* Emphasize: In redundant systems, you rarely want 1ms update times. 4ms or 8ms is usually much more stable for the network load.

### 4. The PN Watchdog Add-In (Automated Method)
* Demonstrate the Add-In.
* Show how selecting all devices and running the tool eliminates human math errors and saves significant engineering time.

### 5. Multi-Assignment Refresher
* Briefly reiterate what the class will do in Lab 1 regarding clicking the "Not Assigned" link to connect S2/R1 devices to the redundant `PLC_1` logical system.