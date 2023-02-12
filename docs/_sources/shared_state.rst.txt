+++++++++++++++++++++++++++++++++++++++++++++
Sharing the Front Panel
+++++++++++++++++++++++++++++++++++++++++++++

The DE7301 maintains the original functions of all of the physical controls on the radio. It virtually duplicates these
and allows simultaneous use of both physical and virtual functions, with a few caveats.


Using both physical and virtual buttons
+++++++++++++++++++++++++++++++++++++++
Both physical and virtual buttons work simultaneously, with two exceptions. `(Issue tracker link) <https://github.com/greatnortherncircuits/de7301/issues/4>`_


* If the same button is pressed simultaneously on both the virtual unit and the physical unit, the IC7300 may stop responding.
* If the Mac/PC application crashes while a button is held, that button may be held forever (with the exception of the power button)

In both of these situations, the solution is to reboot the IC7300.


Using both physical and virtual dials
+++++++++++++++++++++++++++++++++++++

To achieve both physical and virtual dials working together, the DE7301 'mixes' the relative encoders, and multiplexes the absolute encoders.

For example:

* **VFO**: This is a relative dial. Both physical and virtual dials can be used simultaneously and operate smoothly as you would expect.

  * The **Multi Dial** is a relative dial.
  * Under the hood, whenever a virtual or physical dial is moved, the magnitude of the movement is saved and then
    the initial command is discarded. Next, the DE7301 calculates the sum of both dials and transmits that to the Main Unit.

* **Volume**: This is an absolute dial. In this case, whichever dial was used last (physical or virtual) is the value sent to the radio.

  * **RF/Sql**, **Bandpass inner dial**, and **Bandpass outer ring** are absolute dials.

* A turn of a virtual dials does not result in the physical dial moving
* A turn of a physical dial does not result in a virtual dial moving

Power States and interactions
+++++++++++++++++++++++++++++

The DE7301 and the IC7300 utilize separate power supplies and work independent of each other.
The intended usage of the DE7301 is to leave it always powered via USB-C. This will allow the DE7301 to properly
multiplex the IC7300's physical controls with the virtual ones.


However, any combination of powered, unpowered, on, or off of the two are valid and will not cause hardware damage.
In some scenarios, the IC7300 and DE7301 could become de-synchronized.
This can cause "jumps" in dials (Such as the VFO) or may lock up the IC7300. Known problematic scenarios are:

* The USB-C is powered off while the radio is on, dials de-synchronized.
* The USB-C is powered on while the radio is on, dials de-synchronized.
* The USB-C is powered off while actively turning a dials or pressing a button, the IC7300 may lock up and require a power cycle to recover.

Non problematic scenarios are:

* The USB-C is powered off while the radio is off
* The USB-C is powered on while the radio is off