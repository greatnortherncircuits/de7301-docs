Troubleshooting
==================================

.. note::

   If you are having problems during hardware installation STOP and contact support.
   
Support
+++++++

If you have having issues, contact support. To diagnose the issue, please have a copy of
the log file ready. 


Not Connecting
++++++++++++++

If the DE7301 cannot be found on your network, you will repeatedly the below statements in the 
log file::

	State:  "Query mDNS for IC7300"
	Query network for IC-7300 RDU...
	State:  "mDNS Query Time Out"
	"mDNS query timed out, was at 5097"
	

Steps to resolve are:

#. Ensure the Gigabit ethernet connection is plugged in before applying power via USB-C
#. Ensure the USB-C is plugged in to a high quality compliant USB-C Adapter or PC
#. Reset the USB-C Power to the device
#. Looking through the slats in the bottom case, verify a bright red LED is visible


Mac OS Logs
-----------

#. Open Finder
#. In the "Go" Menu, select "Go to folder"
#. In the Location paste "~/Library/Application Support/RDU3/"
#. Open "logs.txt" in TextEdit

Windows Logs
------------

#. Open a Windows Explorer Window
#. In the address bar, paste "%LocalAppData%\RDU3"
#. Open "logs.txt" in Notepad


Hardware Logs
-------------

Having connected the USB-C to your computer, as described in :doc:`USB <usb>` section, 
click the "Attach to Device Logs", then reboot the device. Information about the device's
boot process will be transfered to the logs.txt.




