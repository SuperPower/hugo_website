---
weight: 3
title: "Developers Section"
BookToC: true
---

{{< new_button href="https://docs.google.com/document/d/1YMxS8TpRTd-GMkgAA3SWiNsUh405dQrs2kXNGsebAlk/edit#" text="reference gdoc" >}}

# Developers section
In this section you'll find all the documentation for the project and also how you can customize the behavior of the board by implementing your custom functionality.

## Specifications
### Hardware
    
    #TODO describe the main chips of the board

### Software

For the development of the code, FreeRTOS was used because it offers the ability to divide the code into modules (tasks).  With this when new functionality is added, you only need to consider (for the most part) the requirements and timing of the new functionality and FreeRTOS will handle the timing and resources of the other tasks so everything runs as expected.

#### Tasks

    #TODO list tasks

## Debugging
When developing new software, we need to validate the correct behavior of the code. You can do that with an LED connected to your project or lots of prints in the code, but this is slow and tedious and it only gets worse when the bug that you are trying to catch occurs randomly.  The solution to this is a debugging session and every programming language supports this.  In the case of a microcontroller, it requires hardware inside the chip.  You already paid for this functionality, so why would you not be using it?

For the microcontroller used in the SuperPower board, the debugger is a program called GDB.  GDB is the industry standard: name a microcontroller that supports debugging and it's going to support GDB!  It's a terminal program but all manufacturers provide some level of integration with their IDE's so you can use it with a GUI inside your IDE.
With debugging, you can pause the execution of the program at any moment to test the flow and state of the code by examining the call stack and the values of the variables used in the code. Furthermore, you can set breakpoints that stop your code when a certain condition is met; like an error reading a message or an unexpected value of a variable.  This is a very brief explanation of all the capabilities that debugging brings, if you want to learn more about the capabilities of GDB watch this video: 

{{< youtube sCtY--xRUyI >}}

#TODO describe the limitations of the breakpoints and watchpoints
The SuperPower board supports three main methods for debugging; all of them use GDB and those can be categorized in two groups:

 * Traditional debugging where the debugging probe is connected to the machine in which the code is compiled and developed.
 * Remote debugging where a GDB server instance runs on the Raspberry Pi and you can attach to it with the machine in which the code is compiled and developed.

{{< hint info >}} 
Please note: only one debugging method can be used at a time.  Initiating two debugging sessions at the same time will create unexpected results and may possibly cause physical damage to the board!
{{< /hint >}}

### Traditional debugging
This is the most traditional and most reliable way of debugging a microcontroller, for this you can use your favorite programmer (ST-LINK, JLINK etc) and connect it to the SWD connector without any further configuration required (other than the auto generated config in the project for debugging). Still we'll show you how it's supposed to look for the ST-LINK programer in case you have problems.

    #TODO check that this is true for platform.io
    #TODO label on the PCB
    #TODO where to buy the connector
    #TODO add picture of the PCB pads

### Remote debugging
This method allows you to remotely start a debugging session without having the Super Power board physically connected to a computer.  This requires a GDB server running on the Raspberry Pi and for us, openocd will fulfill that role.  This requires the Raspberry Pi to be connected to the same network as your machine in which the code is compiled and developed.
#TODO create image explaining the concept
Out of the box the Super Power board supports two methods for the openocd server:
ST-LINK: this requires the programmer to be connected to the usb port of the Raspberry Pi.
Stand alone: the Raspberry Pi will create the SWD bus with the GPIO pins and it will simulate a programmer.
This requires some steps before you can begin the debugging session:
If remote debugging is used, no programmer must be connected to the SWD connector.
No matter which method is selected, openocd needs to be installed in order to get the remote debugging working.  To install openocd on the Pi use apt-get:
sudo apt-get install openocd
Once openocd is installed in the Raspberry Pi, the SuperPower.cfg file (inside the daemon folder of the repo) needs to be transferred to the Raspberry Pi, there's no need to clone the entire project because this is the only file that we need.
#TODO Do you want to add a statement about using SCP to get the file there and where to put it?
To select either the ST-LINK or the stand alone mode, the SuperPower.cfg file needs to be configured by the user.  Don't worry you only need to change some flags.

#### ST-LINK
This option allows you to debug the code with the ST-LINK connected to the Pi,
Note: this will only work when the ST-LINK is connected to the Raspberry Pi.
#TODO add image describing the setup To enable this mode set the _PROBE variable to STLINK and start the openocd server.
sudo openocd -f SupwerPower.cfg

#### Stand alone
This allows the user to run a debugging session without the need of an ST-LINK. This is an amazing feature because it allows you to flash and debug your code even if you don't have a programmer at hand.  The only drawback that this brings is the required use of 3 pins (2 if you don't need the NRST pin) of the Raspberry Pi.  The pins required for this mode to work are:
GPIO4 <-> nReset 
GPIO17 (Pin 11) <-> SWDIO 
GPIO27 (Pin 13) <->SWCLK
By default when you get the board, those pins are disconnected and they need to be connected.  To achieve this, the solder bridges must be shorted with a soldering iron #TODO list PCB labels #TODO add image To enable this mode set the _PROBE variable to RPI, also by default this will use the NRST pin, but if you don't need that pin connected you can disable it.  Setting the _USE_RST_PIN variable to NO will configure openocd to use the software reset so you'll have the same functionality.  Once the configuration is done, start the openocd server.
sudo openocd -f SupwerPower.cfg

### Thread aware debugging
Thread aware debugging brings even more functionality to the debugging session because it not only provides the same functionality as before, but it also adds thread awareness to the GDB session.  With this feature, GDB can list the stack, status and run time statistics of the FreeRTOS tasks.  With this, you can see why a task is alway in the blocked state, or check if your task is using all the available time and causing the other tasks to starve and never run.
Thread aware debugging is only possible when openocd runs with the GDB server and when compiling in DEBUG mode.
The SuperPower board offers four levels of thread awareness and they are listed from the least expensive to the most expensive:

* Call task: this reports the call stack, the status and the name of each task.
* Average run time: enables the runtime analysis of each task.
* Minimum free stack: gets the minimum amount of RAM available for each task (you can assign different amounts of RAM to each task).
* Everything: this enables all the previous levels (call stack, average run time and minimum free stack).

{{<hint info>}} 
Note: Minimum free stack and average run time require require the Call stack functionality enabled
{{</hint>}}


The Average run time functionality requires the use of a hardware timer, by default the code is configured to use the TIMER 10.

You can enable only the ones that you need, or all of them.  We only allow you to enable this when the debug build is created, the functionality can be enabled in release builds but it's not a good practice to run release builds with debugging enabled, and because of that it's not documented how to do it.

To enable Thread aware debugging the _FREERTOS_DBG variable must be set to YES in the SuperPower.cfg file, this will allow GDB to get the context required for thread aware debugging.

By default, the enabled level is Everything, but you can change this by defining the compiler flags

    SUPERPOWER_CALL_STACK SUPER_POWER_AVERAGE_RUN_TIME SUPERPOWER_MIN_FREE_STACK SUPERPOWER_EVERYTHING

