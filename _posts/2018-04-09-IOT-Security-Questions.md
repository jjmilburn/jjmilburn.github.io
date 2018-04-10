---
layout: post
title: IoT Device Security Questions
---

Reasonable security questions to ask of your IoT device/ecosystem

-----

I've been thinking recently about the security properties of
'connected devices', mostly in the context of wireless devices
where the developer is forced to 'roll their own' security to
some degree (e.g. BLE 'Just Works' connectivity).

Unlike a cellular connected device, where a VPN to the carrier network
and a SIM card in the end device might provide 'adequate' security in a
number of cases, techologies like BLE often rely on the application developer
to consider end-to-end security, including key management and crypto best practices.

Sometimes, it is imagined that 'securing' a device simply means 'use a convoluted,
multi-step connection process to "confirm" that the user knows our protocol'. Unfortunately,
thats no guarantee against tampering, and is unlikely to produce a device that won't attract
exploits. Many tools exist to monitor BLE connections across a number of connection attempts,
and once an attack is discovered, publicizing an automated exploit becomes a real possibility.

If some examples of BLE-focused hacks help emphasize this point:

* [BLEAH](https://www.evilsocket.net/2017/09/23/This-is-not-a-post-about-BLE-introducing-BLEAH/) - A fine tool for enumerating BLE devices, finding writeable attributes, and generally probing for attack vectors.
* [Picking Bluetooth Low Energy Locks from a Quarter Mile Away](https://media.defcon.org/DEF%20CON%2024/DEF%20CON%2024%20presentations/DEFCON-24-Rose-Ramsey-Picking-Bluetooth-Low-Energy-Locks.pdf) - Practical explanations of attack vectors against commercially available BLE locks.
* [Blue picking - hacking Bluetooth Smart Locks](https://conference.hitb.org/hitbsecconf2017ams/materials/D2T3%20-%20Slawomir%20Jasek%20-%20Blue%20Picking%20-%20Hacking%20Bluetooth%20Smart%20Locks.pdf) - Attacks against seven specific Bluetooth Smart Locks, with implementation details. 

Similar to how there are static analyzer tools to help catch possible bugs in an implementation, it is worth
considering what design rules/review questions are in place to reveal possible security weaknesses in the
design/architecture phase of a project.

Some 'IoT Device Security Review' questions I've found useful to date include:

**Crypto Basics**

* Does the device have a way to guarantee any command is coming from an 'authorized' source, and not spoofed?

* If a [MAC](https://en.wikipedia.org/wiki/Message_authentication_code) is used to authenticate messages,
is it a standard/published/peer-reviewed hashing algorithm, or an easily spoofable CRC/checksum?

* If an attacker simply 'copies/replays' messages sent by your controller to the device, will they be able to perform unauthorized actions?

* If sensitive data is sent from/to the device, is it encrypted?

* If encryption is used in addition to a MAC, is ['encrypt-then-MAC'](http://www.daemonology.net/blog/2009-06-24-encrypt-then-mac.html) used?

* If encryption is used, is a peer-reviewed algorithm used? What is the work required for any known attack
on this algorithm?

**Key Management**

* How are devices serialized? Is this performed in a trusted/secure environment?

* If keys are stored on the device, can an attacker with a JTAG programmer/debugger read the keys?

* If an attacker decompiles the hex/binary file for your device, can they retrieve any secret keys?

* Are the keys the same in every device, or different?

* Are there tiered levels of access/control for the device? If so, how is each level accessed/enabled?

* Are the keys ever transmitted in plaintext over-the-air or over-the-wire?  If so, when?

* If an attacker discovers a key stored in one device, does this allow them to mount an effective attack on
other devices?

* If keys are used to secure communications over a wireless channel, are they rotated periodically?

* If the device connects to a local 'commodity' controller, such as an Android smartphone, are sensitive keys
stored in the Android application?

* If so, can a reasonably skilled attacker (e.g. familiar with interpreting Proguard protected APKs) decompile
the app and obtain the keys?  If they do, is your application security compromised?

**[Security through Obscurity](https://stackoverflow.com/questions/533965/why-is-security-through-obscurity-a-bad-idea)**

These bullets draw out risks inherent in the system design, usually related to 'features' that are added for
convenience in debugging or testing. If left in the system after release, these can represent a major security
flaw.

* Are there 'maintenance/test modes' that provide privileged access to the system, accessible without any
unit-specific authentication?

* Could a technician or authorized repair center 'go rogue' and use their knowledge to create a scalable hack
against your product?

* Does a detailed design review reveal any assumptions of adversary ignorance, e.g.'the bad guys won't ever figure out our interleaving or hashing algorithm'? 

* If one of your key employees leaked all of the design/architecture/specification documentation related to this project, would you need to restart from scratch, or would the deployment remain secure without a major recall/replacement effort?

Feel free to contact me if you've got your own favorite rules, or any questions on the ones listed here.
