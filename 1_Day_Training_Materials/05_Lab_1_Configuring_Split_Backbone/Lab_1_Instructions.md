# Lab 1: Configuring the Split Backbone & Y-Switch Integration

## Objective
Construct the R1 network and integrate the S2 subordinate ring, while discussing Y-Switch configuration do's and do not's.

## Prerequisites
* TIA Portal V17 (or newer) open.
* Repository cloned.
* Basic understanding of MRP.

## Step-by-Step Instructions

### Step 1: Project Setup and Network View
1. Open the provided TIA Portal project.
2. Navigate to **Devices & Networks** > **Network View**.
3. Identify the primary and backup controllers (`PLC_1A`, `PLC_1B`), the backbone switches (`Switch-A`, `Switch-B`), and the R1 ET200SP stations.

### Step 2: Configuring MRP Clients
Both backbone switches must be configured as MRP Clients to allow the 1518HF PLCs to act as Managers for Domain 1 and 2.
1. Select `Switch-A`. Navigate to its **Properties** > **PROFINET Interface** > **Advanced Options** > **Media Redundancy**.
2. Select `mrpdomain-1` from the dropdown. If it does not exist, click **Domain settings** to create it.
3. Set the **Role** to **Client**.
4. Define the **Ring Ports** (e.g., P1 & P2). Ensure the port facing the ET200SP-A is *not* a ring port.
5. Repeat the process for `Switch-B`, but use `mrpdomain-2`.

### Step 3: R1/S2 Multi-Assignment
Devices bridging the redundant controllers must be explicitly assigned to both.
1. On the `ET200SP-A` PROFINET interface module, locate the "Not Assigned" text.
2. Click it and select `PLC_1` (the redundant system). The text will change to **Multi-assigned**.
3. Locate `PNPNCoupler-B`. Click the "Not Assigned" text on the `X1` interface and assign it to `PLC_1` as well.
4. If using `IEPBLinkHA-A`, perform the multi-assignment here too.

### Step 4: Y-Switch Configuration (Do's and Don'ts)
The XF204-DNA bridges the redundant R1 backbone to the standard S2 "Blue Ring".
1. Select the `YSwitch-A`.
2. **Do:** Navigate to the PROFINET interface parameters and explicitly enable **DNA redundancy**.
3. **Do:** Navigate to Layer 2 settings and activate **Ring Redundancy**.
4. **Do:** Set the Y-Switch Role to **MRP Manager** for `mrpdomain-3`.
5. **Don't:** Assign the uplink ports (facing Switch-A/B) to `mrpdomain-1` or `mrpdomain-2`. They are independent.
6. **Don't:** Connect the "Blue Ring" cables to anything other than the explicitly defined Ring Ports (e.g., P1.1 and P2.1). Micro-loops will crash the segment.

### Step 5: Watchdog Tuning (Latency Mitigation)
System failover takes up to 300ms. Standard watchdogs will trigger a fault before the backup assumes control.
1. Select `ET200SP-A` > **PROFINET Interface** > **Advanced Options** > **Real time settings** > **IO Cycle**.
2. Set the **Update Time** to a stable value (e.g., `4.0 ms`).
3. Set **Accepted update cycles without IO data** to `100` (4.0ms * 100 = 400ms).
4. Verify the Watchdog time exceeds 300ms.
5. Repeat for `PNPNCoupler-B` and other S2 clients on the Blue Ring.
*(Instructor Note: Demonstrate using the PN Watchdog Add-in here as well).*

### Step 6: Compilation and Download
1. Select the `PLC_1` folder in the project tree.
2. Click **Compile** > **Hardware (rebuild all)**.
3. With the controllers in STOP mode, click **Download to device**.
