User Extensions/API
+++++++++++++++++++

The DE7301 exposes a programming interface that allows people familiar with Javascript to define
new behaviors, buttons, dials and interfaces. This does not require C++ knowledge, and does
not require a compiler or specialized tools to develop for.

The API has 4 major elements:

* :ref:`Controlling<Control>` the radio via Press, Touch, and Turn
* :ref:`Feedback<Feedback>` by examining the pixel data from the radio
* :ref:`Scheduling<Scheduling>` for Async non blocking programming
* :ref:`MIDI<MIDI>` Interface

The extensions are written in `QML <https://doc.qt.io/qt-6/qmlapplications.html>`_ 
(a GUI language) and are given function via Javascript.

Example:
========
An `example <https://github.com/greatnortherncircuits/de7301/blob/release/Shortcuts.qml>`_  User Script extension is provided with a number of basic features. 


Setup
=====
The user extension file loaded is configured in the :doc:`Settings<settings>` dialog.

Once set, to open extensions, open the "View" menu and select "User Scripts".
Each time the User Scripts window is opened, the Script file is reloaded.

Control
=======
Controlling the radio achieved through the IC7300 object.

For example, to press the multi dial:

.. code-block:: javascript

    IC7300.press(FrontPanelButton.FPB_Multi)

To click an item on the screen

.. code-block:: javascript

    var rfPowerOptionLocation = Qt.point(423,13);
    IC7300.touch(rfPowerOptionLocation);

To turn the Multi-Dial left 100 ticks:

.. code-block:: javascript

    IC7300.encoderAdjust(RelativeEncoder.MultiDial, -100);

Feedback
========
In general, feedback from the Main Unit to the Display Unit is limited to the graphics display.
To utilize this feedback, the IC7300 object provides a mechanism to query pixel data.

.. note::

    To determine a pixel location, go to the "File" menu and click "Save Video/Image".
    Then select "Save to PNG", and open the image in a tool like GIMP.
    
In this example, this functions checks if ":" from the clock is visible, to determine if we are at the "home screen" and not in a menu.



.. code-block:: javascript

    function atHomeScreen() {
        var timeDot1Pt = Qt.point(388,6)
        var timeDot2Pt = Qt.point(388,14)

        //Read both pixel values.
        var timeDot1 = IC7300.pixel(timeDot1Pt)
        var timeDot2 = IC7300.pixel(timeDot2Pt)
        //Assumes that if you can see the clock in the top right the home screen is active
        //Currently gives false positive you have the mode select menu up.
        var dotsPresent = Qt.colorEqual(timeDot1, timeDot2) && Qt.colorEqual(timeDot1, "white")
        return dotsPresent
    }
    function exitToHome(atHomeCallback, recurisonLimit = 7) {
        console.log("Exit to home, level " + recurisonLimit)
        if(atHomeScreen()) {
            console.log("Exit to home, at home screen")
            atHomeCallback()
        } else {
            if(recurisonLimit !== 0) {
                IC7300.schedule(33*11,function(time) {
                    IC7300.press(FrontPanelButton.FPB_Exit)
                    exitToHome(atHomeCallback, recurisonLimit-1)
                })
            } else {
                console.log("Failed to get to home screen")
            }
        }
    }


Scheduling
==========
Any Javascript must immediately return (not block). 
In order to wait for the radio to respond to an action, a timed callback is provided. 

.. note::

	The Javascript engine runs within the main application thread. Blocking will cause the application to misbehave.


Combining the previous examples, we will:

#. Exit to the home screen
#. Click the Multi Button
#. 100ms later, touch the "RF Power"
#. 100ms later, turn the "Multi Dial" left 100 clicks
#. 100ms later, turn the "Multi Dial" right 25 clicks

.. code-block:: javascript

	exitToHome(function(){
		//Open Multi Menu
		IC7300.press(FrontPanelButton.FPB_Multi);
		//100ms from now, click the "RF Power" option
		IC7300.schedule(100, function() { IC7300.touch(rfPowerOptionLocation) });
		//300ms from now, turn the "Multi Dial" left by 100 clicks
		IC7300.schedule(300, function() { IC7300.encoderAdjust(RelativeEncoder.MultiDial, -100); })
		//500ms from now, spin the dial right to get to our desired power
		IC7300.schedule(500, function() { IC7300.encoderAdjust(RelativeEncoder.MultiDial, power); })
		//700ms from now, press exit to close the multi menu
		IC7300.schedule(700, function() { IC7300.press(FrontPanelButton.FPB_Exit); });
	}

MIDI
====

The global "MIDI" object exposes functions to use MIDI compliant devices with your radio.
"DJ Controllers" are cheap and useful ways to add physical buttons and knobs.

The API exposes 5 different endpoints for MIDI:

* Bind a MIDI event to a Button
* Bind a MIDI event to a Absolute Dial
* Bind a MIDI event to a Relative Dial
* Ignore a MIDI event, so that unwanted events are not printed to the system log
* Bind a MIDI event to an arbitrary Javascript Callback

.. note::

    To determine MIDI event data, examine the system output log

In the below example, a DJ2GO2 MIDI Controller is bound to a number of functions.

.. code-block:: javascript

    //Bind the "Browse" knob button to pressing the multi button
    MIDI.bindButton(0x9f, 0x06, 0x7f, FrontPanelButton.FPB_Multi);
    //Bind the "Browse" knob to the multi wheel. The wheel seems noisy and shitty tho.
    MIDI.bindRelativeEncoder(0xbf,0x0, RelativeEncoder.MultiDial);

    //Bind "Master Level" dial to volume
    MIDI.bindAbsoluteEncoder(0xbf, 0x0a, AbsoluteEncoder.Volume)

    //Bind "Level 1" dial to to RFSql  b0160200
    MIDI.bindAbsoluteEncoder(0xb0, 0x16, AbsoluteEncoder.RFSql)

    //Bind the "Big Right" dial to the main dail
    MIDI.bindRelativeEncoder(0xb1, 0x06, RelativeEncoder.MainDial);

    //Bind "Side 2, Pad mode" to a macro to dial to a station and set the volume
    MIDI.bind(0x95, 0x01, function() {
        IC7300.encoderSet(AbsoluteEncoder.Volume,0.25);
        directDialAM("83000");
    });
    //Ignore the release for "Side 2, Pad mode"
    MIDI.ignore(0x85, 0x01);

    //Ignore the capactive touch sensors on the knobs
    MIDI.ignore(0x91, 0x06);
    MIDI.ignore(0x81, 0x06);



IC7300 Object Methods
=====================
.. code-block:: c++

    /**
     * @brief Press and release a button on the front panel.
     * @param button The button to press
     * @param duration The duration to hold the button (default 100ms)
     */
    void press(ScriptAPI::FrontPanelButton button, int duration = 100);

    /**
     * @brief touch Touch and release a point on the touchscreen
     * @param p The point to touch
     * @param duration The duration to hold the button (default 100ms)
     */
    void touch(QPoint p, int duration = 100);
    /**
     * @brief encoderAdjust Turn a relative encoder by ticks. Note that if you call this operation "too quickly" encoder steps may be lost or gained.
     * @param e Which encoder to adjust
     * @param ticks How many ticks of the encoder to send
     */
    void encoderAdjust(ScriptAPI::RelativeEncoder e, int ticks);
    /**
     * @brief encoderSet Set an absolute encoder to the percent of it's range of motion. No limitation on how quickly this can be called
     * @param e Which encoder to adjust
     * @param percent What percet to set the encoder to
     */
    void encoderSet(ScriptAPI::AbsoluteEncoder e, double percent);
    /**
     * @brief pixel Sample a pixel color at a given location. Useful for feedback.
     * To determine a location, use File -> Save as PNG to save an image and use a tool (such as GIMP) to determine the coordinates
     * @param p  The point to sample
     * @return  The color at the point
     */
    QColor pixel(QPoint p);
    /**
     * @brief time Get the current time in the scripting engine. Milliseconds since the scripting engine started.
     * @return The current time
     */
    int time();

    /**
     * @brief schedule Run a callback in a number of milliseconds from now
     * @param msDelay How many milliseconds in the future to run. Not an absolute time, but relative to now.
     * Note that this may be rounded up to the minimum time resolution of this API
     *
     * @param callback The callback to run
     */
    void schedule(int msDelay, QJSValue callback);



MIDI Object Methods
===================
.. code-block:: c++

    /**
     * @brief ignore Tell the MIDI engine to ignore this Message. Useful to keep frequent, but unused events out of th elog
     *
     * @param status MIDI Status Byte
     * @param data1 MIDI Data Byte 1
     */
    void ignore(qint8 status, qint8 data1);
    /**
     * @brief bindButton Bind a MIDI message to a front panel button, 1 to 1.
     * @param status MIDI Status Byte
     * @param data1 MIDI Data Byte 1
     * @param data2 MIDI Data Byte 2
     * @param button The button to press
     * @param durationMs The duration to hold the button (Default 100ms)
     */
    void bindButton(qint8 status, qint8 data1, qint8 data2, ScriptAPI::FrontPanelButton button, int durationMs = 100);
    /**
     * @brief bindAbsoluteEncoder Bind a MIDI message to an absolute encoder (Such as Volume or RF Squelch), 1 to 1.
     * @param status MIDI Status Byte
     * @param data1 MIDI Data Byte 1
     * @param encoder The front panel encoder to bind to
     * @param sliderMax If the MIDI device has a range less than 127 to 0, set a new maximum to scale the value by
     */
    void bindAbsoluteEncoder(qint8 status, qint8 data1, ScriptAPI::AbsoluteEncoder encoder, int sliderMax = 0x7f);
    /**
     * @brief bindRelativeEncoder Bind a MIDI message to a relative encoder (Such as Main Dial, Multi, Band Pass Filters).
     *  Assumes the encoder is signeed.
     * @param status MIDI Status Byte
     * @param data1 MIDI Data Byte 1
     * @param encoder The front panel encoder to bind to
     */
    void bindRelativeEncoder(qint8 status, qint8 data1, ScriptAPI::RelativeEncoder encoder);
    /**
     * @brief bind Bind a MIDI message to a Javascript callback for custom logic. The MIDI message is passed to the callback in the form:
     *  function(midiStatus, midiData1, midiData2)
     * @param status MIDI Status Byte
     * @param data1 MIDI Data Byte 1
     * @param callback The callback to run when the MIDI event occurs
     * @param velocityIsSigned If Data Byte 2 is a signed integer, set this to true.
     */
    void bind(qint8 status, qint8 data1, QJSValue callback, bool velocityIsSigned = false);
    /**
     * @brief connectedDeviceName Get the name of the device connected, useful to use different javascript mappings for different devices
     * @return The device name or "None"
     */
    QString connectedDeviceName();