# Step 1: Enabling Hyper-V

## Goal
Enable Hyper-V on the Windows 11 Pro host so it can act as a hypervisor for the lab's virtual machines.

## Environment
- Host OS: Windows 11 Pro, 25H2
- Hardware:
  - Intel Hexa-Core i7-8700 (6 cores, 12 threads)
  - 32GB DDR4 RAM
  - Virtualization (VT-x / VT-d) enabled in BIOS
 
## Steps
I used PowerShell instead of the WindowsOptionalFeatures GUI checkbox method for this, since it's scriptable and repeatable and definitely closer to how something like this would be done at scale.

<br>

1. Confirmed that virtualization was enabled in the BIOS (The `systeminfo` command shows "Virtualization Enabled in Firmware: Yes")
2. Opened Powershell as administrator and entered:


    ```powershell
    Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V –All
    ```

      - `Enable-WindowsOptionalFeature`: tells Windows to turn on an optional OS feature not activated by default
      - `-Online`: targets currently running Windows OS on the local computer
      - `-FeatureName Microsoft-Hyper-V`: specifies the exact name of the package to install
      - `-All`: forces Windows to auto enable all parent features, sub features, and dependencies for Hyper-V

<br>

3. Restarted PC when prompted
4. Confirmed Hyper-V was active after the restart:


    ```powershell
    Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V
    # State: Enabled
    ```

<br>

5. Opened Hyper-V Manager and confirmed I could see the console with my hostname listed with no errors

## Issues Encountered
So I actually checked the BIOS beforehand and saw that VT-x and VT-d were disabled. Instead of enabling them, I went to Windows and entered the powershell command to enable the Hyper-V feature to see what would happen. I was expecting it to fail, but it installed without error. I then entered the `systeminfo` command and saw the feature was enabled at the OS level but it wasn't enabled at the firmware level. 

This taught me that the OS-level feature flag and the firmware-level capability are two separate things. Windows lets you turn on Hyper-V even if the CPU can't actually run it yet.

## Result
Hyper-V is installed and running. Hyper-V Manager is accessible and ready for virtual switch and VM creation (see [02-virtual-switches](../02-virtual-switches/README.md)).
