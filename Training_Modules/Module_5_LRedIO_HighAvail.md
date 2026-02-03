## Module 5: High Availability IO with LRedIO

### 5.1 Overview
This module covers the implementation of High Availability IO using the Siemens `LRedIO` library. This technique allows the standard R1 Remote IO stations (ET200SP-A and ET200SP-B) to function as a unified redundant IO system for critical signals.

### 5.2 Redundancy Concept
By pairing a module in ET200SP-A with a corresponding module in ET200SP-B, we achieve component-level redundancy.

*   **Digital Inputs (1oo2 Voting):**
    *   The `LRedIO_DI` block reads the status of a sensor connected to both Station A and Station B.
    *   It performs a **1-out-of-2 (1oo2)** vote (OR logic) or discrepancy analysis.
    *   **Benefit:** If one wire breaks or one IO module fails, the PLC continues to receive the "True" signal from the remaining healthy side.

*   **Digital Outputs (Dual Drive):**
    *   The `LRedIO_DQ` block accepts a single command from the PLC logic.
    *   It drives the physical outputs on **both** Station A and Station B simultaneously.
    *   **Benefit:** If one output card fails, the actuator (e.g., lamp or relay) remains energized by the parallel circuit.

**Note:** There are **no Analog points** on this demo configuration. The `LRedIO` implementation here is strictly for Digital Inputs (DI) and Digital Outputs (DQ).

### 5.3 Implementation Guide

#### 1. Library Import
Open the **Project Library** > **Types** and drag the `LRedIO` block family into your program resources.

#### 2. Configuring a Redundant Input (DI)
Use the `LRedIO_DI_1oo2` Function Block.
*   **Input_A:** Map to the tag from ET200SP-A (e.g., `%I120.0 "BlueResetButton_SideA_1"`).
*   **Input_B:** Map to the tag from ET200SP-B (e.g., `%I63.0 "BlueResetButton_SideB_1"`).
*   **Discrepancy Time:** Set to ~500ms to filter out network jitter.
*   **Result:** Use the block's `Out` parameter in your logic.

#### 3. Configuring a Redundant Output (DQ)
Use the `LRedIO_DQ` Function Block.
*   **In:** Map from your control logic (e.g., "System_Reset_Cmd").
*   **Output_A:** Map to the tag for ET200SP-A (e.g., `%Q100.0 "BlueResetLamp_SideA_1"`).
*   **Output_B:** Map to the tag for ET200SP-B (e.g., `%Q12.0 "BlueResetLamp_SideB_1"`).
