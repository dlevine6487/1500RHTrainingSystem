## Module 5: Failsafe Programming & Cross-System Safety

### 5.1 Learning Objectives
*   **Configure** Failsafe Inputs (F-DI) and Outputs (F-DQ) on R1/S2 stations.
*   **Implement** standard Safety Logic (E-Stop, Feedback Loop) using Siemens Safety Advanced.
*   **Establish** PROFIsafe communication across the PN/PN Coupler.

### 5.2 Reference Documents
*   `Application_Examples_and_Docs/Safety_and_Libraries/109995680_LSafe_FRedIO_V1_0_en.pdf`
*   `Application_Examples_and_Docs/Safety_and_Libraries/109826840_LSafe_VoteScale_V1_0_1_en.pdf`
*   `Application_Examples_and_Docs/Hardware_Components/pn_pn_coupler_hardware_manual_en-US_en-US.pdf`

### 5.3 Safety Hardware Configuration
Before programming, the Safety Hardware must be parameterized.

1.  **F-Destination Address (F-Dest-Add):** Each F-Module must have a unique DIP switch address.
    *   *Tip:* Set the address in TIA Portal first, then physically match the DIP switches on the module.
2.  **F-Monitoring Time:** Ensure the default (150ms) is sufficient for the Redundant Switchover.
    *   *Recommendation:* Increase to **350ms or higher** for devices that might experience redundancy delays, to prevent inadvertent "Communication Faults" during CPU swap.

### 5.4 Programming E-Stop Logic
1.  **Safety Administration:** Enable "F-Capability" in the S7-1518HF Properties. Set a Safety Password.
2.  **Safety Runtime Group:** TIA Portal creates a "Main_Safety_RTG" OB (OB123). Place all safety logic here.
3.  **ESTOP1 Block:**
    *   **Call:** Call `ESTOP1` instruction from the Safety Library.
    *   **Input:** Map to the F-DI channel of the E-Stop button.
    *   **Output:** Map to the global "Safe_Enable" tag or F-DQ channels.
    *   **Ack:** Map to a standard HMI/Button reset tag for acknowledgment.

### 5.5 PN/PN Coupler Safety (F-Link)
To pass safety signals (e.g., "Global E-Stop") to the Subordinate PLC:

1.  **Transfer Area:** In the PN/PN Coupler settings, create a F-CD (F-Configuration Data) or F-MS (Module) transfer area, not just standard IO.
2.  **SENDDP / RCVDP:**
    *   **System A (Sender):** Use the `SENDDP` instruction to package safety variables into the PROFIsafe telegram.
    *   **System B (Receiver):** Use `RCVDP` to unpack them.
3.  **F_Dest_Add:** The PN/PN Coupler transfer area acts as a "Virtual F-Module". It needs a unique F-Address that must match on both sides (Sender and Receiver).
