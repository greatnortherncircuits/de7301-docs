USB Driver Installation
+++++++++++++++++++++++

The USB-C port may require drivers to be installed depending on your operating system

Mac
---
Drivers are distributed by Apple and no additional steps are required, it just works.

Windows
-------
On Windows, a specific process is required to install drivers for the DE7301.

#. Download and install `Zadig <https://zadig.akeo.ie/>`_, a utility to install custom drivers
#. Open Zadig.exee, clicking "Yes" to any Windows prompts
#. In the Options Menu, Check "List All Devices"

   .. image:: /img/win_driver/check_all.png

#. In the Options menu, Uncheck "Ignore Hubs or Composite Parents"

   .. image:: /img/win_driver/uncheck_hide.png

#. In the drop down menu locate "Dual RS232-HS (Composite Parent)

   * Do NOT select "Dual RS232-HS (Interface 0)"
   * Do NOT select "Dual RS232-HS (Interface 1)".

#. Verify that The USB ID matches the below picture.

   .. image:: /img/win_driver/composite-parent.png

#. Ensure "WinUSB" is selected above the "Replace Driver" button
#. After double checking the Device USB ID, click "Replace Driver"
#. If you are prompted to confirm replacement of a system driver, click "Yes"

   .. image:: /img/win_driver/click_yes.png

#. Wait for installation to complete. This may take up to 5 minutes

   .. image:: /img/win_driver/installed.png

