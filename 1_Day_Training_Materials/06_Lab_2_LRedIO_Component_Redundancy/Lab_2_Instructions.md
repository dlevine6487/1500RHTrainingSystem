# Lab 2: Component-Level Redundancy with LRedIO

## Objective
Implement software redundancy for critical I/O across physical R1 stations using the LRedIO library, mitigating sensor or actuator failures.

## Prerequisites
* Lab 1 completed successfully.
* Redundant PLCs in RUN-Redundant mode.
* Familiarity with OB1 programming.

## Step-by-Step Instructions

### Step 1: Library Import
1. In TIA Portal, open the **Libraries** task card on the right.
2. Expand the **Project library** and navigate to **Master copies**.
3. Locate the `LRedIO` blocks folder.
4. Drag the necessary blocks (`LRedIO_DI_1oo2`, `LRedIO_DQ`) into your project's **Program blocks** folder.

### Step 2: Implementing 1oo2 Voting for Digital Inputs
We want a reset button signal to be registered if *either* the channel on Side A (channel 1) or Side A (channel 2) is active.
1. Open `Main` (OB1) or your primary cyclic logic block.
2. Drag the `LRedIO_DI_1oo2` instruction into a new network. Accept the auto-generated Instance DB.
3. Map the inputs:
   * **Input_A:** Map to the tag `%I120.0` (`"BlueResetButton_SideA_1"`).
   * **Input_B:** Map to the tag `%I110.0` (`"BlueResetButton_SideA_2"`).
   *(Note: Based on best practices, we mix channels on the same physical side for LRedIO mapping in this demo)*.
4. Set the **DiscrepancyTime** to `T#500ms` to account for network jitter or contact bounce.
5. Map the output `Out` to a new internal memory tag (e.g., `"Validated_Reset_Cmd"`).

### Step 3: Implementing Dual Drive for Digital Outputs
We want a single PLC command to activate a lamp on *both* Side A output channels simultaneously.
1. In a new network, drag the `LRedIO_DQ` instruction. Accept the Instance DB.
2. Map the input:
   * **In:** Map to your control logic signal (e.g., `"System_Reset_Cmd_Output"`).
3. Map the outputs:
   * **Output_A:** Map to the tag `%Q100.0` (`"BlueResetLamp_SideA_1"`).
   * **Output_B:** Map to the tag `%Q110.0` (`"BlueResetLamp_SideA_2"`).

### Step 4: Instructor Demo - TIA Portal V21 Openness
*The instructor will now demonstrate how to script the instantiation and mapping of LRedIO blocks automatically across large IO counts using TIA Portal V21 Openness.*

### Step 5: Compilation and Download
1. Compile the software changes.
2. Because these are logic changes, you can perform an **Online Download** (changes only) without stopping the primary CPU.
3. The Backup CPU will automatically sync the changes.

### Step 6: Testing
1. Go online and monitor OB1.
2. Toggle `%I120.0` True, then False. Verify the `Out` parameter turns True.
3. Toggle `%I110.0` True, then False. Verify the `Out` parameter turns True.
4. Trigger the `"System_Reset_Cmd_Output"`. Verify both `%Q100.0` and `%Q110.0` turn True simultaneously.
