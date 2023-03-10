Theory of Operation
+++++++++++++++++++

The DE7301 is essentially a high speed capture card for the IC7300 with control functionality.

..  figure:: img/beta_unit.jpg
    :target: img/beta_unit_large.jpg

    Beta Unit
    
The DE7301 is based around a Lattice ECP5 FPGA, a type of reprogrammable, high speed digital logic chip. 
This FPGA captures the raw display data between the main CPU and the display. It does not alter, 
inspect, or recreate internal system state in the IC7300. 
It also multiplexes the real-time serial data between the display and CPU.

The FPGA has two different domains, a "Gateware" portion and a "Firmware" portion. 

The firmware is responsible for the TCP/TLS1.2 connection to the application. It handles commands
from the Mac/PC application and writes those into registers the gateware operates on. 

The gateware is responsible for all other functions and operates in a real time manner. Once
configured by the CPU, the gateware runs independently of the CPU, with the exception of 
multiple layers of watchdogs to ensure correct operation.

The gateware also implements a soft-core RISC-V processor. It has fairly limited resources.


Display Capture
---------------

The IC7300 has a display resolution of 272 lines by 480 columns at 60 frames per second. 
The DE7301 is designed to capture this entire data stream and send it onto the Gigabit Ethernet
interface with minimal latency.


Physical Interface
------------------

The CPU and display are connected via a 40 pin, flat flex cable. The DE7301 is plugged in-between
this interface. This allows for a solder-less installation. 

The DE7301 is held in place inside
the radio via a 3d printed bracket made of PETG, a polymer that is heat resistant and creep
resistant. 

Radio Controls
--------------

The CPU and Display in the IC7300 communicate over a serial (UART) link. Button presses are
generated by the display board and communicated as serial commands over this link. 

When the DE7301 is powered on, the FPGA switched in-between this connection. This allows
the FPGA to monitor, alter and inject real time data into the serial stream. 
This introduces a 0.0005 Second delay (~500 Microseconds) in the command, which is0 not 
perceptible to humans.

For example, if the FPGA sees that the main VFO knob is turned, it intercepts this command,
and re-writes it to sum the total movement of the virtual knob and the physical knob, allowing
both to be used. For other knobs, such as volume, the behavior is much simpler as we can 
simply inject a volume knob command and do not have to alter the one from the display.

When the DE7301 is unpowered (but the IC7300 is powered), the FPGA is switched out of the 
serial connection, allowing the radio to operate normally. 

If the DE7301 is unplugged while the IC7300 is actively operating, the IC7300 can get confused and
stop responding to user inputs. A power cycle resets the IC7300 state.

The Radio Power button is an exception, and has a dedicated line on the flex and does not use the
serial link.


HDMI Out/Semiconductor Shortage
-------------------------------

To complain for a moment, the global semiconductor shortage has been a very bad time to launch 
a product. 

The design is based around a FPGA "System on Module" (SOM), as these have inventory
produced pre-pandemic that could be purchased.

Unfortunately, this SOM has a fairly slow memory. As a result, the gateware operates at line rate,
meaning that it only buffers a single 480 pixel line (to high speed, in FPGA memory) 
before sending it to the network. 

An HDMI-out is predicated on the ability to have a high speed DRAM that contains an entire
framebuffer (or two) that could also support rescaling the source pixels. This is not feasible
with the current FPGA SOM.

If the DE7301 is succesful, and the semiconductor shortage abates, the planned "DE7302"
will have a faster DRAM allowing an HDMI out. The DE7302 isn't guaranteed, and the DE7301 may 
be the only product released.


