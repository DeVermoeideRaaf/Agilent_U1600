# Remote control command set
Actual state: Sending commands is well known. Analysis of the response needs to be done.

## Communication settings
* UART
* baudrate: 230400
* 8N1
* no parity
* no handshake
* LSB first

## Theory of remote command set
* every button has its number
* sending the number -> button action

## Commands
Most commands consists of six bytes.
Usually command starts with hex 06 and ends with hex 3b.
Button names are taken from software PC Link V2.51 (screenshot needs to be added).

![The screenshot shows the scope surface as its rendered in PC Link 2.51](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/PCLink251-ScopeSurface.png "Scope surfave shown in PC Link 2.51")


|button name          |function             |command (hex)    |
|---------------------|---------------------|----------------:|
|Power on             |lock local buttons   |53 54 41 52 54 3b|
|Power off            |unlock local buttons |   53 54 4f 50 3b|
|n.a.                 |send screen copy/data|06 00 00 00 00 3b|
|Ch1 Y-div decrease   |                     |06 31 30 00 00 3b|
|Ch1 Y-div increase   |                     |06 31 31 00 00 3b|
|Ch2 Y-div decrease   |                     |06 31 32 00 00 3b|
|Ch2 Y-div increase   |                     |06 31 33 00 00 3b|
|X-div increase       |                     |06 31 34 00 00 3b|
|X-div decrease       |                     |06 31 35 00 00 3b|
|rotary button ccw    |                     |06 31 3e 00 00 3b|
|rotary button cw     |                     |06 31 3f 00 00 3b|
|rotary button press  |                     |06 37 30 00 00 3b|
|F1                   |                     |06 33 30 00 00 3b|
|F2                   |                     |06 33 31 00 00 3b|
|F3                   |                     |06 33 32 00 00 3b|
|F4                   |                     |06 33 33 00 00 3b|
|TRIGGER              |                     |06 32 30 00 00 3b|
|MEASURE              |                     |06 32 31 00 00 3b|
|CURSOR               |                     |06 32 34 00 00 3b|
|SAVE/LOAD            |                     |06 32 35 00 00 3b|
|USER                 |                     |06 32 37 00 00 3b|
|SCOPE                |                     |06 32 38 00 00 3b|
|METER                |                     |06 32 39 00 00 3b|
|LOGGER               |                     |06 32 41 00 00 3b|
|RUN/STOP             |                     |06 34 31 00 00 3b|
|AUTOSCALE            |                     |06 34 32 00 00 3b|
|MANUAL R.            |                     |06 34 33 00 00 3b|
|TRIG. MODE           |                     |06 37 31 00 00 3b|
