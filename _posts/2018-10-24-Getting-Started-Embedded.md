---
layout: post
title: An Intro - IoT Hardware Platforms
---

Selecting an interface to the physical world

-----

For someone new to embedded IoT development, the growing selection of development
platforms can be overwhelming. Here, we'll explore some of the most popular hardware
kits that let you easily build an interface to the real world.

If you're new to embedded engineering, this post seeks to provide you with a better
sense of 'what matters' when exploring a new development platform. Hopefully, you
walk away with a few tools to help filter through the vast selection of hardware
available to anyone starting a connected device project in 2018. While some of
the platforms discussed fall solidly outside of the typical 'embedded' definition
(The Raspberry Pi, for instance, is in no way a [Class 0/1/2 resource constrained
node](https://tools.ietf.org/html/rfc7228)), they're worth including to
compare the tradeoffs between a more 'full featured' platform and a typical
'embedded' processor.

## IoT Hardware Platforms

When performing a 'real-world' downselection process, there's typically a detailed
comparison of the peripherals and features of each chip, including questions like:

* How many UART/SPI/I2C peripherals does this chip have?
* How many independent timer peripherals?
* How long do flash writes and erases take, and do they block the processor?
* Will your program fit in the available flash, with room to spare?
* Is DMA or another CPU-independent data transfer option possible?
* What's the ADC resolution?
* Is the processor serialized from the factory, and can we access the ID if so?

Then, the answers to these questions map back to project requirements - if
three separate high-resolution timers need to be running simultaneously,
that rules out a few otherwise solid options, for example.

However, its useful to filter with less specific criteria at first, to quickly eliminate any solutions that won't work for a more fundamental reason (e.g. you're planning to run the system from AA batteries, but the hardware draws 50mA+ when in idle mode).

Here, we'll make the following notes about each platform, intended to answer some high level questions that could make or break a project:

* Languages Supported
    * If you can't use it, you won't use it
* Power Consumption
    * Ballpark 'system idle' figure under in a default, non-optimized configuration
* Real-time support
    * Defined here as <10 microsecond responses times to interrupts, with <5 microsecond jitter
* General Purpose IO (GPIO)
    * Does it have any built-in pins for driving LEDs, or reading switches?
* Connectivity
    * NFC, Bluetooth, LoRA, LTE-M1, Zigbee, etc?

### [Raspberry Pi 3 Model B+](https://www.raspberrypi.org/products/raspberry-pi-3-model-b-plus/)

If it possible to use a Raspberry Pi to build your device, even if its just an
early 'works-like' prototype, consider doing so. The development environment
is familar to most (runs Linux), and its fairly powerful - [image classification tasks](https://www.svds.com/tensorflow-image-recognition-raspberry-pi/) are well within its capabilities.
to plug-and-play (for the most part) USB devices gives some great peripheral
flexibility. Built in Wifi, Ethernet, and Bluetooth give you solid options for
connectivity out of the box.

In this performance class, also take a look at the [Beaglebone PocketBeagle](https://beagleboard.org/pocket),
billed as a "$25 Dollar Linux Computer".

Hardware cost, power consumption, or the need for realtime processing would
be sensible reasons to move from Pi to a different platform.

Note: For easy cellular connectivity 'out of the box' with a Raspberry Pi,
consider the [Hologram Nova](https://hologram.io/nova/). It has 2G, 3G, 
LTE-M, and NB-IoT options, providing cellular connectivity to a device
anywhere as long as coverage exists. Hologram provides everything necessary
to get connected, including the SIM card.

* Languages Supported - Runs Linux, fairly flexible
* Power Consumption - [Hundreds of milliamps](https://raspberrypi.stackexchange.com/questions/63519/power-consumption-of-pi-zero-w#63535)
* Real-time support - Not out of the box, you can get a little closer with the [`PREEMPT_RT`](https://emlid.com/raspberry-pi-real-time-kernel/) kernel
* GPIO - 5V and 3.3V [onboard](https://www.raspberrypi.org/documentation/usage/gpio/)
* Connectivity - Bluetooth + Bluetooth LE (V4.2), WiFi, and Ethernet 

### [Arduino Mkr Wifi 1010](https://store.arduino.cc/usa/arduino-mkr-wifi-1010)

A household name in the maker community, Arduino has a large community of
active users, providing a large variety of libraries and [examples](https://create.arduino.cc/projecthub/AppsByDavideV/iot-telegram-bot-with-arduino-mkr-wifi-1010-b835a4) to build
new projects upon. The inclusion of a WiFi module and battery-charging circuit
reduce parts count when using the device in a battery powered (or intermittently
powered) installation. 

Besides the built-in WiFi chip, an interesting feature of the Mkr Wifi 1010 is the
inclusion of the [ECC508](https://www.microchip.com/wwwproducts/en/ATECC508A?tab=documents)
"CryptoAuthenticator" chip. This line of chips deserves its own post, but it
handles Elliptic Curve Diffie Hellman key exchange onboard, a key component
of modern asymmetric cryptography in connected systems. I'm (pleasantly)
surprised to see them on entry-level development kits, as my past experience
has been limited to evaluating them for 'production scale' security-sensitive
projects. Here, it appears to be used for WiFi security, and might not be
accessible for user application code.

* Languages Supported - ["Wiring", Arduino Programming Language](https://www.arduino.cc/reference/en/)
* Power Consumption - Not specified, but tens of milliamps should be easy
* Real-time support - tens of microseconds achievable
* GPIO - 3.3V only (although requiring a 5V supply, this model has no 5V IO)
* Connectivity - WiFi, other options avaiable via many, many shields

Note: If you're looking for WiFi connectivity in a slightly more barebones package,
consider a standalone [ESP8266](https://www.sparkfun.com/products/13678). This is a
basic system-on-chip which can run a simple program on the same chip that provides
WiFi connectivity, in a relatively small package.

### [Particle Mesh - Argon](https://www.particle.io/mesh)

Particle makes not only the hardware, but supplies a 'whole package' IoT
solution, including a backend platform with [Developer Tools](https://www.particle.io/developer-tools/).
If you're going to deploy a fleet of devices that need to be
remotely monitored and updated, having a drop-in solution can save
significant development time.

I've highlighted the Particle Mesh solution in particular as a great
example of the 802.15.4 networking capability of the Nordic
[nRF52840 chip](https://www.nordicsemi.com/eng/Products/nRF52840)
which it is built around. Each mesh is expected to support at least
[10-20 devices](https://community.particle.io/t/particle-mesh-faqs/39435),
giving plenty of room for expansion. If you're looking to build
a mesh of locally connected devices, and want to focus on the application
rather than the mesh, this is worth a look.

* Languages Supported - Wiring (same as Arduino)
* Power Consumption - Not yet specified, but expect tens of microamps in sleep
* Real-time Support - Around [1900 ns](https://community.particle.io/t/photon-interrupt-latency/18655/7) interrupt latency possible on previous devices, expect similar here
* GPIO - 20 mixed-signal GPIO, including analog
* Connectivity - 802.15.4 mesh between devices, WiFi, NFC-A, and Bluetooth LE

### [STM Evaluation Boards](https://www.st.com/en/evaluation-tools/stm32-mcu-eval-tools.html?querycriteria=productId=SS1532)

STMicroelectronics makes a fairly popular line of Cortex-M based 32-bit microcontrollers,
which are included here as a well-supported, 'bare metal' option. Unlike a platform
like Particle, you won't get a fully featured 'IoT solution' out of the box. Low
power consumption, a reliable supply chain (and cost that scales well with quantity),
and good support from ST are solid benefits, however.

The speed of writing code that interacts directly with the registers on the MCU also
means that interrupt latencies will be far lower than the options mentioned above.

* Languages Supported - C (although, see "MicroPython" in the next section)
* Power Consumption - Idling in the microamp range (varies on specific family)
* Real-time support - Faster than other options mentioned above, as you're writing 'bare metal'
* GPIO - Varies, but expect 3.3V on almost all options
* Connectivity - Varies depending on the board

### Bonus - [MicroPython](https://micropython.org/)

Not a hardware platform, but an interesting project seeking to make
Python usable on lower-end hardware. The reference platform is a Cortex M4,
which puts it in the same category as the the Particle Mesh Argon mentioned
earlier. Response time can be [under 10 microseconds](https://micropython.org/https://micropython.org/),
which is worse than possible with a bare-metal board (like an STMicroelectronics MCU), but
far better than a Raspberry Pi.

Also worth mentioning in the 'Not-C' embedded languages category:

* [Rust](https://github.com/rust-embedded)
* [Ada](https://www.electronicdesign.com/blog/running-ada-2012-cortex-m4)

## Backend Platforms

The basic concepts behind an IoT platform are easy to conceive. Simply
send data from the device to a server, toss it in a database, and you're done.
But, for a scalable deployment, you'll need to worry about security (both for
data at rest, and during transmission between the server and the devices), authenticating devices
(how do you prevent someone from cloning a device, and gaining write access to your
backend?), providing device diagnostics, and pushing firmware updates to
your fleet of devices.

If you are evaluating a backend to log data from (and push commands to) one
or more connected devices, consider the following:

* Are there examples for connecting *your particular device* to this service?
* Are there built-in tools for your prototyping needs (e.g. data visualization)?
* Is the platform priced on number of devices, data consumed, API calls, or user 'per-seat'?
* Can you easily get data 'out' of the platform, for analysis using other tools?
* How is communication between devices and the backend secured?

## Great "Getting Started in Embedded" Resources

* [5 Steps to Getting Started with Embedded Programming](https://spin.atomicobject.com/2015/08/19/learn-embedded-systems-programming/) - "Embedded development is such a weird and diverse thing that it’s almost like asking, “How do I get started with programming?” except in an alternate universe where 128k is still a lot of RAM. I’m not sure where to even begin." - If you're interested in Rust on Embedded, check out some of the other Atomic Object blog content as well.

* [State Machines for Event-Driven Systems](https://barrgroup.com/Embedded-Systems/How-To/State-Machines-Event-Driven-Systems) - The concept of a state machine is elemental to many embedded projects, this article walks through using the current system *context/state* and *input event* to determine the next state in detail

* [Ganssle Group](http://www.ganssle.com/item/becoming-an-embedded-developer.htm) - Subscribe to the newsletter, check out the articles, and learn from an expert in the field

* [Making Embedded Systems](http://shop.oreilly.com/product/0636920017776.do) - One of the first books I recommend for new embedded device developers. It assumes some programming background, and dives into 'whats different' when working at a lower level. 

* [Guide to Selecting a Bluetooth and BLE Chipset](https://www.argenox.com/bluetooth-low-energy-ble-v4-0-development/library/a-guide-to-selecting-a-bluetooth-chipset/) - If you're planning on evaluating a Bluetooth solution, this provides a relevant feature matrix for choosing between Bluetoth Classic and BLE.
