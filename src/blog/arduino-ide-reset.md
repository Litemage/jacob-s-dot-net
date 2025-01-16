---
title: "The Arduino 1200 bps Reset"
description: "How the Arduino IDE is capable of easily downloading firmware on virtually any controller on demand."
author: "Jacob Simeone"
pubDate: "2025-01-04"
updatedDate: "2025-01-05"
coverImage: ""
tags: "interesting"
---

# Background

Wether you are a beginner in the world of embedded systems, or a veteran of the 8-bit age, it is hard to miss [Arduino's](https://www.arduino.cc/) impressive toolset. One of Arduino's strengths is its ecosystem of supported products compatible for its [wiring-based api](https://wiring.org.co/reference/libraries/), as well as the third-party support for boards and software compatible with Arduino. The Arduino IDE, being one of those tools, is responsible for building user-written software into files capable of being uploaded to the connected controller. That building and uploading process is one that typically does not get a lot of attention from users, and remains a mystery for some until they graduate to microcontroller-specific frameworks, or bare-metal programming and are faced with the dilemma of putting their newly-constructed software onto a microcontroller.

In short, these responsibilities are part of the [bootloader](https://docs.arduino.cc/retired/hacking/software/Bootloader/), a small piece of software that lives at the starting address of the microcontroller. Upon power on, the controller enters the bootloader, which handles downloading firmware, maybe setting up the controller, and jumping to the memory address which holds the main application. However: in order to download firmware, the user must be able to hold the controller in the bootloader mode, or somehow signal to the controller to stay in the bootloader without jumping to the main application. This is where a need arises for resetting a microcontroller through the Arduino IDE comes up.

> A quick aside: it absolutely possible to program a microcontroller without a bootloader "burned" (programmed on the controller), but this typically requires a special programmer for most manufacturers.

# Reset Over Serial

While other interfaces exist, Arduino-compatible boards typically use either native USB as serial or a ["USB-to-Serial"](https://en.wikipedia.org/wiki/USB-to-serial_adapter) converter to communicate from a host computer to the on-board microcontroller. It is over these interfaces that the firmware is sent, bit-by-bit. As a consequence, in order to download firmware without any hardware interaction it must be possible to throw the connected board into bootloader mode over these interfaces.

For boards using USB-to-serial chips, this is not a problem because the IC that handles the USB-to-Serial conversions never has to restart, keeping the port open. The converter IC is also responsible in these scenarios for resetting the microcontroller and setting the necessary conditions to enter the bootloader. However: when using native USB, the device controlling the USB communication has to reset, disconnecting the USB port. Naturally, this is a problem if the IDE is trying to upload firmware.

I believe this is the problem those that worked on the Arduino core toolset were faced with: how to force any connected board into its bootloader state, regardless of connection interface.

# Arduino's Magic 1200 bps Port Speed

Buried in the [Arduino Platform Specification](https://arduino.github.io/arduino-cli/dev/platform-specification/#1200-bps-bootloader-reset), there is a specification that states that upon a port being opened with a baud rate of 1200 bps, the controller should be restarted into the bootloader and wait for firmware. The specific option listed by Arduino is `use_1200bps_touch`, as well as `wait_for_upload_port`. These two options are used to enter bootloader with a port opened with the magic baud rates, as well as waiting for the port to re-appear. The source for this can actually be found in [Arduino's go-serial-utils library](https://github.com/arduino/go-serial-utils/blob/bfe6b0331ffceaeaa645b3ebf9088fe9c07db82d/reset.go#L30), used by [Arduino-Cli](https://github.com/arduino/arduino-cli/blob/82f37f241bfb2181762edbf36199c7bf7166ecef/commands/service_upload.go#L496) to upload the user's firmware to the board. The overall process is pretty simple:

- Reset board by opening port at 1200bps, then closing it.
- Allow board time to reset and list a new port
- Open new port, and stream firmware, and board should go from there


# Hijacking the Feature

All of the exposition out of the way, the usefulness of this feature can be exposed. Because the behavior beyond entering the bootloader is not really specified by Arduino, and is more or less up to the implementation, I am going to pick the [SAMD21](https://github.com/arduino/ArduinoCore-samd) platform for this particular example. You can find the line in the core that checks for the 1200 bps signal [here](https://github.com/arduino/ArduinoCore-samd/blob/993398cb7a23a4e0f821a73501ae98053773165b/cores/arduino/USB/CDC.cpp#L151), whose call will eventually lead to the [flash being wiped and bootloader entered](https://github.com/arduino/ArduinoCore-samd/blob/993398cb7a23a4e0f821a73501ae98053773165b/cores/arduino/Reset.cpp#L42). 

The beauty of this feature is starting to show: all you have to reset the controller into bootloader is open the port using a baud rate of 1200 bps, which is super easy to do in pretty much any common language.

**C#:**
```c
void Perform1200BpsTouchReset(string portName)
{
    // Assuming port is valid - set up port
    SerialPort port = new(portName);

    port.PortName = portName;
    port.BaudRate = 1200; // "Magic" speed (aparently)
    port.Parity = Parity.None;
    port.DataBits = 8;
    port.StopBits = StopBits.One;
    port.Handshake = Handshake.None;
    port.ReadTimeout = Timeout.Infinite;
    port.WriteTimeout = Timeout.Infinite;

    port.Open();

    // Close the port
    port.Close();

    // Arduino source claims that scanning for ports can, in some cases, 
    // interrupt the reset process, so we'll wait half a second before scanning
    Thread.Sleep(500);
}
```

**Python:**
```python
import serial
from time import sleep

def perform_1200_bps_reset(port: str):
    ser = serial.Serial()
    ser.port = port
    ser.baudrate = 1200
    ser.open()
    ser.close()
    # Wait 1/2 second before scanning later...
    sleep(0.5)
```

This is where it gets platform specific: uploading firmware. Doing a little digging for the SAMD21 platform reveals the [BOSSA](https://github.com/shumatech/BOSSA) utility by [ShumaTech](https://shumatech.com/web/). This is an open-source, and far simpler, programmer for microcontrollers that respond to the [SAM-BA](https://www.microchip.com/en-us/development-tool/SAM-BA-In-system-Programmer) software suite by Microchip.

> Spoiler: The bootloader that is a part of the ArduinoSAMD core implements SAM-BA at least enough to download firmware - although I haven't read into it too much to attest to truly how much of the system it implements.

So why does this matter? Well, with a combination of a program like BOSSA and some custom-written code for your chosen platform, you can upload your own binaries to an Arduino board! Which I would say is pretty cool.