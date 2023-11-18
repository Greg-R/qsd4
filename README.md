# QSD4

This is the PCB for the QSD4 receiver module for the T41 "Software Defined Transceiver".
The PCB was designed using the open-source design tool Kicad 7.

The QSD4 evolved from the original V010 design which remains the most recently published
receiver module for the T41 Software Defined Transceiver.

The goals of the QSD4 are to improve receiver performance, and in particular, the performance
in the 10 meter amateur radio band.  Please note that the designer of this project does not
own a laboratory full of expensive test equipment.  Much of the verification of the receiver
performance has been done "on the air".  There are no guarantees or other warranties of the
performance of this receiver module.  What you see is what you get!

Gerber files for PCB fabrication are included in the gerbers folder.
A PDF of the schematic (qsd4.pdf) is included for quick viewing of the circuit design.

Please note that the components used in this design increase the cost compared to the
original design.  However, the board is easier to build.  A link to a public Digikey
BOM is included so that the prospective builder can evaluate the cost before proceeding.

## Divide-by-2 versus Divide-by-4

The original QSD uses a divide-by-4 quadrature LO generator circuit.  This is a robust
circuit which produces high-quality quadrature local oscillator waveforms.  However,
the circuit has a trade-off and that is the requirement for VHF range frequencies in the
higher amateur HF bands.

For example, reception of a SSB signal at 28.510 MHz requires a frequency of:

(28.510 MHz + 48 kHz) * 4 = 114.232 MHz

Note the 48 kHz term which is the IF frequency.

This frequency is applied to the quadrature generator which is composed of a dual flip-flop
integrated circuit.  The circuit is powered from the 3.3 volt supply.

The flip-flop device is a type 74AC74.  The specification sheet is here:

<https://www.ti.com/lit/ds/symlink/sn74ac74.pdf>

The specification of interest, fclock with Vcc = 3.3 volts, is on page 4, and the maximum is 95 MHz.
This is insufficient to support the 10 Meter band.

One page the same specification is shown for Vcc = 5.0 volts, and the maximum increases to 125 MHz.
This shows a method to increase the frequency range, however, a matching 5.0 volt specified multiplexer
must be used.  A 5.0 volt multiplexer is in fact available at no cost penalty.

For optimal quadrature circuit performance, the coaxial cable between the Main board and the QSD4 should be
as short as possible.

## A Summary of Methods to Extend the T41 Frequency Range

The T41 is an "Experimenter's Platform", and there is no right or wrong way to solve a problem.
It is the experiment; the journey of discovery and amateur radio fun and adventure is the destination!

Here are a few ways to experiment with T41 frequency range expansion:

1.  Change the bias voltage on the 74AC74 from 3.3 volts to 5.0 volts.  Change the multiplexer IC to a part
specified for 5.0 volt operation.  This changes increases the frequency response of the flip-flop device.
2.  Change the 74AC74 to a device specified for higher frequencies while leaving the bias at 3.3 volts.  The
device is the 74LVC74.  The specification sheet is here:  <https://assets.nexperia.com/documents/data-sheet/74LVC74A.pdf>.
3.  Change the divide-by-4 circuit to the divide-by-2 circuit as described above.  This circuit is slightly more complex,
however, it has large margin to the upper frequency limits of the components at 10 meters.  6 meter operation may be possible.
There is no known low-frequency limit to the divide-by-2 (or divide-by-4) circuit.
4.  The T41 V012 design generates quadrature using an internal phase-shift circuit, such that one clock output is in-phase
and another clock output is shifted by 90 degrees.  This is directly applied to the multiplexing (demodulator) device,
and thus this circuit is very simple.  This also exploits the maximum upper frequency range of the Si5351 phase-lock-loop IC.  
On the other hand, this introduces a low frequency limit and it may not be possible to cover the LF and VLF amateur bands with this scheme.

The QSD4 circuit uses either method #1 or method #2.  Method #1 is what I have used during prototyping of the QSD4.
A zero ohm jumper selects either 3.3 or 5.0 volts applied to the quadrature generator circuit.

## Other Changes to the QSD4 Circuit Design

The QSD4 receiver module circuitry is almost entirely revised compared to its ancestor the V010 QSD.

### RF Amplifier

The discrete bipolar transistor RF amplifier is replaced with a Mini-Circuits integrated wideband amplifier:

<https://www.minicircuits.com/pdfs/GALI-39+.pdf>

Using this device is easy as it requires only a bias resistor and two coupling capacitors.  There is a series
of similar devices in the same package.  This particular device may not have the optimal gain and intermodulation
performance, however, it will be easy to substitute and experiment with other Mini-Circuits amplifiers in the same
family.

### Pi RF Attenuator

The problem with many of the integrated RF amplifiers is that they have too much gain!  Too much gain will reduce
the intermodulation and general strong-signal handling of the receiver.  Too much gain will have negligible impact
on receiver sensitivity.

A Pi resistive attenuator is added between the output of the RF amplifier and the input of the demodulator.
Thus the front-end gain can be adjusted to optimize both receiver sensitivity and intermodulation performance.

### Double-Balanced Quadrature Sampling Demodulator

The original V010 demodulator circuit is a single-balanced design.  The QSD4 is upgraded to double-balanced.
This requires an input transformer with a tapped secondary:

<https://www.minicircuits.com/pdfs/ADT4-6T+.pdf>

The tapped secondary provides the means of applying a common-mode bias to the demodulator device.
Using this ready-made transformer means no toroid winding!  However, it is expensive:  US$6.25.

### Balanced Output Summing Capacitors

The output of the demodulator uses "differential mode" integration capacitors.  The idea here is to balance
the output circuit to the highest degree possible.  The capacitors should be specified to 1% tolerance.  The typical
shunt capacitors are also provided.  The capacitor values in this circuit may be further optimized.

### Instrumentation Amplifiers

"Instrumentation amplifiers" replace the op-amps from the V010 circuit.  The instrumentation amplifiers are intended
to improve the symmetry and balance of the demodulator circuit, thus improving performance, and in particular the image rejection.

Another benefit of the instrumentation amplifiers is the simplification of the circuit.  A single resistor sets the gain.
The gain setting resistors should be specified to 1% tolerance.

A disadvantage of the instrumentation amplifiers is higher cost.  The AD8226 device cost is US$3.77.  Quantity 2 are required.

### Power Supply Decoupling

The power supplies routed through the 16 wire ribbon cable are prone to noise, most likely from the Main board.
Extra decoupling components were added to clean up the power supplies.  This has been noted in a lower noise floor
of the displayed spectrum.

### Option for 3.3 or 5.0 Volts Biasing and Comments on Reference Regulators

A zero ohm jumper is placed to select either 3.3 or 5.0 volt supply bias to the quadrature and demodulator devices.
It is also necessary to change the instrumentation amplifier reference regulators depending on the the chosen bias voltage.
Use 1.8 volt regulators with 3.3 volt bias, or a 2.5 volt regulator with 5.0 volt bias.

There are independent regulators U7 and U8 for the instrumentation amplifiers.  It may be possible to use a single regulator
for both.  A jumper resistor R16 can be placed to implement this change.  The capacitor C26 and U8 are not used.  This
change has not been tested yet, however, there is a good probability it will work fine.

### Connectors

The I/Q output connector is changed to surface mount.  The RF SMA connectors are the same, or they can be changed to right-angle
connectors to improve coaxial cable routing.  The 16 pin IDC connector is the same as the V010 board.

### PCB Layout

The PCB layout was completed using Kicad version 7.  This is an open-source tool, unlike the "Dip Trace" commercial product used
to design the V010 and also the V012 boards.

The layout in the quadrature generator and demodulator areas was very carefully routed for maximum frequency performance.
The QSD4 is approximately the same board size as V010, but there is enough area to add additional circuitry if desired.

The prototype PCBs were fabricated by PCBWay at a cost of US$1.00 each.  Shipping was about US$25.00 for quantity 5 boards.

## Bill Of Material (BOM)

A public Digikey BOM is here:

<https://www.digikey.com/en/mylists/list/WJHL6RPJHR>

This BOM includes parts to build a board with 5.0 volt biasing.  This is assumed to be the highest performance configuration.

Please note that specific parts may or may not be available when attempting to order.  It is the responsibility of the builder
to find subsitutes as required.

## Build Tips

In general, QSD4 is easier to build than the original V010/V011 series boards.  There are a few items to be aware of to avoid
build errors.

The most probable error(s) are incorrect orientation of the flip-flop, multiplexer, transformer, and instrumentation amplifier devices.  The integrated circuits have round dots located near pin 1.  The transformer has a visible center tap, and this should be connected to the output side of the circuit.

There are also 3 leaded electrolytic capacitors.  The markings for these parts are clear on the PCB.

Here are details on each part with regards to proper orientation of the board.  The descriptions are viewing the top side of the board, with the "T41 EXPERIMENTERS PLATFORM" near the bottom of the board in normal left-to-right reading orientation.

### Transformer TR1

The transformer TR1 should be placed with the "dot" at the upper right corner.  The textual markings will be upside down.

### 74AC74 Dual Flip-Flop U4

Pin 1 should be in the upper left corner.  This is marked with a small white dot on the PCB.
Note that both the flip-flop and the multiplexer are 4.4 mm TSOP type packages.

### 3253 Multiplexer U3

Pin 1 is at the upper left.  So the part is placed with the dot up, towards the flip-flop device.
So for the QSD4, the flip-flop and the multiplexer pin 1 dots should both be upper left.

### Instrumentation Amplifiers AD8226 U5 and U6

Only U5 has the Pin 1 indicated with a dot on the PCB.  However, both parts are oriented the same, with Pin 1 towards the upper left.  There is a dot on the parts to indicate Pin 1, however, it is very hard to see.  The package is beveled on the Pin 1 side; this is easy to see.

### Non-placed Parts

C20 and C21 are not used.

R29 and R30 are zero-ohm jumpers.  Place R29 for 5.0 volt bias on the quadrature generator.  This is the recommended configuration, and don't place R30 which is the 3.3 volt jumper.

### Bottom Side Parts

There is 1 capacitor on the bottom side.  This should be soldered last.  I was able to use a hot air gun without any problems with the top-side components desoldering.

R16 is placed on the bottom side in the case of using only one of the reference voltage regulators.

### SMA Connectors

Take a good look at the placement of the board in your T41 radio.  You will want the SMA connectors of the QSD2 in the orientation for easy coaxial cable routing.  I used 90 degree connectors as this was the easiest for extensive testing, but not necessarily the best for permanent installation.  Also, you may choose to put the SMAs on one side or the other for optimal cable routing.

Please note that the cable from the Main board to J1, which is the receive local oscillator, should be as short as possible.

### High Resolution Photos of QSD4

Links to photos of a fully constructed QSD4 follow.

<https://drive.google.com/file/d/1aspcZNfzApl-qxwgoq-D433Be0dcwM16/view?usp=sharing>

<https://drive.google.com/file/d/1kP5jGNJovGSlniU-m27XpsKZK9AjJI5x/view?usp=sharing>

<https://drive.google.com/file/d/10uLrnD7fPuJHRcCnD-vJwYjdFCpwKCTR/view?usp=sharing>

<https://drive.google.com/file/d/1hoJ_x_dKpq-bHXMOEn-O8QFBmkSheuwZ/view?usp=sharing>

Please note that the above photos show a version 1.0 board.  The Gerber files are for
a revised version 1.1.  The only significant difference is the placement of a 0 ohm resistor
on the back side to allow optional sharing of a single 2.5 volt reference source.

## References and Further Reading

The QSD4 is an attempt to integrate the best circuits from several sources into a high-performance HF receiver module.
Here is a list of sources which inspired the design of the QSD4:

1.  "Digital Signal Processing and Software Defined Radio, Theory and Construction of the T41-EP Software Defined Transceiver",
by Albert F. Peter, AC8GY, and Dr. Jack Purdum, W8TEE.  This is the ultimate resource for builders of the T41 Software Defined
Transceiver:  <https://www.amazon.com/Software-Defined-Radio-Transceiver-Construction/dp/B09WYP1ST8>
2.  "A Software Defined Radio for the Masses, Part 4" by Gerald Youngblood, AC5OG.  This shows the circuit for the double-balanced
demodulator circuit and the use of instrumentation amplifiers on the output circuit.
<https://www.arrl.org/files/file/Technology/tis/info/pdf/030304qex020.pdf>
3.  The Rod Gatehouse 2022 transceiver website:  https://ad5gh.wordpress.com/2022-sdr-transceiver/
Rod took the original T41 SDT design and evolved it into a high-performance digital mode radio.  Excellent circuit upgrades!
4.  "The Lentz Receiver: Tayloe Evolved" by H. Scott Lentz, AG7FF.  An interesting discussion of several design features of the
"Lentz Receiver" which improve the performance of the typical "Tayloe Detector" style HF receiver.
<https://www.arrl.org/files/file/QEX_Next_Issue/2023/05%20may-jun%202023/05%202023%20TofC.pdf>
5.  The QSD2 which is very similar to the QSD4:
<https://github.com/Greg-R/qsd2>


