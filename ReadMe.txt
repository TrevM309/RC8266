Plan is an RC Controller
Joystick connected to an 8266
Should be LiPo powered
Should have USB charging built in
Should monitor battery

Need momentary SW between 15 RST to 8 GND
10 Flash_IO0 needs pull up (10K) to 7 VCC_3.3V, SW to 8 GND for program mode

Use TFT from Sipeed Nano (got spares)
ESP-M2 takes 600mA max
Need 1A buck regulator to get 3V3 from LiPo battery
LiPo battery range is 3V2 to 4V2

1st Attempt (crap)
joystick holes all way too small, oval holes mistake
USB holes all wrong, pins too narrowly placed
vias all too small
12.82-1.32=11.5 / 2 = 5.75
14-0.5=13.5+1.25=14.75
3.75/2=1.875

Thu 13/05/2021
2nd Attempt much better

Fri 14/05/2021
Took half day off
ADC chips arrived
I think the charge cct is working
blue light comes on, it is taking current in low mA range
4v16 at 14:18
3V3 reg appears to be working
3V3 on switch
15:00 showing green (charged) 4V21
--
Checked PortIO, no lib headers, no idea
Go with Arduino
E:\Projects\ArduinoMicro\F8JTS3KJ0COLXAN
  Scanning (SDA : SCL) - GPIO5 : GPIO4 - I2C device found at address 0x48  !
Think that is the ADC, can't see LCD :-(
Reading joystick ok
Reading battery Voltage OK
TFT80160 compiles for Nano, NOT esp8255
  bitmapdatatype
  regtype
  regsize
OK, so no quick fix, try porting from Heatprobe (small fonts)

Sat 15/05/2021
Built Carla cake
Started on case

Sun 16/05/2021
Not much time, birthdays
No way it's going to work with current connections
Needs SPI, it says SDA, but that is SPI with slave responding on MOSI, MISO not used

Mon 17/05/2021
Created pins.xlsx, mostly educated guesses
step1 was to disconnect where needed
Everything except lcd still working well
LCD back light is on with en disconected!
TFT_eSPI did not work
TFTv2 did not work
Adafruit SS7735 graphicstest constantly resets, nothing on lcd
uclibtest
		UTFT(byte model, int CS, int RST, int SER=0);
Nothing seems to drive this LCD, have code for Sipeed Longan Nano though.

Tue 18/05/2021
Re-wired, still no joy

Wed 19/05/2021
Sipeed Longan Nano TFT wiring:
  GD_PA4   SPIO0_NSS	P3_9?
  GD_PA7   SPIO0_MOSI   TFT_SDA
  GD_PA5   SPIO0_SCK	TFT_SCL
  GD_PB0   GPIO_PP_LO	TFT_RS
  GD_PB1   GPIO_PP_LO	TFT_RST
  GD_PB2   GPIO_PP      TFT_CS
spi_config() also of interest

Thu 20/05/2021
Added wires and header to an LCD
1 BackLight
2 BK GND
3 WT CH1 ch0 RST 
4 BN CH2 ch1 RS
5 GN CH3 ch2 SDA
6 BL CH4 ch3 SCL
7 VT CH5 ch4 3V3
8 GY CH6 ch5 CS

Looked at Longan Nano on anylyzer
1 LEDA  n/c
2 GND   bk  gnd
3 RESET wt  ch1 ch0 reset
4 RS    bn  ch2 ch1 rs/dc
5 SDA   gn  ch3 ch2 mosi
6 SCL   bl  ch4 ch3 clk
7 VDD   vt  ch5 ch4 high
8 CS    gy  ch6 ch5 enable
All looks good at max 25 MS/s
RESET is always high
RS looks as expected
Session 5.sal = known good capture off Sipeed Logan Nano through reset (got start up)
Clock = 12 MHz 50% duty
CS goes low before clock, 8 clocks (data on SPI), CS goes Hi
CS byte width = 1.375 uS
1.125 between bytes on clock
RS looks like it is correct, mostly Low, sometimes high for 3 bytes

switch to RC
SPI clock looks good
Enable not changing
MOSI could be Enable

Fri 21/05/2021
Didn't bother until 20:30
Can't get it going
Maybe need to drop to breadboard

Sat 22/05/2021
Saleae Logic is annoying, keeps bombing out with my cheap analyser
PulseView is much better
Then PulseView died, uninstall / reinstall could not get it back
Logic 2.3.27 started behaving
Finally got it driving the LCD properly in RcCtlTft
Re-designed PCB with connections found

Sun 23/05/2021
Built PCB
Driving the LCD nicely
The USB connector won't stay on!
The ADCs don't work
Not tried battery charging
CS will work tied to GND, frees up GPIO5
Done on Schematic and layout, try to link PCB

Mon 24/05/2021
Linked the PCB, it worked, still had a problem with horizontal, needed touching up.
Re-visited PCB
Horiz
	left 0x1d(29), centre 0x193(403), right 0x232(562)
	swing left 374, swing right 159
Vertical
	down 0x42(66), centre 0x17f(383), up 0x228(552)
	swing down 317, swing up 169
Ok, be better to have settings in NVM?
Old ESP8266RcCtrl has NVM
00

Tue 25/05/2021
joystick reading is crap
  --- 3V3
   |
  | |
  | |<-- ADC
  | |
   |
  --- GND
worth a try, maybe link it
Araldited USB connector on
joystick better

Wed 26/05/2021
Add supports in case, printed
No joining, either glue or heat seal
Look at Joystick (the one NOT soldered on)
10K (9K72,9K91)
--
5k6  10.94 1.36
5.52 10.58 0.50
--
5.17 10.40 0.43
5.20 10.38 0.45
--
so, 10k between 0V and 3V3 is ok

Thu 27/05/2021
Case re-work, added housing around USB connector
PCB re-work

Fri 28/05/2021
PCB re-work

Sat 29/05/2021
Printed case in grey (230 degrees)
Etched and drilled PCB

Sun 30/05/2021
Could not get LCD to work

Mon 31/05/2021
Got LCD running
HW all tested and correct
4.158V (on Charge, powered by Battery) 20:28
4.164 20:37
4.170 20:41
4.134 charge complete, powered by battery
OK, now need RC device
Current KiCad:
	RcCtrl1: ESP-03, 3V3 reg, 1 pin per servo
	RcCtrlBat:  ESP-03, cleaned up ver of RcCtrl1
	RcCtrlReg:  Looks same as RcCtrlBat
	RcCtrlRegLev: Level shifters plus +- on Servos, design I used
CD40109BPW level shifter, 16 pin

Sat 12/06/2021
RC8266Device done and built
Charger appears to be good
RC RX appears good
Need another program PCB
Created
OK, all hardware appears to be working
Need SW to tie them together.

Sun 13/06/2021
Start device with old ESP8266RcCtrl, on COM5
No longer works with my Nokia phone!
Seems to do the same with my old Samsung!
Just shows mouse position!
Ok, so I have WiFi examples for simple server and client
Suggesting controller is server, device is client
Controller on COM23
Tera Term INI:
	; Enabled ISO-2022 Shift Function (on/off/combination of SI,SO,LS2,LS3,LSR1,LSR2,LSR3,SS2,SS3)
	ISO2022ShiftFunction=off
Too hot, got bored

Fri 18/06/2021
Took pm off, try again
Device is server, controller is client
client/controller constantly sends H & V percentages to server/device
Not sure about signal strength or battery readings going from server/device to client/controller
siyekServe and siyekClient works together
Servos working, move joystick, servos ch1 & 2 move, but not quick!

Sat 19/06/2021
Ordered 5 off battery from Ebay at £16.60 = £3.32 due Wed 23/06
Ordered 10 off battery from AliExpress at £29.99 = £3.00 due 30-50 days
Re-worked controller to send more often, much more responsive
Cleaned up controller code
1 dodgy speed controller, 1 good
Added 100n capacitors to all PCBs

Sun 20/06/2021
Ordered PCB from Elecrow

Mon 21/06/2021
Elecrow requested my phone number

Sat 26/06/2021
Got Ebay batteries
PCBs all created, controller working
Shortage of 10uF caps, due this pm (Farnell, ordered Thu night)
PCBs have been made by Elcrow and are due in 7 to 10 days
Need to make case for new controller
Need to fit caps and check device is running
Signed up to Hackaday.io and added project

Sun 27/06/2021
Still no Farnell order (was supposed to be Sat pm, now Mon pm)
Re-visited Ctrl case
Can export step from KiCAD
Can load step and export as STL in FreeCAD
Can import ("file") in OpenSCAD

Thu 01/07/2021
Been wrestling with new PCBs, trying to get everything working.
Tried device case, that was crap.
Using step / stl, now have good models for all 3 PCBa in OpenSCAD
Got reading ADC of ESP-M2
Reading 0x316 with full 8.4V battery
adc/1023 = 1/11 of Voltage
Reading ok! Was too easy
Controller is spamming device with 0/1 H & V

Fri 02/07/2021
Day off (speed awareness course)
Lots of playing with software
Got device battery level in controller
Got signal strength in dBm
-30 Amazing
-70 Ok
-80 Not Good
-90 crap
Think I'm ready for LCD graphics
Need Device to set servos to 0 if client lost
sin a = opp / hyp
cos a = adj / hyp
opp (x) = sin(a) * hyp
adj (y) = cos(a) * hyp
Got signal meter

Sat 03
Sun 04
Mon 05/07/2021
Complete, published files to Hackaday.io

Tue 06/07/2021
Got PCBs

Built up a Controller PCB and used with old buggy 8266-12 device
Decided to build a dual joystick to see usage difference

Sat 17/07/21
Finished building dual joystick version, controller was working out of case
2nd joystick stopped working when cased up!
Built up device PCBs, all working
Was crap, scrapped

Sat 24/07/21
New PCB for Dual Joystick
0 RH ok
1 RV ok
2 LH ok
3 LV ok
Everything working electrically
Fished Dual Case design








---------------------------
TX Mode 1
   E                T
   L                H
   E                R
   V                O
RUD*DER		AILE*RONS
   A                T
   T                T
   O                L
   R                E
---------------------------
TX Mode 2
   T                E
   H                L
   R                E
   O                V
RUD*DER		AILE*RONS
   T  		    A
   T		    T
   L		    O
   E		    R
---------------------------
TX Mode 3
Mode 2 with sticks reversed
---------------------------
TX Mode 4
Mode 1 with sticks reversed