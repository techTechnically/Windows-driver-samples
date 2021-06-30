# What is this?

This is a duplicate of the KMDF echo device/driver sample provided by Microsoft in their driver samples github: https://github.com/microsoft/Windows-driver-samples. Specifically: Windows-driver-samples/general/echo/kmdf/. This sample has been tweaked to fire a WHEA error on device initialization. Feel free to check out the changes applied on top of the original by reviewing this commit: ce1559f8ca30cdb861d18a18aac60caa18dd77ee .

# Prerequisites

* Windows 10 64-bit
    * Professional edition (or greater) would be ideal so that you can use Hyper-V manager's quick create feature to initialize a separate W10 developer VM environment. 
* Visual Studio Community 2019
    * Other versions *might* be okay but this driver was only tested with the above IDE.
* Windows SDK
* Windows Driver Kit

# How to...
## ...Compile

To compile this device/driver do the following:
1. Install all of the above prerequisites.
2. Open the Windows-driver-samples/general/echo/kmdf_wheaExample/kmdfecho_wheaExample.sln solution file.
3. Build the solution

### ⚠ Warning ⚠

Spectre mitigations have been disabled on each project rather than enforcing a spectre mitigation library be installed for ease of use. Would definitely _**not**_ use this in production code. More information can be found here: https://docs.microsoft.com/en-us/visualstudio/msbuild/errors/msb8040?f1url=%3FappId%3DDev16IDEF1%26l%3DEN-US%26k%3Dk(MSB8040)%26rd%3Dtrue&view=vs-2019.

## ...Install
1. Find the *"echo" output* folder on your development machine. It will most likely be: Windows-driver-samples\general\echo\kmdf_wheaExample\driver\AutoSync\x64\Debug\echo.
    * Files should be **echo.inf**, **echo.sys**, and **kmdfsamples.cat** .
2. Find **devcon.exe** and copy it to the "echo" output folder.
    * devcon.exe will most likely be located in: C:\Program Files (x86)\Windows Kits\10\Tools\x64.
3. Copy the *"echo" output folder* to the Windows 10 machine you wish to install the driver (ideally, this should be a VM).

*The remaining steps are to take place on the target Windows 10 machine.*

4. Disable Secure Boot
5. Allow for test signed drivers to be install on this Windows machine.
    * An easy way to do this is to open command prompt with admin privileges  and run the command: `bcdedit /set testsigning on`
    * This cannot be completed unless Secure Boot is disabled.
6. Restart the machine
7. Install the device by running the following from command prompt with admin privileges : `devcon install echo.inf root\ECHO`
    * You should see output like: 
    > Device node created. Install is complete when drivers installed...
    > 
    > Updating drivers for root\ECHO from C:\Users\User\Desktop\echo\echo.inf.
    > 
    > Drivers installed successfully.
    * If you encounter issue here then consult the log file located at: C:\Windows\INF\setupapi.dev.log.
 8. To confirm everything has been installed properly, open Device Manager and under "Sample Device" you should see a device listed as "This fires WHEA errors PEW PEW".
    * A WHEA logger event should have been logged to the Event Viewer as well. You can find it under the "Administrative Events" custom view and under the following "Application and Service Logs / Microsoft / Windows / Kernel-WHEA / Errors".
    * You can create more events by disabling and then re-enabling the driver in the Device Manager.
 9. When finished, make sure to uninstall the device (and driver) from the Device Manager, disable test signing (`bcdedit /set testsigning off`), and re-enable Secure Boot (if desired).
