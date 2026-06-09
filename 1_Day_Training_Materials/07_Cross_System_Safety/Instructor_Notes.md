# Instructor Notes: Cross-System Safety (F-Link)

## Preparation Checklist
* **Software:** TIA Portal project open, showing the `PNPNCoupler-B` properties and the `Main_Safety_RTG` block.
* **Whiteboard:** Draw two PLCs with a PN Coupler in between to illustrate the Transfer Areas.

## Key Discussion Points

### 1. When to PN Couple or Not to PN Couple?
* Address the user prompt discussion topic directly.
* **When TO use a PN Coupler:**
    * When connecting two completely separate automation islands (e.g., a Siemens HA system to an OEM skid with a different PLC).
    * When IP addressing schemes conflict and cannot be changed (the Coupler isolates them).
    * When you need a hard deterministic data handoff point for safety accountability.
* **When NOT to use a PN Coupler:**
    * When all devices are within the same engineering scope and subnet. Use standard I-Device or Shared Device communication instead, as it requires less hardware and less configuration overhead.

### 2. The Mechanics of the PN/PN Coupler
* It has two independent sides: X1 (connected to our 1518HF system) and X2 (connected to the Subordinate PLC-C).
* **The Mirror Rule:** Transfer areas must be exact opposites.
    * If X1 has an IN of 10 bytes and an OUT of 5 bytes.
    * Then X2 MUST have an OUT of 10 bytes and an IN of 5 bytes.

### 3. Safety over the Coupler (PROFIsafe)
* Standard IO areas cannot carry safety data. You must select an **F-CD** or **F-MS** transfer area type.
* These transfer areas act as "Virtual F-Modules".
* **Crucial Parameter:** The **F-Destination Address**. This address must match exactly on both the Sender (X1) and Receiver (X2) configurations, otherwise, the safety telegrams will be rejected.

### 4. SENDDP and RCVDP Instructions
* Walk through the logic in `Main_Safety_RTG`.
* Explain that `SENDDP` packages the failsafe data (e.g., Global E-Stop status) into a secure PROFIsafe telegram.
* Explain that `RCVDP` unpacks it on the other side.
* Point out the F-Monitoring Time on the RCVDP block. Remind the class that this time must be > 350ms to survive the system switchover, just like the standard IO watchdogs!