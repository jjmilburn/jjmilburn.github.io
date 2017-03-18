---
layout: post
title: The Basics - Certifying a Cellular Product in the US
---

Even if your product integrates a cellular module with an FCC certification, it
does not mean that the integrated product is fine to sell on the open market.
This post pulls together estimates from various sources to gain a rough estimate
of total cost for RF-related certification on a product using a 'certified'
cellular module.

-----

First, lets be clear -- I'm not a legal expert on cellular certifications, and
the information in this post is meant to be informative, not a complete guide
to what you need to cerify a GSM product in the US.  With that in mind, lets
proceed to explore this topic.

If you intend to sell a product with radio communication in the EU, [R&TTE](http://www.tuv.com/en/usa/services_usa/product_testing/telecom_it/rtte_directive/rtte_directive.html) compliance will likely be required.  GSM/M2M modules can be [individually certified as compliant with R&TTE](http://www.tuv-sud.co.uk/uploads/images/1365171464129234960088/uk-mkg-guide-to-wireless-m2m-module-integration-en-uk.pdf), which reduces the testing burden.  A number of third party test facilities (TUV, 7-Layers, IB Lenhardt, etc.) exist which can help take a product through the R&TTE certification process.

### US Specific - PTCRB

This was mentioned briefly in a previous post, but you can't ignore the PTCRB
if planning to sell in the USA.  As mentioned previously, the cost for this
certification runs between [$16k-20k for a data-only device](http://m2msupport.net/m2msupport/ptcrb-process-costs-timeline-and-labs/).  Other resources suggest a total budget of
[~$80k](http://www.aldacom.com/gsm-info/gsm-certification-info.html), including
all pre-compliance lab testing, field testing, PTCRB, and FCC testing.

### Tests Before the Tests (Precertification)

Before going forward with the formal testing, however, it would be prudent to
seek 'pre-approval' testing.  This sort of testing typically examines two key
test parameters:

* TIS (Total Isotropic Sensitivity) -- measuring reception efficiency
* TRP (Total Radiated Power) -- measuring transmission efficiency

This assumes the device is not 'near' a human body, e.g. not a cellphone
or a wearable.  For devices in those categories, you'll also need to consider
[SAR testing](http://www.intertek.com/wireless-mobile/devices/sar-testing/).

There are [guideline values](http://www.taoglas.com/wp-content/uploads/2015/07/Taoglas-Guidelines-for-achieving-highest-RF-performance-and-passing-select-regulatory-approvals-for-cellular-devices-with-internal-antennas-2010042.pdf)
for these measurements given by Taoglas.  Your cellular module supplier and/or
antenna vendor may be able to perform some form of TIS/TRP testing on your
assembled product, and if not, should be able to refer you to appropriate
"TIS/TRP Precertification Test" facilities.  These tests will usually involve the
testing house inserting a test SIM into your device, and executing a number
of tests in an anechoic chamber -- during which your device should remain
powered on and your firmware should not be attempting to control the GSM
module.  Confirm with the test house, but generally, the firmware elements
controlling the GSM module should be modified to only power on the GSM module
and otherwise leave it alone (e.g. don't try to transmit data).

TIS/TRP testing can acts as a gatekeeper to costlier certifications -- if the
device is unable to pass a basic TIS/TRP precertification test, improvements
should be made before moving to the more formal tests (e.g. PTCRB).  While
analysis equipment (a TEM cell, spectrum analyzer, etc) is typically necessary
to pinpoint the source of noise on a PCB, some 'common' RF performance
improvement moves include:

* [Better via stitching to reduce EMI](http://www.cvel.clemson.edu/pdf/EMCS00-833.pdf)
* Wider/lower inductance power traces to the GSM module
* Switching to shielded inductors where possible
* Keeping the antenna distant from metal (the PCB, components, and housing elements)
* Shorter sensitive signal lines (shorten SIM card interface traces)
* Keeping the GSM module (and any antenna trace) distant from SMPS elements
* Keeping the controlling MCU and GSM module on opposite sides of the PCB
* Any RF traces (antenna to module) are as short as realistically possible
* Determine and follow exact RF trace width/spacing based on PCB thickness

### FCC Approved Module

In the US, using an FCC certified wireless module is an absolute requirement.
Fortunately, most modules you'd be considering from major vendors (Simcom,
Telit, Quectel, ZTE, Huawei, Neoway, etc) will already have FCC certification.

You can check for the FCC record on a given module by searching the [fccid.io](https://fccid.io/)
database.  For instance, the [Simcom SIM800C](https://fccid.io/UDV-SIM800C) is
an example of a widely available FCC certified cellular module.

### Antenna Selection

Ideally, you have plenty of space, and can use an off-the-shelf antenna
and follow the guidelines provided by the manufacturer.  If this is not
the case, I can't be of much help -- consult an antenna expert.  But if you're
designing an IoT product and need to get it to market, generally de-risk by not
trying to make it as small as possible if you can avoid it -- leave around 20mm
air-gap between the antenna and any metal (PCB, screws, buttons, capacitors
or inductors, etc) and you'll save yourself significant headaches resulting
from interactions in the [near-field region](http://www.antenna-theory.com/basics/fieldRegions.php).
Also, get as efficient an antenna as you can reasonably find for the bands
of interest to you.

As an example, this Pulse Electronics [W3538](http://productfinder.pulseeng.com/files/datasheets/W3538.pdf)
has three different versions with varying efficiencies.  To get maximum
efficiency at the 1710-1990 MHz frequency range, you would select the
W3538B0200 model (it lists 80% efficiency, compared to the 70% efficiency
provided by the other two submodels).  Every step along the chain, there is
signal degradation: the connection point to the PCB, attentuation through
the product enclosure, the RF trace on the PCB, so sourcing an efficient
antenna is one way to reduce losses in the signal chain.

### Additional Resources

* ["FCC Approval of Host Devices with Integrated Wireless Modules](http://library.ul.com/wp-content/uploads/sites/40/2015/02/UL_WP_Draft_FCC-Approval-of-Host-Devices-with-Integrated-Wireless-Modules_v6.pdf)
* ["Compliance Design & Certification for IOT Cellular Application" (Digi)](https://www.digi.com/pdf/iot_design_certs_cellularapps.pdf)
* ["Certification Requirements for a GSM Device in North America" (Taoglas)](http://www.taoglas.com/wp-content/uploads/2015/07/Certification-Requirements-for-a-GSM-Device-in-North-AmericaAPN-14-8-003.A.pdf)
* [antenna-theory.com TRP Page](http://www.antenna-theory.com/definitions/trp.php)
* [antenna-theory.com TIS Page](http://www.antenna-theory.com/definitions/tis.php)
