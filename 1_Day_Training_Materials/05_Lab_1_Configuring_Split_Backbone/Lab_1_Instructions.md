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

### Step 5: Watchdog Tuning - Pinch Point Scenario
System failover takes up to 300ms. Let's intentionally configure a failure to understand how it feels in TIA Portal.
1. Select `ET200SP-A` > **PROFINET Interface** > **Advanced Options** > **Real time settings** > **IO Cycle**.
2. **Intentional Failure:** Leave the **Accepted update cycles without IO data** at the default value of `3` (yielding a Watchdog of ~6ms).
3. Select the `PLC_1` folder, Compile the Hardware, and **Download to device** (Controllers in STOP).
4. Put the primary CPU into RUN. Force a CPU switchover by pulling the sync fiber or stopping the primary CPU.
5. **Observation:** Open the TIA Portal diagnostics buffer. Note the "IO device failure" because the 6ms watchdog expired before the Backup CPU could take over. The outputs dropped (bumped transfer).
6. **Correction:** Go back offline. Change the **Accepted update cycles** to `100` (4.0ms * 100 = 400ms).
7. Download the corrected hardware configuration and test the switchover again. The IO should remain active without faulting.
*(Instructor Note: Demonstrate using the PN Watchdog Add-in here as well).*
