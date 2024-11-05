<<<<<<< HEAD
HTML version of this page can be found [here](https://cracked-machine.github.io/Tiny4xPoweredStereoMixer/)

# Tiny 4 Channel Powered Stereo Mixer

A Tiny form-factor 4-channel stereo mixer for use with TV/PC/Turntable/Amplifer

![](doc/design/SystemContextDiagram.drawio.png)

## Requirements

1. 4 stereo inputs
2. 1 stereo output
3. Inputs will all use stereo RCA/Phono connectors
4. volume controls for each of the 4 input channels
5. volume controls should be mounted in the front of the case
6. External power input shall be 9V DC
7. Internal power supply shall be +/-9V DC
8. Input signals shall be buffered
9. Volume controls for each channel shall  be independant and not subtract gain from the other channels.
10. Cross-talk between channels should be minimised.
  
## Top level Design

![](doc/design/BlockDiagram.drawio.png)

The opamps shall use [TL072](https://www.ti.com/lit/gpn/TL072H)

Using opamps to buffer the input signals and mix the channels together will ensure that the volume of each channel can be mixed independently from the others.

The design will implement an inverting opamp "virtual  ground" topology. The virtual ground refers to the inputs of the mixing opamp: the non-inverting input will be referenced to ground, which means the voltage at both opamp inputs will be referenced to ground because the potential _difference between inputs_ will always try to be zero. The absence of voltage at these inputs will prevent cross-talk between the channels. There is a far superior explanation of this concept at [sound-au.com](https://sound-au.com/articles/audio-mixing.htm#s3)

## Power Stage Design

The old PSU design for Rev A/B can be found in [OLDPSU.md](OLDPSU.md).

The negative voltage rail is generated from the positive rail using a [TPS54202](https://www.ti.com/lit/ds/symlink/tps54202.pdf) SMPS IC. This is normally a buck regulator. However, it can be configured to generate a negative voltage when pin 2 is clamped to ground, forcing pin 1 to a negative voltage.

![](doc\design\TPS54202BuckBoostConverter.PNG)

Further details can be found in the [SLVA933](https://www.ti.com/lit/an/slva933/slva933.pdf) app note

### Soft Start

Unlike the LM2611, the TPS54202 has an internal soft start circuit. This can be seen in the Function Block Diagram of the [TPS54202](https://www.ti.com/lit/ds/symlink/tps54202.pdf) datasheet.

### Output Voltage 

Output voltage is selected using the voltage divider between pins 2 and 1, creating feedback into pin 4. This is very similar to the normal operation of this IC.

### Current Draw and Load Regulation

Output ripple is shown in the [SLVA933](https://www.ti.com/lit/an/slva933/slva933.pdf) app note as 5mV at no load and 20mV at 0.8A (see pages 21-22).

For my own test I [created a small board](https://github.com/cracked-machine/tps54202_inverting_breakout).
The original layout example with the new configuration violated the layout guiidelines from the [TPS54202](https://www.ti.com/lit/ds/symlink/tps54202.pdf) datasheet and proved to be noisy (240mV @ 0.6A). A second revision was made with a better layout that observed the guideline and was a significant improvement (80mv @ 0.6A).

Load regulation was around 5%, dropping from 12V to 11.4V @ 0.6A.

These results were acceptable so this PSU design was used.

## Schematics

<object data="doc/design/Tiny4xPoweredStereoMixer.pdf" type="application/pdf" width="100%" height="1000px">
    <embed src="doc/design/Tiny4xPoweredStereoMixer.pdf">
        <p>This browser does not support PDFs. Please download the PDF to view it: <a href="doc/design/Tiny4xPoweredStereoMixer.pdf">Download PDF</a>.</p>
    </embed>
</object>

## Layout

I used a 4 layer board so I could have a layer per voltage rail. This makes routing the traces significantly easier. I kept the board dimensions below 10cm to minimize manufacture costs. 

[Interactive BOM](https://cracked-machine.github.io/Tiny4xPoweredStereoMixer/doc/design/ibom.html)

## Enclosure

I abandoned the 3D printed enclosure idea and made the PCB to fit this [Hammond RM2005STBK](https://www.hammfg.com/part/RM2005STBK) clam shell enclosure.

=======
HTML version of this page can be found [here](https://cracked-machine.github.io/Tiny4xPoweredStereoMixer/)

# Tiny 4 Channel Powered Stereo Mixer

A Tiny form-factor 4-channel stereo mixer for use with TV/PC/Turntable/Amplifer

![](doc/design/SystemContextDiagram.drawio.png)

## Requirements

1. 4 stereo inputs
2. 1 stereo output
3. Inputs will all use stereo RCA/Phono connectors
4. volume controls for each of the 4 input channels
5. volume controls should be mounted in the front of the case
6. External power input shall be 9V DC
7. Internal power supply shall be +/-9V DC
8. Input signals shall be buffered
9. Volume controls for each channel shall  be independant and not subtract gain from the other channels.
10. Cross-talk between channels should be minimised.
  
## Top level Design

![](doc/design/BlockDiagram.drawio.png)

The opamps shall use [TL072](https://www.ti.com/lit/gpn/TL072H)

Using opamps to buffer the input signals and mix the channels together will ensure that the volume of each channel can be mixed independently from the others.

The design will implement an inverting opamp "virtual  ground" topology. The virtual ground refers to the inputs of the mixing opamp: the non-inverting input will be referenced to ground, which means the voltage at both opamp inputs will be referenced to ground because the potential _difference between inputs_ will always try to be zero. The absence of voltage at these inputs will prevent cross-talk between the channels. There is a far superior explanation of this concept at [sound-au.com](https://sound-au.com/articles/audio-mixing.htm#s3)

## Power Stage Design

To power the buffer and mixer opamps, the design will need a power stage that supports 

1. Bi-polar or symmetric voltage rails that will allow "rail-to-rail" positive and negative headroom for the +/- audio signal.
2. Enough current for 5 (4 input + 1 mix) dual channel opamps. 

Traditionally, a mains transformer was used to take the AC voltage and create a positive/negative supply. This required placing a large transformer in the device itself. This means a much larger physical size for your device. It also meant that you had to centend with AC in your device. Electrocution hazards are not fun.

Modern electronic devices have largely ceased using AC directly. Instead they use an AC adpator built into the wall socket plug that provides a positive DC voltage at a set current.  The leseer the current output, the smaller the plug adaptor.  So how do modern products create an bipolar supply from this positive DC voltage? 

The simplest method for creating a negative voltage rail is....to not create a negative voltage rail! In other words: use a voltage divider to create DC bias mid-point at the positive voltage rail. This has the effect of level-shifting the operating point of the opamp so that the ground reference is halfway between 0 volts and the positive rail. This is a cheap solution because it uses passive components. However, it can cause performance issues with the opamp and complicates the design by adding more components per channel. You also have to be careful to filter out the DC from the input and output to prevent damage to other devices at the load, such as speakers. These are usually audible as loud pops or bangs which are also alarming to the user. You can probably tell I'm not a fan of this design.

You can create a negative voltage from the positive voltage rail with a charge pump voltage regulator. This is an inductorless topology - already a benefit - but has very limited current capabilities - usually only 10-40mA - and is unregulated. This means the generated negative voltage is easily pulled down by the load. Which will occur when your design consists of more than a couple of opamps ICs. In practical terms, you could require a charge pump circuit for each opamp channel. Its also recommended to put a passive low pass filter on each charge pump output to prevent ripple. Each charge pump circuit requires roughly 6 components. In total that could be lot of components when you multiply that per opamp, per channel. This isn't a huge improvement over the complications introduced by the voltage divider design. At least the charge pumps are using the opamps as intended (using a bipolar supply).

In the end I settled on the Cuk regulator topology using the [LM2611](https://www.ti.com/lit/ds/symlink/lm2611.pdf) converter IC. This is a buck-boost type regulator with an inverted output rail. And it comes in a tiny SOT-23 footprint!

Here is the reference design from the datasheet. 

![](doc/design/LM2611_Shutdown_and_Soft-Start.PNG)

### Soft Start

Since the IC doesn't contain a soft-start feature, required additional circuitry is shown in the image above. Without the soft-start, the in-rush current will significantly exceed the current consumption specification. In fact, it tripped the lab power supply current protection; I thought the board had a short. I had to adjust the current protection to to more than 1 Amp to allow the circuit to power up correctly! Not great if you're trying to design a product with a small AC adaptor in mind. Adding the soft-start circuitry meant that the board powered up peaking at 60mA...which is a massive drop and far mroe acceptable. Below is a screenshot of the scope capture. Negative power rail from the [LM2611](https://www.ti.com/lit/ds/symlink/lm2611.pdf) is Yellow, output signal is shown in Blue. The power rail is now much more gradual, rather than a sudden off-on transition. The datasheet implied there would still be a voltage spike at the peak of the turn on transtition but looking at the capture below, it would seem that the voltage spike with the soft-start has also been minimised greatly.

However, a large voltage spike remains on the output signal when the board is powered up. This is the culprit for the pop or thump you often hear in the output device speakers. Not sure what is the best solution here: Maybe caps on the output path, maybe delayed opamp start or maybe just turn the board on __before__ any down stream amplifiers.

![](doc/design/soft-start-scope-capture.jpg)

### Output Voltage 

The output voltage can be adjusted using the negative feedback resistors RFB1 and RFB2.
Rather unhelpfully the datasheet did not contain the equation to calculate the resistor divider values. I found the equation on a post in the TI forums. Maybe this is EE101 but I can't reconcile that omission in my head.

|VREF|RFB1|RFB2|VOUT|
|-|-|-|-|
|-1.23v|2.2K|22k|VREF * (1 + (RFB2/RFB1)  = -13.5v|

Note, that the resistors must not exceed 50Kohm. According to the datasheet this is due to leakage at the NFB pin.

Using common resistor values it was only possible to reach just under 12v or quite a bit over. So I opted for the latter. Better to have a larger power supply swing than too little. It also gives some allowance for load regulation.

### Current Draw and Load Regulation

The application curve from the datasheet shows that with -12V output, the max current it could provide would be 400mA.

![](doc/design/ApplicationCurve-Max_Output_Current_vs_Output_Voltage_12V_to_–5V.png)

Bearing in mind the quiescent voltage of each TL072 opamp is only 1.2mA, this is overkill. However, during active use, the current will certainly be higher. 

The datasheet boasts `Better Regulation Than a Charge Pump` but then fails to offer any evidence elsewhere in the datasheet. I have no doubt that this claim is broadly true - charge pumps have abysmal load regulation. However, I'm always sceptical about the claims of load regulation on these regulator IC's. Usually when a company omits test results from the datasheet it's because the results were not so great.  

Below are the load regulation test results from 10mA to 400mA. Test was run using a [TENMA 72-13210](https://uk.farnell.com/tenma/72-13210/dc-electronic-load-prog-30a-120v/dp/2848407)

![](doc/design/LoadRegulation.png)

The negative voltage rail dropped by ~1.2V at the max load of 400mA, which is a 10% drop. That's actually not __too__ bad. Although if I had picked < 12V output I might not be so forgiving. Still, better than a charge pump...

---

Final note on the Cuk topology: There don't appear to be many IC's out there that support this type of regulator. Either it's overlooked or it has some major drawback that I'm missing here.

I noticed later it is possible to create an inverting regulator from a synchronous buck regulator.  [See this app note from TI](https://www.ti.com/lit/an/slva458b/slva458b.pdf). However, there are significant limitations:

- the input voltage
needs to be higher than the minimum required voltage for the device
- The maximum allowable output
voltage is limited by the maximum Vdev minus the maximum input voltage.
- The maximum load current cannot be more than the maximum current through the internal switches.


## Schematics

<object data="doc/design/Tiny4xPoweredStereoMixer.pdf" type="application/pdf" width="100%" height="1000px">
    <embed src="doc/design/Tiny4xPoweredStereoMixer.pdf">
        <p>This browser does not support PDFs. Please download the PDF to view it: <a href="doc/design/Tiny4xPoweredStereoMixer.pdf">Download PDF</a>.</p>
    </embed>
</object>

## Layout

I used a 4 layer board so I could have a layer per voltage rail. This makes routing the traces significantly easier. I kept the board dimensions below 10cm to minimize manufacture costs. 

[Interactive BOM](https://cracked-machine.github.io/Tiny4xPoweredStereoMixer/doc/design/ibom.html)

## Enclosure

I plan to 3D print the enclosure. Designs will follow...

**TODO: ADD DESIGNS HERE**
>>>>>>> fd31914126166bc3ad245573ad948d9d03d16b6a
