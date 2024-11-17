### Add remote control
- Add digital potentiometer ICs for digital control of Op Amp gain
- Replace mechanical potientiometers with rotary encoders to allow manual gain control
- Add ESP32 module to simultaneously
    - run a web server that serves webpage containing a slider graphical user interface
    - read rotary encoder values via ESP32 timer peripherals
    - control the op amp gain via the digital pots (using either values from the rotary encoders or the web GUI)
 
### Reading Notes

- [https://www.analog.com/en/resources/technical-articles/audio-gain-control-using-digital-potentiometers.html](https://www.analog.com/en/resources/technical-articles/audio-gain-control-using-digital-potentiometers.html)
