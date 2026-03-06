## Module 1: R1 Backbone Architecture & Station Integration

### 1.1 Learning Objectives
*   **Construct** the "Split Backbone" topology using S7-1518HF PLCs and XC208 switches.
*   **Commission** Remote IO Stations (R1) spanning both backbone rings.
*   **Configure** Profinet Watchdog Timers to ensure bumpless transfer during redundancy switchover.
*   **Implement** correct MRP Client settings for distribution switches.

### 1.2 Architecture: The R1 Split Backbone
The foundation of the system is the **R1 Topology**. This consists of two physically isolated Profinet rings (Domains 1 & 2) that are logically unified by the R1 IO devices.
*   **Side A (Left):** PLC A + XC208-A + Left Interface of R1 IM. (Manages **MRP Domain 1**).
*   **Side B (Right):** PLC B + XC208-B + Right Interface of R1 IM. (Manages **MRP Domain 2**).
*   *Note:* There is no copper link between XC208-A and XC208-B.

### 1.3 Step-by-Step: XC208 Switch Configuration
The XC208 switches act as the entry points for the R1 IO. They must be configured as **MRP Clients** to allow the PLCs (Managers) to control the ring.

**Procedure for XC208-A (Side A):**
1.  **Add Device:** Drag `SCALANCE XC208` from the Hardware Catalog to Network View.
2.  **Assign Name:** Set Profinet Name to `Switch-A`.
3.  **MRP Configuration:**
    *   Navigate to **Profinet Interface** > **Advanced Options** > **Media Redundancy**.
    *   **Domain:** Select `mrpdomain-1`.
    *   **Role:** **Client**.
    *   **Ring Ports:** Select the two ports forming the ring with PLC A (e.g., P1 & P2).
4.  **Port Configuration:** Ensure the port facing the R1 IO Station (e.g., P5) is *not* a ring port but is active.

**Procedure for XC208-B (Side B):**
1.  **Add Device:** Add second XC208. Name: `Switch-B`.
2.  **MRP Configuration:**
    *   **Domain:** Select `mrpdomain-2`.
    *   **Role:** **Client**.
    *   **Ring Ports:** Select ports forming ring with PLC B.

### 1.4 Step-by-Step: R1 Remote IO Setup (ET 200SP)
An R1 Station uses an **IM 155-6 PN R1** interface module. This module essentially contains two separate network adapters in one head unit.

1.  **Hardware Selection:**
    *   Catalog: `Distributed I/O` > `ET 200SP` > `Interface Modules` > `Profinet` > `IM 155-6 PN R1`.
    *   Drag into Network View.
2.  **Device Assignment (Multi-assignment):**
    *   All R1 and critical backbone devices (such as `Switch-A`, `Switch-B`, and `ET200SP-A`) must be logically assigned to the redundant controller system.
    *   In the **Network view**, click the "Not Assigned" link on the device's PROFINET interface.
    *   Select the redundant system (e.g., `PLC_1`). The text will change to **Multi assigned**, confirming its connection to both **PLC1** and **PLC2**.
3.  **Topology Connection:**
    *   **Port 1 (Interface 1):** Connect to `Switch-A` (Side A).
    *   **Port 2 (Interface 2):** Connect to `Switch-B` (Side B).
    *   *Result:* TIA Portal recognizes this as a valid R1 connection spanning the two redundant subnets.

### 1.5 Critical Configuration: Watchdog & Update Time
**The Challenge:** When a Primary PLC fails, it takes time (up to 300ms) for the Backup PLC to assume control and send new data. If the IO Station's "Watchdog" timer expires *before* the Backup takes over, the outputs will turn off (flicker) before coming back on. This breaks "Bumpless Transfer".

**The Formula:**
`Watchdog Time = Update Time x Accepted Update Cycles`

#### Manual Calculation Method (Primary)
1.  **Navigate:** Go to the R1 Station > **Profinet Interface** > **Advanced Options** > **Real time settings** > **IO Cycle**.
2.  **Update Time:** Set a stable update time (e.g., **2.0 ms** or **4.0 ms**). Faster is not always better in redundant systems.
3.  **Watchdog (Accepted Update Cycles):**
    *   Standard default is often 3 cycles (2ms x 3 = 6ms). **This is too short for redundancy.**
    *   **Requirement:** Watchdog must be > System Switchover Time (approx 300ms).
    *   **Calculation:** 300ms / 2ms = 150 cycles.
    *   **Action:** Change "Accepted update cycles without IO data" to a manual value, e.g., **150** or **200**.
    *   *Target:* Ensure the calculated Watchdog Time is **> 300ms** (e.g., 400ms to be safe).
4.  **Verify:** Check the "Watchdog time" field updates to reflect the new calculation.

#### Automated Configuration: PN Watchdog Add-In (Alternative)
For efficient and error-free configuration, you can use the **PN Watchdog Add-In**. This tool automates the calculation and application of watchdog timers.

**1. Installation:**
*   Locate the Add-In file (`.addin`) provided in the documentation package.
*   Copy the file to the TIA Portal Add-Ins directory:
    *   **Path:** `C:\Program Files\Siemens\Automation\Portal V[Version]\AddIns\`
    *   *Alternative:* `Documents\Siemens\Automation\Portal V[Version]\AddIns\`

**2. Activation in TIA Portal:**
*   Open the **Add-ins** task card (right-side pane).
*   Expand the list to find **PN Watchdog**.
*   Right-click the Add-In and select **Activate**.
*   *Note:* Ensure "Mass data operations" are enabled if prompted.

**3. Usage:**
*   Open **Network View**.
*   Select the target IO Stations (e.g., ET200SP-A, Switch-A, Switch-B).
*   Right-click the selection > **Add-Ins** > **PN Watchdog**.
*   In the dialog, set the desired Watchdog Time (e.g., **400ms** to safely exceed the 300ms switchover).
*   Click **Run** or **Apply** to update the devices.

### 1.6 Device Commissioning: Assigning Names & IPs
Once the hardware configuration is complete, the physical devices must be commissioned using TIA Portal's **Online Access** features to match the project configuration.

**Procedure:**
1.  **Open Online Access:** Expand the "Online access" node in the project tree.
2.  **Select Interface:** Choose your network adapter (e.g., "PLCSIM" or your physical Ethernet adapter).
3.  **Scan:** Click **Update accessible devices**.
4.  **Commission Each Device:** For every device found (PLC-A, PLC-B, Switch-A, Switch-B, ET200SP-A):
    *   Expand the device entry.
    *   Double-click **Online & diagnostics**.
    *   **Assign Name:** Navigate to **Functions** > **Assign PROFINET name**.
        *   Select the matching name from the project configuration (e.g., `Switch-A`).
        *   Click **Assign name**.
        *   *Crucial:* The name must match exactly. This is how the Redundant Controllers identify the device.
    *   **Assign IP:** Navigate to **Functions** > **Assign IP address**.
        *   Enter the IP address defined in the hardware configuration.
        *   Click **Assign IP address**.

**Checklist:**
*   [ ] PLC-A (Primary) - 192.168.0.1
*   [ ] PLC-B (Backup) - 192.168.0.2
*   [ ] Switch-A (Left Backbone) - 192.168.0.10
*   [ ] Switch-B (Right Backbone) - 192.168.0.6
*   [ ] ET200SP-A (R1 Station) - 192.168.0.4 / 192.168.0.5

### 1.7 Security & Downloading Configuration
Proper security settings and operational modes are required to successfully download the hardware configuration to the physical devices.

#### Switch Security: Changing Default Credentials
The SCALANCE XC208 switches ship with default credentials that are flagged as insecure. During the initial download or first login, you will be prompted to change them.

1.  **Default Login:**
    *   Username: `admin`
    *   Password: `admin`
2.  **Required Action:**
    *   Access the switch via Web Based Management (WBM) using a browser at its IP (e.g., `https://192.168.0.10`).
    *   When prompted, change the password to the project standard.
    *   **Project Standard Credentials:**
        *   Username: `SiemensAdmin`
        *   Password: `Siemens1!`
3.  **TIA Portal User Management:** ensure these credentials are also updated in the TIA Portal project under the switch properties (Security / Users) so TIA can automatically log in during downloads.

#### Downloading to S7-1500R/H System
Downloading hardware changes to a redundant system has stricter requirements than a standard PLC.

*   **PLC State:** The S7-1500R/H system must often be in **STOP** mode to accept significant Hardware Configuration changes (such as modifying the PROFINET Topology, adding R1 devices, or changing IP addresses).
*   **Procedure:**
    1.  Select the **System** folder (PLC_1) in the Project Tree.
    2.  Click **Download to device**.
    3.  **Load Preview:** TIA Portal will perform a consistency check.
    4.  **Stop Requirement:** If the "Load preview" window indicates that the "Module must be stopped", you must check the "Stop all" or "Stop module" box.
    5.  **Synchronization:** After the download completes and the Primary PLC restarts, it will automatically synchronize the Backup PLC.

#### Downloading Project Data to the Backup CPU
By default, project data is downloaded to the Primary CPU. However, you can deliberately download to the Backup CPU. This strategy allows the primary CPU to continue controlling the active process during the download. Once complete, you force a switchover, making the newly updated Backup CPU the new Primary.

*   **Requirement:** The Backup CPU must be in the **STOP** operating state.

**Procedure:**
1.  **Select System:** Right-click the S7-1500R/H system folder in the project tree.
2.  **Initiate Download:** Select **Download to backup CPU** -> **Hardware and software (only changes)**. The backup CPU is automatically selected as the target in the *Extended download* dialog.
3.  **Configure Interface:** Select your subnet, PG/PC interface adapter, and physical interface connection.
4.  **Connect:** Click **Start search**. Ensure the *Choose target device* table accurately reflects the CPUs and their roles (Backup CPU should be selected by default).
5.  **Execute:** Click **Load** and review the *Results of loading* dialog.

>   **Pro-Tip (Retentive Data):** Exercise caution when downloading to the Backup CPU. If your project relies heavily on retentive data, the Backup CPU will boot utilizing potentially outdated retentive state data.

**Executing the Switchover:**
After a successful download to the Backup CPU, you must swap roles to activate the new configuration.
1.  Transition the **Primary CPU** to the **STOP** operating state.
2.  Transition the updated **Backup CPU** to the **RUN** operating state.
    *   *Result:* The former backup CPU becomes the new Primary CPU and takes over control of the process in the **RUN-Solo** state.
3.  Once the old primary is updated and brought back online, the system will execute a SYNCUP and return to the stable **RUN-Redundant** state.

**Troubleshooting:** If the download fails with "Loading of hardware configuration failed", verify that:
*   You are not trying to download while the system is in active Redundant (RUN-Redundant) mode for changes that require a restart.
*   The device names and IPs match exactly.
*   The "Protection" level in the PLC properties allows loading.

### 1.8 Reference Documents
*   `Application_Examples_and_Docs/System_Architecture_and_Manuals/s71500rh_manual_en-US_en-US_v21.pdf`
*   `Application_Examples_and_Docs/Redundancy_and_MRP/109739614_MRP_DOKU_V10_en.pdf`
*   `Application_Examples_and_Docs/Hardware_Components/et200sp_im_155_6_pn_r1_en-US_en-US.pdf`
*   `Application_Examples_and_Docs/Safety_and_Libraries/109769093_S7_1500RH_AddIn_DOC_V1_4_en.pdf`
