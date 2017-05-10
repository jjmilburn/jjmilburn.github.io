<In progress>

---
layout: post
title: Embedded Code Quality -- Beyond `-Wall`
---

While `gcc` can be used to find a number of possible problems using the `-Wall`
and `-Werror` flags, additional linting and analysis tools can be used to
catch addiitonal issues that are not noticed by gcc.  This post discusses
a few options, and shows the general approach used to set up 
the general approach used to set up `splint` to lint files in an embedded C
project using `gcc` as the compiler.

-----

### Rationale

We have an embedded C project, consisting of a number of different modules, and
use `gcc` and `check` to compile the project and run unit tests against each
module.  This is a fine approach, and we even get some additional coverage
during the `gcc` compile process by using additional flags.  The flags we
pass to `gcc` specifically in this build-for-unit-test process 
(including a custom one you can ignore, `-DANGAZA_USE_DEFAULT_ASSERT`), are:

```
CFLAGS=\                                                                        
    -Isrc/ -O0 -g -Wall -Wextra -Wno-unused-parameter -Wundef -Wuninitialized\  
        -Winit-self -Wpedantic -Werror -Winline -std=c11 -DANGAZA_USE_DEFAULT_ASSERT -coverage
```

`-Wall` is a very valuable flag for knocking out potential bugs -- if you're
able to compile your code using the `-Wall` flag and see no warnings, its
a strong positive indicator for code quality.  In my daily work, we go a step
further-- the `-Werror` flag is used to turn these warnings into errors, and
prevent this questionable code from being checked into master.

However, we can do more.  `splint` is one such tool, and Barr Group has an
excellent [article](http://www.barrgroup.com/Embedded-Systems/How-To/Lint-Static-Analysis-Tool) on where to use lint in a design process (after the code is 
compilable), and I won't repeat that discussion here (go read it, though).
Personally, I've found `splint` to be a bit heavy-handed with our codebase,
and the amount of work required to support it outweighed the potential issues
it found.  It's worth a try, though -- if you find its manageable, use it.

relevant link: https://coelhorjc.wordpress.com/2015/02/19/how-to-do-static-code-analysis-in-cc-using-sparse-splint-cpplint-and-clang/comment-page-1/

### OS and Install

If using a 'standard' Unix based OS (e.g. Ubuntu), its possible that`splint`
is available using the usual `apt-get install splint` approach.  If not, you
can find the source and install instructions [on the splint website](http://www.splint.org/source.html).



Rationale

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

![VirtualBox Serial Port Settings](/assets/images/Vbox_Serial_Port.png)

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

