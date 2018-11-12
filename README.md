# ARM-WS2812-DRIVER
An ARM C driver for the WS2812 a.k.a. "NeoPixel". 
This was developed for the BlueNRG2 SoC. However, this should be easily portable to any other ARM based SoC.
This implementation uses SPI to generate the clock. Most of the functionality is self explanatory. 

## Usage

### Initialization
1. LED_Configuration();
2. SPI_Configuration();

### Runtime
1. setColor(uint8_t r, uint8_t g, uint8_t b, uint8_t w);
2. setLED(int color);
3. SPI_Send(uint8_t *command, uint8_t len);

## Explanation
_(You may need to play with these values as it may vary per system)_

To transmit a logic `0` the waveform should resemble this:</br>
wav:`|````_________|`</br>
val: 1 1 1 0 0 0 0 0</br>
bit: 1 2 3 4 5 6 7 8</br>

To transmit a logic `1` the waveform should resemble this:</br>
wav:`|``````````___|`</br>
val: 1 1 1 1 1 1 0 0</br>
bit: 1 2 3 4 5 6 7 8</br>

clock     = 8MHz</br>
1 bit     = 125 ns    = 1     cycle</br>

Logic 0   = 375 ns    = 3     cycles = 3 HIGH 5 LOW</br>
Logic 1   = 750 ns    = 6     cycles = 6 HIGH 2 LOW</br>
period    = 1000 ns   = 8     cycles</br>

This is implemented in the following variables:</br>
#define LED_PULSE_0 0xc0 // 0b11000000</br>
_(Pulses a logic `0` to the WS2812)_</br>

#define LED_PULSE_1 0xf8 // 0b11111000</br>
_(Pulses a logic `1` to the WS2812)_</br>

Thus, the output buffer `led_buffer` is encoded to contain 8 values of `LED_PULSE_X` for each color (R,G,B,W).</br>
Here is an example of what setting the device to purley Green (0, 255, 0, 0) would look like.</br>
_e.g._ </br>
LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0  "R"</br>
LED_PULSE_1 LED_PULSE_1 LED_PULSE_1 LED_PULSE_1 LED_PULSE_1 LED_PULSE_1 LED_PULSE_1 LED_PULSE_1  "G"</br>
LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0  "B"</br>
LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0 LED_PULSE_0  "W"</br>


_N.B. You may need to rearange the order of the `colors[4]` variable inside of the `setColor` function.
My chips were arranged as GRBW as apposed to simply RGBW._
