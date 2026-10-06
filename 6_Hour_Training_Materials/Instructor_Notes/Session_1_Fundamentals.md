# Instructor Notes: Session 1 - Fundamentals

## 09:00 - 10:00 | S7-1500R/H Fundamentals & The R1 Backbone (Theory)

### General Pacing & Philosophy
*   **Time Management:** 6 hours is tight for this material. Strictly enforce break times and keep theoretical lectures concise. The real learning happens during the "Pinch Points" in the labs.
*   **The Starter Project:** Ensure the TIA Portal starter project is pristine before every class. It should contain the generic hardware profiles (PLCs, IO stations, Switches) and templated tags, but **must not** have IP addresses assigned, MRP domains configured, multi-assignments made, or watchdogs tuned.

### Key Teaching Moments
*   **Key Concept:** Clearly distinguish between **R1** (Redundant Interface - two physical ports, two separate network rings) and **S2** (System Redundancy - single network connection logically communicating with two CPUs). Use the whiteboard heavily here.
*   **Demo Kit Orientation:** Spend 5 minutes physically pointing out the components on the demo kit. Show them the split backbone wiring vs. the subordinate S2 ring wiring.

### Relevant Slide Content
Please reference `1500RH v21.pdf`:
*   **Slide: "System Overview" (Starts around Slide 845)**: Use this section to introduce the general concept of the redundant system and the differences between the R (ring) and H (high-availability) CPU synchronization methods.
*   **Slide: "System Redundancy R1" (Starts around Slide 2126)**: Use this to explain the two physical connections and the redundant interfaces before diving into the lab.
*   **Slide: "System Redundancy S2" (Starts around Slide 1821)**: Use this to contrast R1 with S2, showing how S2 involves a logical multi-assignment through a single physical interface path (which will be relevant later for the Y-Switch and standard switches).
