---
layout: post
title: Importing Atollic C Projects to Eclipse
excerpt: <p>In the course of working with Cortex M0 STM32 microcontrollers, it became necessary to modify and build firmware without having direct access to Atollic TrueSTUDIO.  This post outlines the steps taken to successfully build code that performs the same function, and fits in (approximately) the same footprint, using Eclipse CDT.</p>
---

### Eclipse CDT and Cortex M0

1) Get [Eclipse CDT](http://www.eclipse.org/cdt/); this is an Eclipse IDE tailored to C/C++ development.  AFter installing Eclipse CDT, go to "Help", and "Install New Software".  Add http://gnuarmeclipse.sourceforge.net/updates to the "Work with" path, and select the available update.  Install this, and restart Eclipse.  **Tested on Kepler**.

2) Ignore everything you read about CodeLite/CodeSourcery.  You want a cross-ARM toolchain that supports newlib-nano, for the size-optimized nano libraries.  Specifically, you want "GNU Tools for Embedded Processors (gcc-arm-embedded); download the latest precompiled toolchain (or source, if you are feeling masochistic today) from [LaunchPad](https://launchpad.net/gcc-arm-embedded).  It's maintained by ARM employees so hopefully it isn't broken.  I've tested a working binary created on release [2014-q2](https://launchpad.net/gcc-arm-embedded/+milestone/4.8-2014-q2-update)

*[Embedded Toolchain Diagram](http://avr-eclipse.sourceforge.net/user%20manual/concepts/toolchain.html)*

3) Get a copy of whatever project you've successfully developed in Atollic, that you wish to port to this toolchain.

4) Launch Eclipse CDT, and go to "File->Import" to import this project into the workspace.

5) Right click on the project name, and go to "Properties"
- Click on "C/C++ Build", and note the "Configuration" at the top of the dialog, this is the name of this configuration to which these settings are saved.  It is likely "Default [Active]"
- Under "C/C++ Build" on the "Builder Settings" tab, set the Builder Type to "Internal Builder"

6) Under the "C/C++ Build" category, click on "Settings", then "Toolchains".  Ensure that the name of the toolchain contains a mention of "arm-none-eabi-gcc"

7) Click the "Use Global Toolchain Path", and navigate to the /bin folder of the toolchain code downloaded and extracted in  step 2.  For example, "/home/josh/Development/gcc-arm-none-eabi-4_8-2014q2/bin".

8) Under "Settings" -> "Tool Settings", set the following:

**Target Processor**

* ARM family to "cortex-m0"
* Instruction set to "Thumb"

**Optimization**

* Optimize level "-Os" (optimize size)
* Enable "Function Sections" (-ffunction-sections)
* Enable "Data Sections" (-fdata-sections) 

**Warnings**

* Set "Enable all common warnings (-Wall)"

**Debugging**

* Set Debug Level to "None"

**Cross ARM GNU Assembler**

* Under "Preprocessor", enable "Use Preprocessor" and "Do not search system directories (-nostdinc)"
* Ensure that nothing is listed under the "Includes" path, "Warnings" flags, or "Miscellaneous" flags here

**Cross ARM C Compiler**

* Under "Preprocessor", add any symbols that are necessary for your project from Atollic
* Add library paths to the processor-specific header files for the processor which you are building.  In the case of the Cortex-MO, this is likely three folders, with names like "/CMSIS/Device/ST/STM32F0xx/Include", "CMSIS/Include", and "STM32F0xx_StdPeriph_Driver/inc"
* Add library paths to your own code header files as well
* Under "Optimization", set the toolchain language to GNU ISO C90

**Cross ARM C Linker**

* Under "General", enable "Remove unused sections (-Xlinker -gc-sections)"
* Under "General", add the script file (which will be executed with "-T") for your processor.  For the STM32, use "stm32_flash.ld"
* Under "Other Linker Flags", add the following:

```
-Wl,--start-group -lc -lm -Wl,--end-group -static -Wl,-cref,-u,Reset_Handler
```

**Post Build Commands**

The above commands will allow Eclipse to generate an .elf file.  To generate a bin or hex, use whatever tool you find most preferable.

*Disabling Debug Symbols*

- The BlinkyLED project is probably too large (flash overflow) when targetted against an F4.  Follow the steps here to disable debug symbols:
http://wiki.wxwidgets.org/Eclipse,_CDT_%26_MingW_%26_MSYS_Setup_Guide#Changing_the_Build_Configuration

-----

**References**
[Operators and C Syntax](http://www.tutorialspoint.com/cprogramming/c_operators.htm)
