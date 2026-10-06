# Instructor Notes: Session 6 - Programming Wrap-up

## 15:15 - 15:45 | Programming Considerations & Wrap-up (Theory)

### Key Teaching Moments
*   **H-CiR (Hardware Configuration in RUN):** Briefly explain that while software updates are seamless, changing hardware configuration often requires breaking the redundant state (dropping to RUN-Solo) or even stopping the process entirely, depending on the modification.
*   **Sync Restrictions:** Mention that certain asynchronous instructions (like some open user communications or file handling) behave differently or are restricted in an H-system due to the need to keep both CPUs perfectly cycle-synchronous.

### Hardware Teardown
At the end of the course, instruct students to clear the PROFINET names and IP addresses from the training hardware (or perform a factory reset via TIA Portal) to prepare the kits for the next class.

### Relevant Slide Content
Please reference `1500RH v21.pdf`:
*   **Slide: "CiR" (Around Slide 257)**: Discuss Hardware Configuration in RUN capabilities and limitations.
*   **Slide: "Programming recommendations SIMATIC S7-1500R/H" (Starts around Slide 5176)**: Review the specific programming restrictions and recommendations for cycle-synchronous execution.
*   **Slide: "RH_CTRL Instruction" (Starts around Slide 5253)**: Briefly introduce how user code can explicitly interact with the redundant state (e.g., forcing a sync-up or requesting redundancy status) using the RH_CTRL block.
