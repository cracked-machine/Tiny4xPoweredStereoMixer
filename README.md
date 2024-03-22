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

Using opamps to buffer the input signals and mix the channels together will ensure that the volume of each channel can be mixed independently from the others.

The design will implement an inverting opamp topology, sometimes known as a "virtual  ground" design. The virtual ground refers to the inputs of the mixing opamp: the non-inverting input will be referenced to ground, which means the voltage at both opamp inputs will be referenced to ground because the potential _difference between inputs_ will always try to be zero. The absence of voltage will prevent cross-talk between the channels.

## Power Stage Design

To power the buffer and mixer opamps, the design will need a power stage that supports 

1.  bi-polar or symmetric voltage rails that will allow "rail-to-rail" headroom for the +/- audio signal.
2.  enough current for 5 (4 input + 1 mix) dual channel opamps.

The simplest method for creating a negative voltage rail is to....not create a negative voltage rail. In other words: use a voltage divider to create DC bias mid-point at the positive voltage rail. This has the effect of level-shifting the operating point of the opamps. This requires only passive components - which is cheap - but also complicates the opamp design and can create issues with the opamp behaviour itself. You also have to be careful to filter out the DC from the input and output to prevent "pops" and damage to other devices such as speakers.

A better way is to actually create a new negative voltage rail from the existing positive rail. 

You can do this easily with a charge pump voltage regulator. This is an inductorless topology - already a benefit - but has very limited current capabilities, usually only 10-40mA, which is unregulated and quickly drops below the target voltage rail as soon as a load is present. In practical terms, you would require a charge for each opamp (ideally at the point of each load)....and thats not very practical at all. Its also recommended to put a passive low pass filter on each charge pump output to prevent ripple. Each charge pump circuit requires roughly 6 components, which becomes a lot when you multiply that for each opamp.

In the end I settled on the Cuk topology using the [LM2611](https://www.ti.com/lit/ds/symlink/lm2611.pdf) converter IC. This is a small SOT-23 footprint. This is a buck-boost type regulator with an inverted output rail. 

Here is the reference design from the datasheet. 

![](doc/design/12V_to_–5V_Inverting_Converter.PNG)

The output voltage can be adjusted using the negative feedback resistors RFB1 and RFB2:

|VREF|RFB1|RFB2|VOUT|
|-|-|-|-|
|-1.23v|10K|88.7k|VREF * (1 + (RFB2/RFB1)  = -12.14v|

The application curve from the datasheet showing the output current vs the output voltage: For -12V out, the max output current would be 400mA.

![](doc/design/ApplicationCurve-Max_Output_Current_vs_Output_Voltage_12V_to_–5V.png)

This is way more current than we need for this project but I'm always sceptic about the load regulation on these regulator IC's so this is an experiment as much as anything else.  I will be testing the power supply stage with a variable load to see how close I can get to this current draw in the real world.

**TODO: ADD LOAD RESULTS HERE**

## Schematics

<object data="doc/design/Tiny4xPoweredStereoMixer.pdf" type="application/pdf" width="100%" height="1000px">
    <embed src="doc/design/Tiny4xPoweredStereoMixer.pdf">
        <p>This browser does not support PDFs. Please download the PDF to view it: <a href="doc/design/Tiny4xPoweredStereoMixer.pdf">Download PDF</a>.</p>
    </embed>
</object>

## Layout

I used a 4 layer board so I could have a layer per voltage rail. This makes routing the traces significantly easier. I kept the board dimensions below 10cm to minimize manufacture costs. 

[ibom](https://cracked-machine.github.io/Tiny4xPoweredStereoMixer/doc/design/ibom.html)

## Enclosure

I plan to 3D print the enclosure. Designs will follow...

**TODO: ADD DESIGNS HERE**
