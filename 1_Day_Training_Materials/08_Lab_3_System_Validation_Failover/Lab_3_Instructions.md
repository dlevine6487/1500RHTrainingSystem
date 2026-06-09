# Lab 3: System Validation & Failover Simulation

## Objective
Prove system resilience against hardware and network faults, and understand how to interpret redundancy diagnostics.

## Prerequisites
* Lab 1 and Lab 2 completed successfully.
* Hardware setup running and physically accessible.

## Step-by-Step Instructions

### Step 1: Baseline Verification
1. Open TIA Portal and go **Online** with `PLC_1`.
2. Ensure both the Primary and Backup 1518HF PLCs indicate solid green **RUN** LEDs on the physical hardware.
3. Open a watch table and monitor `"Validated_Reset_Cmd"`. Force the input signal to True.

### Step 2: CPU Failover Simulation
1. Locate the physical switch on the front of the **Primary PLC**.
2. Flip the switch from **RUN** to **STOP** (or pull the power plug if instructed).
3. **Observation:**
   * The "BlueResetLamp" should remain lit.
   * The `"Validated_Reset_Cmd"` in the watch table should stay True without a dip.
   * The Backup PLC's role LED will change to indicate it is now the Primary.
   * This confirms a "Bumpless Transfer".

### Step 3: Diagnostic Evaluation (OB72)
1. Transition the stopped CPU back to **RUN** to initiate a SYNCUP and restore the RUN-Redundant state.
2. In TIA Portal, open the **Online & diagnostics** view for the new Primary PLC.
3. Navigate to the **Diagnostics buffer**.
4. Locate the entries related to the failure and recovery.
5. Notice the calls to **OB72** (Redundancy Error). OB72 is called when the H-Sync connection is lost or restored, or when the system transitions out of the RUN-Redundant state.

### Step 4: Network Break Simulation
1. Ensure the system is back in RUN-Redundant state.
2. Physically disconnect the PROFINET Ethernet cable between `Switch-A` and the `ET200SP-A` R1 Interface.
3. **Observation:** Process logic (lamps/buttons) must continue uninterrupted. Data is now routing exclusively through Domain 2 (Side B).

### Step 5: Diagnostic Evaluation (OB70)
1. Refresh the **Diagnostics buffer**.
2. Locate the entries related to the IO station.
3. Notice the calls to **OB70** (IO Redundancy Error). OB70 is specifically called when a peripheral device (like our R1 ET200SP or an S2 device) loses one of its redundant ARs (Application Relations), but remains connected via the other.
4. Reconnect the cable and observe the OB70 "Redundancy Return" event.
