### Add remote control
- Add digital potentiometer ICs for digital control of Op Amp gain
- Replace mechanical potientiometers with rotary encoders to allow manual gain control
- Add ESP32 module to simultaneously
    - run a web server that serves webpage containing a slider graphical user interface
    - read rotary encoder values via ESP32 timer peripherals
    - control the op amp gain via the digital pots (using either values from the rotary encoders or the web GUI)
 - 12V PSU is not required for line level signals. Lower to 2.5V.
    - Will allow wider selection of digital pots
    - TPS54202 min input voltage is 4.5vdc so would still need to power the board using a 5vdc transformer:
        -  Put the +5vdc input into an LDO and regulate it down to the +2.5vdc rail.
        -  Put the +5vdc input into the TSP54202 inverter and create the -2.5vdc rail.
      
### Reading Notes

- [https://www.analog.com/en/resources/technical-articles/audio-gain-control-using-digital-potentiometers.html](https://www.analog.com/en/resources/technical-articles/audio-gain-control-using-digital-potentiometers.html)
