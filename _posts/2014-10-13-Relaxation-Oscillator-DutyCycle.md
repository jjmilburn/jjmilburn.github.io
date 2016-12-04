---
layout: post
title: Relaxation Oscillator with Variable Duty Cycle
---

A "Relaxation Oscillator" is just a fancy name for a circuit which generates a repetitive output signal (Wikipedia says this must be nonsinusoidal).  Most examples are for 50% duty cycle designs.  Here, build one using the [NXP NXC2200](http://www.nxp.com/documents/data_sheet/NCX2200.pdf).

-----

### Components

* Comparator (op-amp will work too)
* 3x equal value resistors (R1-R3)
* 1x small signal diode
* 1x capacitor in the 1-20nF range (C1)
* 1x resistor in the 100 to 500k range (R4)
* 1x resistor approximately 20% the size of R4 (R5)

Wire up the circuit like this:

<a href="http://everycircuit.com/circuit/6290233901449216">Relaxation Oscillator - EveryCircuit</a><br> <iframe width="560" height="360" src="http://everycircuit.com/embed/6290233901449216" frameborder="0"></iframe>
 
Note that in this example, R1-R3 are 510k, C1 is 8.2nF, R4 is 330k, and R5 is the combination of 50k and 30k -- simulation is a bit wonky when it comes to making this converge.

### Testing

Wire up the comparator/op-amp, and take a look on your scope.  The pulse width seen at the output should have a variable duty cycle. This is a direct result of the time constant for charging C1 being smaller than the time constant for C1 to discharge (due to the diode allowing for a parallel resistance only when C1 is charging). 

### Uses

Timing circuits, periodic enable lines (or wakeups), triggers for sampling, etc.  The shorter on-time can allow for lower quiescent current when attempting to enable a power-hungry device that doesn't need to be on for the full 50% duty cycle.

### Additional Notes

- The quiescent current of this circuit can get fairly low; on the order of microamps, depending on component selection.

- If repeatable thresholds/oscillation frequencies are not a concern, this can be made pretty cheaply with a Schmitt Trigger instead of comparator/op-amp.
