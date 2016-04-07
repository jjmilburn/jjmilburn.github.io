---
layout: post
title: Sharing a Prolific USB->UART Converter with VirtualBox
---

While creating a UART protocol for use in a new product, it
became useful to connect a serial adapter from my host PC to the Vagrant
environment which held the protocol code and its dependencies.  This approach
should work regardless of the UART converter model/brand (Prolific, FTDI, etc)
assuming that plugging the device in creates a `/dev/ttyUSB[0-9]` entry.  This
guide is written for a *nix host platform (Ubuntu).

-----

### First Things First

The adapter which was used to test this approach is similar to the
[USB to TTL Serial Cable](https://www.adafruit.com/products/954) available on Adafruit; but any similar adapter should work.

### Step-By-Step

1. Plug in the USB->Serial adapter, and ensure that it shows up under `lsusb`, as well as creating a device entry similar to `/dev/ttyUSB*` .

2.  Once the adapter is plugged in, execute `chmod 666 /dev/ttyUSB0` (substituting ttyUSB0 for whatever USB* port the adapter enumerated to) on the host machine.  Determine the adapter by observing what new entry is added to the output 
of `ls /dev/ttyUSB*` when the adapter is plugged into the host machine.

Alternately, ensure that the user running the VirtualBox on the host machine
is in the `dialout` group (`sudo adduser $USER dialout`).  This requires a
logut/login cycle to take effect.

3. In the VM VirtualBox Manager GUI, go to "Settings" for the machine which
will receive access to the serial port, and navigate to the "Port 1" tab under
"Serial Ports". Set "Port Number" to "COM4" (corresponding to a guest VM serial
port of `/dev/ttyS3`), leave IRQ and I/O port as their defaults, select
"Host Device" as the Port Mode, and type the path to the serial port device
on the *Host* machine under "Port/File Path".  In this example, this is
`/dev/ttyUSB0`.

[Screenshot here]

4. On the guest `sudo adduser $USER dialout` will add the guest user to a group
which has access to the serial port interfaces (e.g. /dev/ttyS*).  Execute this command, then log out and back into the guest machine.

5. Ensure the TX/RX lines are connected together on the serial adapter for
the next step (a loopback test from the guest VM).

6. Log into two separate terminal sessions on the guest VM, in the first
session, type `cat /dev/ttyS3`.  In the second terminal session, type
`echo "test" > /dev/ttyS3`.  If the serial device from the host is being
successfully passed through to the guest, "test" should appear in the window
where `cat` is running.

At this point, the guest VM has access to read and write to a USB serial adapter
connected to the host machine.  Note that this approach does not require the
guest VM to have any direct USB peripheral access to the host.

## Asides

* [`PySerial`](https://pypi.python.org/pypi/pyserial) is an extremely useful library for abstracting away most of the
complexities involved with programmatically accessing a serial port for UART
communications.

* If also occasionally using the serial port outside of the virtual machine,
note that if the virtual machine is currently accessing the serial port (e.g.
the host is passing the serial port directly through to the VM) there will be
problems attempting to use it on the host at the same time.

* This approach might not be suitable for near-realtime response requirements
on the serial lines.

