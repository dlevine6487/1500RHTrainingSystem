# Instructor Notes: The 1500RH Standard/Unit

## Preparation Checklist
* **Hardware:** Stand next to the demo rack. Ensure the PLCs are visible to the audience to point out the LED states.
* **Slides:** Prepare the "Single Mode" and "Feature Comparison" slides from the `Instructor_Slides.pdf`.

## Key Discussion Points

### 1. Capabilities and Constraints
* **What it CAN do:** Redundant IO (via LRedIO), OPC UA Server, Webserver, Long Term Tracing.
* **What it CANNOT do (Restrictions):** Motion Control (Technology Objects), Isochronous Mode, Hardware Configuration in RUN (H-CiR requires a switchover).
* Reference the comparison chart between S7-1500R, S7-1500H, and S7-400H.

### 2. Operational Modes
Walk the students through the lifecycle of a redundant system using the front-panel LEDs.
* **STOP:** Both CPUs halted.
* **RUN-Solo:** One CPU is running the process. The other is off, in STOP, or disconnected. There is NO redundancy in this state.
* **SYNCUP:** The critical transition phase. The Primary CPU copies its entire memory state (process image, DBs, bit memory) over the fiber optic link to the Backup CPU. The system must pause non-critical tasks to do this.
* **RUN-Redundant:** Both CPUs are running synchronously. This is the target state.

### 3. Single Mode for R-CPUs
* **Use Case:** Show the OEM vs. End Customer slide.
* **Explanation:** Single Mode allows an OEM to program and test a system with a single R-CPU without the PLC constantly generating "Redundancy Loss" errors.
* **Expansion:** Later, when the end-customer wants high availability, they simply install the second CPU, disable Single Mode, and the system becomes redundant without needing a new TIA Portal project download.

### 4. Automatic SYNCUP
* Briefly mention the `R_H_Sys_Status` block and the Automatic SYNCUP application example.
* Explain that without automatic handling, a maintenance tech might replace a failed CPU but forget to initiate the Syncup, leaving the system vulnerable (in RUN-Solo).