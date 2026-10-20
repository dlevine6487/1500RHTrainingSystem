# Lab 3: Populating the S2 Ring & Watchdog Tuning

**Objective:** Connect S2 devices (Standard ET200SP, IE/PB Link HA, PN/PN Coupler) to the subordinate ring and tune watchdogs to survive a failover.

### Step-by-Step Configuration:

1. **S2 Multi-Assignment:**
   * In `Network view`, locate the pre-positioned S2 devices: `ET200SP-B`, `IEPBLinkHA-A`, and `PNPNCoupler-B`.
   * Just like the R1 device, click the "Not assigned" link on each PROFINET interface and select the redundant `PLC_1` system.
   * Notice that visually, TIA Portal routes their connection through the `YSwitch-A`. This is the hallmark of an S2 multi-assignment.

2. **Templated Device Verification (Gateway/Coupler):**
   * **IE/PB Link HA:** Verify it has a Profibus network assigned. The network gateway parameter should be set to 'local download required'. *Note: These gateways require an independent hardware download after the main PLC is loaded.*
   * **PN/PN Coupler:** Verify that the Transfer Areas (F-CD or F-MS) are present. For Safety communication to work, the F-Address must match exactly on both the Sender and Receiver sides. This configuration is mostly pre-templated for you.

> **Pro-Tip: Subordinate MRP Domains**
> Remember to consider which domain you subscribe your subordinate S2 network to! In this demo, the standard ET200SP-B, IE/PB Link HA, and PN/PN Coupler are part of an MRP ring controlled on `mrpdomain-3`. This domain is *not* managed directly by the PLC CPUs (which handle Domains 1 and 2), but is instead managed solely by the Y-Switch to isolate traffic. Ensure the PROFINET interfaces for these subordinate devices are explicitly assigned to `mrpdomain-3` under their Media Redundancy settings.

3. **Manual Watchdog Tuning (Pinch Point):**
   * The default Watchdog update cycle count (typically 3 cycles, ~6ms) is insufficient for an S7-1500RH system to survive a switchover. If left at default, the IO will drop out during a failover event.
   * **Manual Method (Primary):** Select `ET200SP-B`. Go to `Properties` -> `PROFINET interface [X1]` -> `Advanced options` -> `Real time settings` -> `IO cycle`.
   * Change the `Update time` to a stable manual value (e.g., 2.0ms or 4.0ms) instead of "Automatic".
   * Change the `Accepted update cycles without IO data` to a larger number (e.g., 100 or 150) so that the calculated `Watchdog time` exceeds 300ms (aim for ~400ms).
   * **Alternative Method:** If installed, use the 'PN Watchdog' TIA Portal Add-in to bulk update these settings across all S2 devices.

> **Pro-Tip: Watchdog Tuning - Don't Guess!**
> Do not just guess watchdog timers. The necessary watchdog time is directly correlated to the maximum system switchover time, which is influenced by the amount of retentive data, the distance between CPUs, and the number of PROFINET nodes. For a standard 1518HF demo, >300ms is usually safe. If your real-world process cannot tolerate a 300ms data freeze, the 1500R/H system might not be the correct hardware architecture.