# Context
 
*LoRa* is a low-power long-range wireless technology, developed by a French startup, later on bought 
by [Semtech](http://www.semtech.com/), a supplier of analog and mixed-signal semiconductor products. 
 
[Libelium](http://www.libelium.com/) is a Spanish company delivering an open source sensor platform 
for the *Internet of Things*.

Libelium added LoRa support to its offering some months ago. To start playing with LoRa, you simply 
have to use what Libelium names a *Plug & Sense* (mainly a waterproof case integrating a microcontroller 
board, several sensors and a wireless module, a LoRa one in our case) and their *LoRa gateway*, 
a USB stick that you can plug into a PC.

LoRa uses spread spectrum technology, on different channels. This means that the Plug & Sense and the 
gateway can be configured in several different ways, depending on parameters such as channel, 
bandwidth, coding rate and spreading factor. On Plug & Sense side, the developer can use the available 
software library. On gateway side, the developer has to send configuration messages to the USB stick 
using a terminal emulator. This is not very convenient.

# Libelium LoRa Gateway Controller

## Overview

This application implements a simple controller for the Libelium LoRa gateway: it allows to configure
various gateway parameters, and to read those parameters. Additionally, it displays messages
sent to the gateway by the Plug & Sense.

## User interface

The application user interface (UI) provides following elements:

* a serial port device selector
* a button to send **READ** frames
* a button to send **SET** frames
* several elements to define gateway parameters, used by the **SET** frame
* a first scrolling list
* a second scrolling list

More precisely:

* the top combo box allows to select the serial port to be used. Other buttons are active only 
once the serial port is selected. Once selected, it's no more possible to change the serial port.
* the **Send READ command** button sends the **READ** frame to the gateway. The gateway answers 
with its current configuration.
* the **Send SET command** sends the **SET** command to the gateway, with parameters set to 
the values selected on the right-hand side of the window: frequency and channel, address, 
bandwidth, coding rate and spreading factor.
* below those buttons, there are two scrolling lists of messages. The top one displays all frames 
received from the gateway. Those frames can be either answers to commands sent to the gateway, 
or data received from the Plug & Sense. Every message is timestamped.
* the bottom list displays messages generated by the applications. These messages inform the user 
about ongoing operations. In current version, following messages are displayed:
  * at startup time: the list of available serial ports
  * indication of the use of the command buttons
  * frame sent by the use of a command button
  
## Discrepancies

Developing this type of application quite often leads to discovering some discrepancies between 
documentation and reality. In our case, we can list:

* frames received from the Plug & Sense are terminated by an undocumented sequence of two 
characters (*Carriage Return* and *Line Feed*)
* the gateway does not check the CRC of the command message it receives
* if a data frame sent by the Plug & Sense is received by the gateway almost at the same time 
a command is sent to it, format of data received by the PC is incorrect

## Limitations

The Plug & Sense can send two types of frames: *ASCII* frames and *binary* frames. In current version, 
only ASCII frames are supported.

# Installation of source code

## Prerequisites

The Libelium LoRa Gateway Controller uses the [RXTX library](http://rxtx.qbang.org/wiki/index.php/Main_Page) 
to access the serial port. You have to install it in order to be able to run the application. 
For installation procedure, please refer to [this page](http://rxtx.qbang.org/wiki/index.php/Installation),
or see below.

Additionally, the application relies on [JavaFX](https://docs.oracle.com/javase/8/javafx/get-started-tutorial/jfx-architecture.htm#CHDFDAFF). See below.

This repository provides source code files for *Eclipse*. Of course, those files can be used in any other
development environment. But following explanations relate to Eclipse only. Additionally, information is provided 
for a PC running *[Linux Mint](http://www.linuxmint.com)*, a Linux distribution derived from *Ubuntu*.

## Installation of RXTX

- install package `librxtx-java` (version `2.2pre2-11`)

## Installation of JavaFX

- configure Eclipse for JavaFX, as explained [here](http://www.eclipse.org/efxclipse/install.html#for-the-lazy)
- download [Scene Builder](http://docs.oracle.com/javase/8/scene-builder-2/user-guide/index.html) from 
[here](http://gluonhq.com/open-source/scene-builder/). Then, in Eclipse, set the path 
to Scene Builder executable (**Window / Preferences / JavaFX**). This step is optional.

## Import of project

- in Eclipse, switch to the *Git* perspective and clone this Git repository. 

## Configuration of Eclipse for RXTX

You should not have to go through the two steps below, if you imported the project from the
repository as directed above. But it can be useful to know what has to be done for RXTX, in a more
general case:

- add `/usr/share/java/RXTXcomm-2.2pre2.jar` external JAR to the Eclipse project
- right-click on the jar in the list of referenced libraries in the Eclipse project, 
select **Properties / Native Library** and enter the directory containing native libraries: 
`/usr/lib/jni`

# Application build

- create a *Run* configuration. For project, use `LoraGatewayController`. For main class, use
`com.orange.pb.loragatewaycontroller.Main`.
- create a runnable jar, using **File/Export.../Java/Runnable JAR file**

# How to run the application

- add `RXTXcomm.jar` to `CLASSPATH`:

```
export CLASSPATH=$CLASSPATH:/usr/share/java/RXTXcomm.jar
```

- add `librxtxSerial.so` to `LD_LIBRARY_PATH`:

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib/jni/
```

- run the JAR file created above:
```
java -jar <JARFile>
```