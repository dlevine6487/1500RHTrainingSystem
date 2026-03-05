## Module 3: Hardware Tagging & IO Configuration

This module outlines the IO tag assignments for the two Remote IO stations, ET200SP-A and ET200SP-B, based on the physical hardware configuration.

### 3.1 Efficient Engineering: Using Project Libraries
To streamline engineering and avoid manual typing errors, the Tag Tables for this project have been pre-configured in the global Project Library.

**Procedure:**
1.  Open the **Global Library** (or Project Library) in TIA Portal.
2.  Navigate to **Master Copies** > **Tag Tables**.
3.  Locate the specific tables (e.g., `ET200SP-A_Tags`, `ET200SP-B_Tags`).
4.  **Drag and Drop** the selected table into the **PLC Tags** folder of the S7-1518HF project tree.

*Note: The detailed lists below serve as a reference for verification purposes.*

### 3.2 ET200SP-A Tag List

#### Slot 4: DI 8x24VDC HF (Digital Inputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %I120.0 | BlueResetButton_SideA_1 | Bool | Blue Reset Button on Side A Channel 1 |
| %I120.1 | SelectorSwitch_SideA_1 | Bool | Left Channel Switch Selector on Side A Channel 1 |
| %I120.2 | SEL1400_SideA_1 | Bool | Selectivity Module Side A Channel 1 |
| %I120.3 | PSU6200_SideA_1 | Bool | Power Supply Unit Side A Channel 1 |
| %I120.4 | YSwitch_Fault_SideA_1 | Bool | Y Switch Fault Side A Channel 1 |
| %I120.5 | Switch_Fault_SideA_1 | Bool | XC208 Switch Fault Side A Channel 1 |
| %I121.0 | VS_BlueResetButton_SideA_1 | Bool | Value Status Blue Reset Button on Side A Channel 1 |
| %I121.1 | VS_SelectorSwitch_SideA_1 | Bool | Value Status of Selector Switch Side A Channel 1 |
| %I121.2 | VS_SEL1400_SideA_1 | Bool | Value Status Selectivity Module Side A Channel 1 |
| %I121.3 | VS_PSU6200_SideA_1 | Bool | Value Status Power Supply Unit Side A Channel 1 |
| %I121.4 | VS_YSwitch_Fault_SideA_1 | Bool | Value Status Y Switch Fault Side A Channel 1 |
| %I121.5 | VS_Switch_Fault_SideA_1 | Bool | Value Status XC208 Switch Fault Side A Channel 1 |

#### Slot 5: DI 8x24VDC HF (Digital Inputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %I110.0 | BlueResetButton_SideA_2 | Bool | Blue Reset Button on Side A Channel 2 |
| %I110.1 | SelectorSwitch_SideA_2 | Bool | Left Channel Switch Selector on Side A Channel 2 |
| %I110.2 | SEL1400_SideA_2 | Bool | Selectivity Module Side A Channel 2 |
| %I110.3 | PSU6200_SideA_2 | Bool | Power Supply Unit Side A Channel 2 |
| %I110.4 | YSwitch_Fault_SideA_2 | Bool | Y Switch Fault Side A Channel 2 |
| %I110.5 | Switch_Fault_SideA_2 | Bool | XC208 Switch Fault Side A Channel 2 |
| %I111.0 | VS_BlueResetButton_SideA_2 | Bool | Value Status Blue Reset Button on Side A Channel 2 |
| %I111.1 | VS_SelectorSwitch_SideA_2 | Bool | Value Status of Selector Switch Side A Channel 2 |
| %I111.2 | VS_SEL1400_SideA_2 | Bool | Value Status Selectivity Module Side A Channel 2 |
| %I111.3 | VS_PSU6200_SideA_2 | Bool | Value Status Power Supply Unit Side A Channel 2 |
| %I111.4 | VS_YSwitch_Fault_SideA_2 | Bool | Value Status Y Switch Fault Side A Channel 2 |
| %I111.5 | VS_Switch_Fault_SideA_2 | Bool | Value Status XC208 Switch Fault Side A Channel 2 |

#### Slot 6: DQ 8x24VDC/0.5A HF (Digital Outputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %Q100.0 | BlueResetLamp_SideA_1 | Bool | Blue Reset Button Lamp Side A Channel 1 |
| %Q100.1 | SelectorSwitchLamp_SideA_1 | Bool | Selector Switch Lamp Side A Channel 1 |
| %Q100.2 | GreenProcessLamp_SideA_1 | Bool | Green Process Lamp Side A Channel 1 |
| %Q100.3 | SEL1400_Reset_SideA_1 | Bool | Selectivity Module Reset button on HMI Side A Channel 1 |
| %I11.0 | VS_BlueResetLamp_SideA_1 | Bool | Value Status Blue Reset Button Lamp Side A Channel 1 |
| %I11.1 | VS_SelectorSwitchLamp_SideA_1 | Bool | Value Status Selector Switch Lamp Side A Channel 1 |
| %I11.2 | VS_GreenProcessLamp_SideA_1 | Bool | Value Status Green Process Lamp Side A Channel 1 |
| %I11.3 | VS_SEL1400_Reset_SideA_1 | Bool | Value Status Selectivity Module Reset button on HMI Side A Channel 1 |

#### Slot 7: DQ 8x24VDC/0.5A HF (Digital Outputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %Q110.0 | BlueResetLamp_SideA_2 | Bool | Blue Reset Button Lamp Side A Channel 2 |
| %Q110.1 | SelectorSwitchLamp_SideA_2 | Bool | Selector Switch Lamp Side A Channel 2 |
| %Q110.2 | GreenProcessLamp_SideA_2 | Bool | Green Process Lamp Side A Channel 2 |
| %Q110.3 | SEL1400_Reset_SideA_2 | Bool | Selectivity Module Reset button on HMI Side A Channel 2 |
| %I12.0 | VS_BlueResetLamp_SideA_2 | Bool | Value Status Blue Reset Button Lamp Side A Channel 2 |
| %I12.1 | VS_SelectorSwitchLamp_SideA_2 | Bool | Value Status Selector Switch Lamp Side A Channel 2 |
| %I12.2 | VS_GreenProcessLamp_SideA_2 | Bool | Value Status Green Process Lamp Side A Channel 2 |
| %I12.3 | VS_SEL1400_Reset_SideA_2 | Bool | Value Status Selectivity Module Reset button on HMI Side A Channel 2 |

#### Slot 8: F-DI 8x24VDC HF (Failsafe Inputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %I130.0 | Estop_SideA | Bool | |

#### Slot 9: F-DI 8x24VDC HF (Failsafe Inputs)
*No tags configured in current reference.*

#### Slot 10: F-DQ 4x24VDC/2A PM HF (Failsafe Outputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %Q270.0 | EstopLamp_SideA | Bool | |

#### Slot 11: F-DQ 4x24VDC/2A PM HF (Failsafe Outputs)
*No tags configured in current reference.*

### 3.3 ET200SP-B Tag List

#### Slot 3: DI 8x24VDC HF (Digital Inputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %I37.0 | BlueResetButton_SideB_2 | Bool | Blue Reset Button on Side B Channel 2 |
| %I37.1 | SelectorSwitch_SideB_2 | Bool | Switch Selector on Side B Channel 2 |
| %I37.2 | SEL1400_SideB_2 | Bool | Selectivity Module Side B Channel 2 |
| %I37.3 | PSU6200_SideB_2 | Bool | Power Supply Unit Side B Channel 2 |
| %I37.5 | Switch_Fault_SideB_2 | Bool | XC208 Switch Fault Side B Channel 2 |
| %I38.0 | VS_BlueResetButton_SideB_2 | Bool | Value Status Blue Reset Button on Side B Channel 2 |
| %I38.1 | VS_SelectorSwitch_SideB_2 | Bool | Value Status of Selector Switch Side B Channel 2 |
| %I38.2 | VS_SEL1400_SideB_2 | Bool | Value Status Selectivity Module Side B Channel 2 |
| %I38.3 | VS_PSU6200_SideB_2 | Bool | Value Status Power Supply Unit Side B Channel 2 |
| %I38.5 | VS_Switch_Fault_SideB_2 | Bool | Value Status XC208 Switch Fault Side B Channel 2 |

#### Slot 4: DI 8x24VDC HF (Digital Inputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %I63.0 | BlueResetButton_SideB_1 | Bool | Blue Reset Button on Side B Channel 1 |
| %I63.1 | SelectorSwitch_SideB_1 | Bool | Switch Selector on Side B Channel 1 |
| %I63.2 | SEL1400_SideB_1 | Bool | Selectivity Module Side B Channel 1 |
| %I63.3 | PSU6200_SideB_1 | Bool | Power Supply Unit Side B Channel 1 |
| %I63.5 | Switch_Fault_SideB_1 | Bool | XC208 Switch Fault Side B Channel 1 |
| %I64.0 | VS_BlueResetButton_SideB_1 | Bool | Value Status Blue Reset Button on Side B Channel 1 |
| %I64.1 | VS_SelectorSwitch_SideB_1 | Bool | Value Status of Selector Switch Side B Channel 1 |
| %I64.2 | VS_SEL1400_SideB_1 | Bool | Value Status Selectivity Module Side B Channel 1 |
| %I64.3 | VS_PSU6200_SideB_1 | Bool | Value Status Power Supply Unit Side B Channel 1 |
| %I64.5 | VS_Switch_Fault_SideB_1 | Bool | Value Status XC208 Switch Fault Side B Channel 1 |

#### Slot 5: DQ 8x24VDC/0.5A HF (Digital Outputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %Q12.0 | BlueResetLamp_SideB_1 | Bool | Blue Reset Button Lamp Side B Signal 1 |
| %Q12.1 | SelectorSwitchLamp_SideB_1 | Bool | Selector Switch Lamp Side B Channel 1 |
| %Q12.2 | GreenProcessLamp_SideB_1 | Bool | Green Process Lamp Side B Channel 1 |
| %Q12.3 | SEL1400_Reset_SideB_1 | Bool | Selectivity Module Reset button on HMI Side B Channel 1 |
| %I13.0 | VS_BlueResetLamp_SideB_1 | Bool | Value Status Blue Reset Lamp Side B Signal 1 |
| %I13.1 | VS_SelectorSwitchLamp_SideB_1 | Bool | Value Status Selector Switch Lamp Side B Signal 1 |
| %I13.2 | VS_GreenProcessLamp_SideB_1 | Bool | Value Status Green Process Lamp Side B Signal 1 |
| %I13.3 | VS_SEL1400_Reset_SideB_1 | Bool | Value Status Selectivity Module Side B Channel 1 |

#### Slot 6: DQ 8x24VDC/0.5A HF (Digital Outputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %Q18.0 | BlueResetLamp_SideB_2 | Bool | Blue Reset Button Lamp Side Channel 2 |
| %Q18.1 | SelectorSwitchLamp_SideB_2 | Bool | Selector Switch Lamp Side B Channel 2 |
| %Q18.2 | GreenProcessLamp_SideB_2 | Bool | Green Process Lamp Side B Channel 2 |
| %Q18.3 | SEL1400_Reset_SideB_2 | Bool | Selectivity Module Reset button on HMI Side B Channel 2 |
| %I14.0 | VS_BlueResetLamp_SideB_2 | Bool | Value Status Blue Reset Lamp Side B Channel 2 |
| %I14.1 | VS_SelectorSwitchLamp_SideB_2 | Bool | Value Status Selector Switch Lamp Side B Channel 2 |
| %I14.2 | VS_GreenProcessLamp_SideB_2 | Bool | Value Status Green Process Lamp Side B Channel 2 |
| %I14.3 | VS_SEL1400_Reset_SideB_2 | Bool | Value Status Selectivity Module Reset Side B Channel 2 |

#### Slot 7: F-DI 8x24VDC HF (Failsafe Inputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %I39.0 | Estop_SideB | Bool | Estop Side B |

#### Slot 8: F-DI 8x24VDC HF (Failsafe Inputs)
*No tags configured in current reference.*

#### Slot 9: F-DQ 4x24VDC/2A PM HF (Failsafe Outputs)
*No tags configured in current reference.*

#### Slot 10: F-DQ 4x24VDC/2A PM HF (Failsafe Outputs)
| Address | Tag Name | Data Type | Comment |
| :--- | :--- | :--- | :--- |
| %Q53.0 | EstopLamp_SideB | Bool | Estop Lamp Side B kit |

#### Slot 11: Server Module
*End of Station*
