# COMIX-35 Home Computer
The COMIX-35 is an open-source clone of the 1980s COMX-35 8-bit home computer.
It uses the RCA 1802 microprocessor which is strange and slow but holds the
distinction of being the first CMOS microprocessor. The "35" is because the
original computer has 32K of main memory + 3K of video RAM.

![photo](https://github.com/schlae/comix-35/blob/master/images/comix-35.jpg)

There are a number of differences between the COMIX-35 and the original:

* Single +5V power supply
* Uses SRAM; no stolen cycles for DRAM refresh
* Configurable PAL or NTSC by changing three jumpers and swapping a crystal
* Additional CPU crystal for a new turbo mode
* Mechanical key switches for your typing comfort
* Cassette input data light indicates incoming information

## Design files

Here are the design files. The BOM includes Mouser part numbers for everything
except the CDP1802, CDP1869, CDP1870, CDP1871, the trimmer capacitor (optional),
and the expansion slot header (also optional). I recommend you purchase IC
sockets for the four CDP parts, as well as for the ROM chip U15.

[Schematic](https://github.com/schlae/comix-35/blob/master/COMIX-35.pdf)

[Bill of Materials](https://github.com/schlae/comix-35/blob/master/COMIX-35.csv)

[Fab Files](https://github.com/schlae/comix-35/blob/master/fab/COMIX-35.zip)

The board is rather large, at 12 x 6.5 inches (304.8 x 165.1 mm), but it does
include the keyboard. It is a 2-layer design so it should be relatively cheap
to manufacture. A word of advice: pick out your keycap set first, then choose
the PCB color to match it.

The keyboard keyswitches are easily substituted for lower-cost ones from other
vendors. I recommend you purchase DSA-profile keycaps since some of the keys
are located in rows that are not typical. I ended up buying the [YMDK 125 DSA Dye Sub 9009 Retro PBT Full Keyset for MX](https://www.amazon.com/gp/product/B083L6FVPN/). You can choose whatever you want, but make sure there is a 1 unit
control key and a 1.25 unit shift key. The enter key needs to be 1.5 unit, so
I just used the tab key for that instead. If you have any doubt, check the PCB
layout which has the key cap unit sizes marked below each key.

I have not included part numbers for the 0.1" headers which usually come as a
long strip that you break into the appropriately-sized sections.

Most other components may be safely substituted. U4 is a little tricky. It
should be a 74HCU04 in order for the oscillators to operate, but in a pinch,
a 74HC04 will also work. A 74LS04 or a part from another logic family will
not work.

## Assembly notes
Since the standard COMX-35 ROM configures the CDP1869 to use extra-tall
characters, you need to tie CDP1869 pin 25 to CMA3. Do this by soldering a
jumper wire from pin 1-2 on JP6. If you wish to write your own code to deal
with short characters and more text pages, then install a 0.1" header and
jumper instead.

The default configuration for PAL is as follows:

| Location | PAL     | NTSC    |
| -------- | ------- | ------- |
| JP1      | 2-3     | 1-2     |
| JP2      | 2-3     | 1-2     |
| JP3      | 2-3     | 1-2     |
| Y1       | 17.734 MHz | 14.318MHz |

The trimmer capacitor, C12, is optional. It allows you to adjust the color
image for best clarity. If you don't use it, then just stuff C13 with a 22pF
capacitor.

You can also configure your COMIX-35 for turbo mode which allows the CPU to
run at much higher frequencies than the original computer, limited only by
the maximum frequency of your particular CDP1802. Check the datasheet for the
variation you have, or experiment. Turbo mode is enabled by jumpering JP4 to
position 1-2. This enables the clock oscillator installed at Y3. To run at the
standard speed, set JP4 to 2-3.

If you use a 28C256 or 27C256 ROM, jumper JP5 will allow you to select between
the upper and lower 16K of ROM. This is useful if you want to use store two
different ROM binaries, such as the standard COMX-35 ROM and your own custom
program.

The COMX-35 ROM image is relatively easy to find so I will not provide it here.

## Video adjustments

There are two trimmer resistors that adjust the video:

* RV3 adjusts the sync pulse amplitude
* RV4 sets the overall video amplitude

The simplest way to get it working is to use an oscilloscope to set the sync
pulse amplitude to about 300mV and the video amplitude to about 700mV. If you
don't have an oscilloscope, set RV4 to the maximum, set RV3 to the minimum,
and then slowly increase RV3 until you get a stable picture. If the colors
are not saturated enough, you can decrease RV4.

The C12 trimmer capacitor, if you installed it, adjusts the frequency of the
colorburst by a small amount. Since the colorburst and the pixel clock are not
sychronized, you'll get a "shimmering" effect on colored pixels. Adjust C12
until you can't see shimmering anymore. 

## Loading programs off a computer using the cassette port
The process I've had success with is this:

* Run the [Emma 02 emulator](https://www.emma02.hobby-site.com/). Turn off SB
mode, turn off Turbo LOAD/SAVE. Make sure Auto LOAD/SAVE is turned on.
* Hit run. Press enter to go past the COMX screen and get to the BASIC prompt.
* Click the LOAD button and select the program you are interested in.
* From BASIC, type PSAVE.
* Name the file (it is actually a raw sound file, not WAV.)
* It will take a while but it will eventually save the cassette sound.
* Load the raw file in Audacity using File-\>Import-\>Raw Data. Select 22050 as
the sampling rate, mono, signed 16 bit little endian.
* You may wish to save the program as a regular WAV file at this point.
* Connect an audio cable between the COMIX-35's EAR jack and your computer's
speaker output.
* Make sure your computer has absolutely no audio processing going on at all.
No echo, no equalizer, nothing at all. You may have to really crank up the
volume.
* Hit play, and then adjust the input bias control until the data LED flickers.
* Stop, rewind, hit play, and then (on the COMIX-35) type "PLOAD" and hit
enter. If everything works, PLOAD will return you back to the BASIC prompt.
* Type RUN and hit enter to start the program.

The cassette interface is a bit tricky to work due to various reasons. The
input circuit uses a Schmitt-trigger buffer, so the DC bias of the incoming
audio signal must be adjusted to the halfway point of that threshold. This
is what the INPUT BIAS trimmer does.

You also need enough volume to be able to exceed the rising and falling
thresholds.

Tips and tricks if things don't work right the first time:
* Does your computer *really* provide clean audio? You've turned off all
effects processing? Audio managers, headphone bass boosters--all that *must* 
be turned off!
* Check your cables
* Check the volume level on the PC. If it is too low, it will not work
* Connect an oscilloscope to the cathode of D5 and make sure you see a clean
waveform

## Compatibility
Note that I have not tested the COMIX-35 with real COMX-35 accessories, such
as the expansion chassis, RAM boards, disk controllers, etc. It should work
with all these accessories except for the RAM expansion because it uses the RAS
and CAS lines. Since the COMIX-35 does not have the DRAM refresh circuit, these
signals don't exist on the expansion connector.

## License
This work is licensed under a Creative Commons Attribution-ShareAlike 4.0
International License. See [https://creativecommons.org/licenses/by-sa/4.0/](https://creativecommons.org/licenses/by-sa/4.0/).
