FAQ
===

* Does this have an HDMI Out? Does this require a computer?

  * No HDMI Out. A Mac or PC computer required.
  * Design adaptations due to semiconductor shortage forced HDMI out to be dropped from the DE7301.
  * See :doc:`Theory Of Operation <operation>` for more information.

* Does this add a CVI port? Does it use a CVI port?

  * No, this is not an additional CVI Port. It does not require a CVI port
  * Communication between the Front Panel and the Mainboard in the IC7300 is a proprietary protocol that is not CVI.
  * The virtual front panel re-implements this proprietary protocol
  
* How does this interact with HRD, Hamlib, wfview, win4icom, etc?

  * The DE7301 do not conflict or use the same resources as other control software.
  * There are currently no integrations with other software packages. 
  * If you've got a use case or are an application developer, feel free to open a `Github issue <https://github.com/greatnortherncircuits/de7301/issues>`_ to discuss integration opportunities. 
  
* Can this be used over-the-internet?

  * Yes, see :doc:`Proxy Mode. <remote>`
  * A local Mac/PC is required at the radio

* How is this secured on my network?

  * All virtual events (touch, keypress, etc) are sent over an industry standard TLSv1.2 connection
  * Screen data is encrypted while in Proxy Mode, and not encrypted in Local LAN mode.
  * Access control and credentials can only be reset utilizing the USB-C interface.
  * See :doc:`Security <security>`

* Does this require an always-on internet connection? When does this use the internet?

  * No, the product will function locally without an internet connection.
  * The DE7301 Application will automatically connect to the internet:
 
    * To check for updates automatically, once per day when being used
    * If a fatal application crash is encountered, a crash report will be uploaded.
  * If running in Proxy Mode, incoming connections from the internet are processed.

* What firmware revisions are supported?

  * v1.30 and v1.41
  
* How is this different from Icom's RS-BA?

  * The DE7301 does not in any way use the USB sound card or CVI ports to function
  * The DE7301 captures pixel perfect output from the display, not a facsimile 
  * The DE7301 is not limited by the 47Khz soundcard interface, the waterfall can run at full bandwidth.
  
* What does "limited production" mean?

  * A limited quantity of DE7301s will be produced
  * Sourcing the required semiconductors is challenging in this market
  * The design was adapted to semiconductors available 
  * Design adaptations has elements that do not allow for automated assembly
  * Due to parts and labor cost, the product price is higher than desired
  * Being more expensive and harder to produce, the DE7301 design isn't feasible for mass production.
  * A mass production variant is planned (DE7302) but currently no timeline and not guaranteed.
  
* Open Source Software

  * A number of OSS components make this possible:
     * `ecpprog <https://github.com/gregdavill/ecpprog>`_ by Greg Davill, `forked ecpprog <https://github.com/greatnortherncircuits/ecpprog>`_
     * `QMdnsEngine <https://github.com/nitroshare/qmdnsengine>`_, `forked QMdnsEngine <https://github.com/greatnortherncircuits/qmdnsengine>`_
     * `Qt GUI Framework <https://www.qt.io/>`_  
     * `libftdi1 <https://www.intra2net.com/en/developer/libftdi/index.php>`_
     * `portmidi <https://github.com/PortMidi/portmidi>`_.
     * `mbedtls <https://github.com/Mbed-TLS/mbedtls>`_.
     * `ffmpeg/libav <https://ffmpeg.org/>`_.