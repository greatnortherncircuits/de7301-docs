Save to MP4
===========

The application can save the video stream to an MP4 file utilizing the h264 codec.
This is useful when wanting to use the video in an external video editor, or to record a QSO.

.. note::

   Saving audio is a `planned enhancement. <https://github.com/greatnortherncircuits/de7301/issues/16>`_. At the time of writing, audio capture is not supported.

.. image:: /img/save_video/mac_ui_default.png

Usage
+++++

#. To save to an MP4, go to the "File" menu and click "Save Video/Image ".
#. Select the output directory by clicking the button next to "Output Directory".
#. Optionally, change the :ref:`Encoder Profile<Encoder Profiles>`. Recommended is *H264_YUV420_CRF17*.
#. Click Start Recording
#. In the "Encoder Status", "State" should be "ACTIVE" if the radio is on, indicating active recording to a file.

   * Recording will continue until you click "Stop Recording", you quit the application, or the DE7301 is reset.
   * Clicking "Close" in the dialog will NOT stop the recording.
   * While recording, the option to change framerate is disabled.


.. image:: /img/save_video/mac_ui_recording.png


Encoder Profiles
+++++++++++++++++

For all normal purposes, **H264_YUV420_CRF17** should be used.
This is nearly visually lossless, and has wide compatibility with players and editors. 


.. note::

	There is no truly lossless H264.
	If you have an application with a lossless RGB16 format and use case, 
	`open a github issue <https://github.com/greatnortherncircuits/de7301/issues/new>`_ describing it. 


Other profiles are provided:

.. csv-table:: Encoder Profiles
   :file: /save_mp4_table.csv
   :header-rows: 1