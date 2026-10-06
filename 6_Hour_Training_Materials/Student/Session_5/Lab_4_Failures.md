# Lab 4: Failure Scenarios & Diagnostics

**Objective:** Download the full configuration and validate the system's resilience by intentionally causing faults.

### Step-by-Step Configuration:

1. **Compile and Download:**
   * Ensure both primary and backup CPUs are in STOP mode.
   * Compile the hardware and software. Download the project to the primary CPU.
   * Switch the primary CPU to RUN.
   * Switch the backup CPU to RUN. Wait for the SYNCUP phase to complete. Both CPUs should indicate solid green RUN LEDs (RUN-Redundant state).
   * *Note:* You must perform a separate, independent download to the IE/PB Link HA.

2. **Network Failover Test:**
   * Disconnect the PROFINET cable between `Switch-A` and the `ET200SP-A` R1 Interface.
   * **Observation:** The IO should continue to operate without interruption, seamlessly failing over to Side B.
   * **Diagnostics:** Go online and open the diagnostic buffer of the CPU. You should see an entry indicating peripheral redundancy loss (calling OB70).

3. **CPU Failover Test (Bumpless Transfer):**
   * Reconnect the network cable and wait for the system to return to a healthy state.
   * Physically turn off the power supply to the Primary CPU (or flip its switch to STOP).
   * **Observation:** The Backup CPU instantly assumes the Primary role. The process outputs (e.g., indicator lamps) should remain stable (Bumpless Transfer).
   * **Diagnostics:** Check the diagnostic buffer of the new Primary CPU. You will see an entry for system redundancy loss (calling OB72).

4. **Pivot Point 2: Direct Hardwired Timing Presentation:**
   * **Guest Presentation (15 Min):** A presenter will showcase a separate subordinate PLC. This PLC utilizes a direct Digital Input coupled to the Digital Output of the Redundant System.
   * The presenter will demonstrate the physical time between failover events, explicitly comparing the timings in an R1 setup versus an S2 setup, and further comparing the timings with and without MRP configurations for the remote IO stations.

> **Pro-Tip: Gateway Startup Issues**
> If the IE/PB Link HA remains in STOP mode after its initial download, it often requires a manual push. You can transition it to RUN mode via TIA Portal's 'Online access' -> 'Online & diagnostics' -> 'Online tools'. Additionally, always verify the mandatory C-PLUG (Configuration Plug) is securely inserted behind the front cover.