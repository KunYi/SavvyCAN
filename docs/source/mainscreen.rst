Main / Start Up Screen
======================

**SavvyCAN main screen**

.. image:: ./images/MainScreen.png

This screen embodies the core of the program. Here you will find the master list of all frames. Also, here you can navigate to the other aspects
of the program. You can have multiple sub-windows open at once - in fact, quite often this is very beneficial. This screen is also used to 
connect to a GVRET compatible device. But, one thing at a time.


The Main Frame List
====================

The main frame list takes up the majority of the main screen. This list consists of the following sections:

- Timestamp: The timestamp is either in microseconds or seconds. This is a setting in preferences. Either way, the timestamp can have
  microsecond resolution. The difference is just whether there is a decimal point or not. GVRET has the ability to maintain full microsecond
  resolution for timestamping purposes.
- ID: The ID is specified either in hexadecimal or decimal (a preference you can set). This is the message identifier sent over the CAN bus.
- Ext: 0 = Standard message (11 bit ID). 1 = Extended message (29 bit ID)
- Bus: SavvyCAN was meant for use with GVRET compatible capture devices. GVRET supports two buses labeled 0 and 1. The bus a frame came in on
  is specified here. Many file formats do not specify bus and thus all frames will be loaded as bus 0.
- Len: The number of data bytes that were sent with this frame. It can range from 0 to 8.
- Data: All of the data bytes separated by spaces. Can be in either hexadecimal or decimal (preference). If "Interpet Frames" is checked you will
  also see extra data at the end of any frames that have DBC data. To see the rest of this data click upon the frame in the list. It will
  automatically expand to show all signals attached to that frame.


The Bottom Statusbar
====================

At the very bottom of the main screen is a status bar with three sections. 

* The first section shows the connection status. If you are connected to a GVRET compatible dongle you will see the 
  version of the connected device here. 
* The second second section shows which file is currently loaded. This is updated by loading or saving.
* The third section shows whether a DBC file is currently loaded and which one. DBC files are used to interpret messages.


Connecting To A Dongle
======================

SavvyCAN is able to connect to GVRET compatible devices to capture new traffic. These devices will present as serial ports on the connected PC.
To connect to a dongle select the proper serial port and click "Connect to GVRET". If a valid device is found on that serial port the first
statusbar section will update and the currently set canbus speeds will show in the drop down lists below the button. These speeds can then
be changed. Changes to canbus speed will only take effect when you click "Set CANBUS Speeds". The program will let you know if the connection
was not successful.


The Rest of the Main Window
===========================

Below the dongle configuration is an area that shows the total number of captured frames and the frames per second. Total frames might not match
the number of shown frames. If you've deselected any IDs in the filter list then fewer frames will be shown. Frames per second is calculated as
an average and so will wind up or down when there is a sudden change.

Suspend Capturing / Resume Capturing is a button that will temporarily disable frame capture or re-enable it. This can be used to keep everything
connected without capturing traffic for a short time. This can help to not capture traffic in between tests.

The "Normalize Frame Timing" button is used to reset the lowest timestamp to "0" and offset all other timestamps accordingly. This is useful to
remove the starting offset when you start up the GVRET board long before actual traffic starts. SavvyCAN is designed such that this doesn't really matter
most of the time but normalizing the timing might be useful to help correlate the timing between two different captures.

The "Clear Frames" button will erase all captured messages. They will be irreversably erased and all memory will be freed.

The "Auto Scroll Window" checkbox will cause the main frame list to hunt toward the bottom of the list as frames come in. It will normally not be quite
at the very bottom as, for performance reasons, the program runs at quarter second updates to things like the auto scroll. Thus, the main list will be
scrolled to the bottom four times per second.

The "Interpret Frames" checkbox is used to specify whether the loaded DBC file should be used to interpret all available messages and signals. One might want
this off for performance reasons (interpreting takes some extra processor power and RAM) or to declutter the main frame list.

The "Overwrite Mode" checkbox is used to ensure that only the newest frame for each message ID is shown. That is, if 100 messages with ID 0x105 come in you
will see only the newest one. This is generally used along side "Interpret Frames" to interpret frames and always see the up-to-date information.


Loading And Saving Frames
=========================

What CANBus analysis tool would be complete without an easy way to load and save frames? 

SavvyCAN can load and save in several formats:
	- CRTD: This format was made by Mark Webb-Johnson for OVMS (open vehicle monitoring system) and other related tools. It is a reasonably 
	  readable and compact format. GVRET defaults to saving in this format. One reason one might not want to use CRTD is if knowing which bus
	  a frame originated on is important. CRTD does not save that information.
	- GVRET: This is the native format for GVRET and SavvyCAN. One might then ask why CRTD is the default. Well, for compatibility with 
	  OVMS tools. Otherwise, the GVRET format saves more information such as the bus a frame originated on. This format is in CSV 
	  (comma delimited) format and as such can easily be loaded into your favorite spreadsheet program as well.
	- Generic ID/DATA - Another CSV format. This is a very cut down format with limited information.
	- BusMaster - This is the format output by the BusMaster CANBus program. BusMaster is an open source Windows-only somewhat clone 
	  of CANAlyzer (the 800lb gorilla in the analysis space). The ability to load and save in this format makes SavvyCAN fully capable 
	  of swapping data with BusMaster should you need to do so.
	- Microchip - Format output by Microchip CANBus tools. Perhaps you have logs that were 
	  captured with a $100 Microchip dongle? You can load them in SavvyCAN.


Filters
========

You might notice that there are three entries in the file menu that mention filters. SavvyCAN can filter messages so that you only see some 
of the messages coming in on the bus. It still saves all incoming messages but you are able to filter which you will view at any given time. 
SavvyCAN allows for loading and saving the list of frames you'd like to view so that you can easily switch "sets" of frames to view. Also,
when saving you can optionally save just the frames that you have filtered instead of every captured frame. Filters are set in the lower
righthand of the this screen. All IDs are selected by default. To deselect an ID click on the checkbox next to it. You can also deselect
all IDs or select all IDs. These are useful if you only want to view a couple of IDs (click None then the few you need) or you just want
to remove a couple (click All and then deselect the ones you don't care about).



What is DBC and why would I care?!
==================================
	
I'm glad you asked. DBC is a file format used to specify how "signals" are stored in "messages." A message is essentially a unique 
packet of data sent on the CAN bus. Ordinarily this message is differentiated by frame ID. Each ID is a different message (usually).
A signal is a piece of data stored in a message. For instance, ID 0x105 might be a message from the vehicle control unit to the motor
inverter. Within that message bytes 0 and 1 might encode the desired torque. That would be a signal. A DBC file allows these relationships
to be specified and named. It also allows for scaling of values stored in a signal. Additionally, a signal can have values associated with
textual output. For instance, if a signal encodes the current gear then a DBC file can define that a value of 0 means "Park" and a value
of 1 means "Drive". This makes analysis a lot easier since you do not need to remember the mapping yourself. In this way data can be better
understood by users of the program. Also, other windows can use the DBC file for such things as being able to graph a signal without having
to figure out the actual details of that signal.


How DBC interacts with the main screen:
=======================================
	
First of all, one can load and save DBC files from the File menu. Also, one can edit the currently loaded DBC file (or start working on 
one if one is not loaded). It is also possible to save the currently loaded frames but with DBC decoding. This is somewhat like the normal
saving functionality with a two differences: there is only one output format and that format has all signals contained in each message listed
and decoded.
