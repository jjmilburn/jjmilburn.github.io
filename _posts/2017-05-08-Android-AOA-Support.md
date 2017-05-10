---
layout: post
title: Android Open Accessory Mode (AOA) Support in 2017
---

While Bluetooth gets plenty of attention for providing sensor and accessory
connectivity to Android devices, an older protocol introduced at Google I/O
2011 continues to promise widely compatible, no-pairing data wired data
transfer.

-----

During the course of evaluating connectivity options for an Android-connected
accessory in a recent project, Android Open Accessory (AOA) came up as an
option.  This is a little talked about protocol that has been around since API
3.1, and provides 'driverless' support for communicating to an external USB
device which must power the bus (like a host).

'Widespread compatibility' was an important metric for us, which ruled out
USB OTG.  Using OTG/Host mode would reduce the accessory cost and complexity
by removing the need for the accessory to have a battery (since the phone
powers the accessory in OTG mode), but the spotty availability of native,
works-out-of-the-box USB OTG support on low and mid-range handsets eliminated
this as a possibility.

This left the choices of WiFi, BlueTooth, and AOA.  We decided to determine
empirically how widely supported AOA was, and found the results quite
promising.

To investigate further, we purchased the [UMFT311EV](http://www.ftdichip.com/Products/Modules/DevelopmentModules.htm#UMFT311EV) AOA evaluation board from FTDI. This board implements the full AOA
protocol, and FTDI provides a [UART Terminal App](https://play.google.com/store/apps/details?id=com.ftdi.j2xx.hyperterm&hl=en) which, if installed on an Android phone, will send data to a connected AOA
device.

We used this app and evaluation board, and connected to the UART on the board
to attempt bidirectional communication with the phone from a separate computer
via the FTDI311 evaluation board, using AOA to the phone.


### Phones Tested for AOA Support

This is a list of phones which we tested, where we indicate
"AOA 1.0 Bidirectional Data Support" if the phone met the following criteria:

1. The phone immediately detects that an AOA accessory is plugged in, and
prompts a relevant dialog upon insertion of the AOA
2. We are able to receive data at our UART terminal connected to the FTDI
evaluation board from the phone
3. We are able to send data from our UART terminal to the phone through the
FTDI evaluation board
4. We do not have to modify any settings or configuration on the phone, it
'just works'

In the table below, APIs for all phones not supporting AOA are explicitly noted,
as well as a few 'newer' models to indicate the firmware at time of testing.

Manufacturer

| Manufacturer  | Model                    | AOA 1.0 Bidirectional Data Support |
|:-------------:|:------------------------:|:----------------------------------:|
| Alcatel       | 918S (API 2.3.6)         | **No**                             | 
| Alcatel       | 4007D (API 2.3.6)        | **No**                             |
| Alcatel       | 4015D                    | Yes                                |
| Alcatel       | 4028S                    | Yes                                |
| Alcatel       | 4032E Pop C2             | Yes                                |
| Asus          | Zenfone                  | Yes                                |
| Blu           | Dash 3.5 (API 2.3.5)     | **No**                             |
| Blu           | Dash Jr K                | Yes                                |
| (Unknown)     | FS358/WaterWorld67715~   | Yes                                |
| Huawei        | Ideos (API 2.2)          | **No**                             |
| Huawei        | Honor 3x [G750-T00]      | Yes                                |
| Huawei        | Y221-U22                 | Yes                                |
| Huawei        | Y330-U05                 | Yes                                |
| Huawei        | Y336                     | Yes                                |
| Infinix       | x351                     | Yes                                |
| Itel          | it1409                   | Yes                                |
| Jivi          | JSP20 (API 2.3.5)        | **No**                             |
| LG            | E615                     | Yes                                |
| Mi-Fone       | MI-A403                  | Yes                                |
| Google        | Pixel (API 7.1.2)        | Yes                                |
| Motorola      | Moto G4 [Athene Core]    | Yes                                |
| Motorola      | Moto E2                  | Yes                                |
| Plum          | X350                     | Yes                                |
| Prestigio     | 3540 DUO                 | Yes                                |
| Samsung       | Galaxy Tab E SMT560-NU   | Yes                                |
| Samsung       | GT-S5380V (API 2.3.6)    | **No**                             |
| Samsung       | GT-3501L                 | Yes                                |
| Samsung       | JM-110H/DS               | Yes                                |
| Samsung       | SM-361H/DS               | Yes                                |
| Tecno         | P5S                      | Yes                                |
| Tecno         | W4                       | Yes                                |
| Tecno         | Y2                       | Yes                                |
| Tecno         | Y4                       | Yes                                |

~ The "FS358" is not labeled beyond the model, and simply reports an oem of
"waterworld67715"

This is, of course, not comprehensive by any means -- but intentionally skewed
towards handsets that are deployed primarily in the developing world.

Note that all phones which did **not** support AOA are pre API 3.1; which is
expected.  So far, we have no evidence that phones newer than API 3.1 lack
support for AOA.

### Future Support?

It is unclear what the roadmap for AOA support moving forward looks like:

* Is there an AOA 3.0 with new features on the horizon?
* Is 'keeping the status quo' alive the way forward?
or..
* Is AOA going to be unceremoniously chopped out at some point?

As 'feel-good' evidence for ongoing support, it's nice that the Pixel clearly
supports AOA (so, this functionality isn't getting deprecated in today's latest
'flagship' handsets, at least).  It is also nice that all phones we tested with
API >= 3.1 were fully supported.

Straight from the documentation itself, however, there is no guarantee
that a given handset will have AOA support:

> Because not all Android-powered devices are guaranteed to support the USB
> accessory APIs, include a <uses-feature> element that declares that your
> application uses the android.hardware.usb.accessory feature.

[Android Open Accessory Protocol 1.0](https://source.android.com/devices/accessories/aoa)

As for existing uses, a cursory search for commercial products using AOA turned
up automotive head units (car stereos) supporting AOA.  A major online vendor
in the space, Crutchfield, lists a specific section for ["Android Control AOA Car Stereos"](https://www.crutchfield.com/S-Cjr256pIBXL/fg_300_AG_Android_Control%7CFFAOA/Android-Control-AOA-Car-Stereos.html?tp=5684).  This application appears to leverage the 
streaming audio endpoints introduced in [AOA V2](https://source.android.com/devices/accessories/aoa2).
One can imagine wanting to pipe audio from a handset into a car stereo in
higher fidelity than (non-AptX) Bluetooth can provide, but with some form of
audio playback control.  In this, AOA seems to have found a pleasant market
niche.

Will this still be supported on Android handsets being released in 2020?  No
clear answers yet.

An interesting factoid discovered along the way -- connecting to a phone via ADB,
and executing `adb shell ls /system/etc/permissions | grep "usb"` provides
evidence of whether a phone can *potentially* support AOA or USB OTG.  Specifically:

* Presence of `android.hardware.usb.accessory.xml` indicates AOA may be supported
* Presence of `android.hardware.usb.host.xml` indicates USB OTG may be supported

If the relevant XML file is *not* present, the phone will not support the
connection mode of interest.

### Additional Resources

* [Android Open Accessory Protocol 1.0](https://source.android.com/devices/accessories/aoa)
* [Android Open Accessory Protocol 2.0](https://source.android.com/devices/accessories/aoa2)
* ["AptX High Quality Bluetooth Music Audio Codec"](http://www.csr.com/products/aptx)
