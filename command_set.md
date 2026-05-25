
# Remote control command set
Actual state: Sending commands is well known. Analysis of the response needs to be done.

## Hardware connection
The scope is connected to the host computer running linux via an Micro-USB to USB-A cable.<br>
lsusb:
```
Bus 003 Device 010: ID 0403:6001 Future Technology Devices International, Ltd FT232 Serial (UART) IC
```
dmesg:
```
[  691.432770] usb 3-2.3.4.3: new full-speed USB device number 10 using xhci_hcd
[  691.529380] usb 3-2.3.4.3: New USB device found, idVendor=0403, idProduct=6001, bcdDevice= 4.00
[  691.529389] usb 3-2.3.4.3: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[  691.529393] usb 3-2.3.4.3: Product: U1600A Handheld Oscilloscope
[  691.529397] usb 3-2.3.4.3: Manufacturer: Agilent
[  691.529399] usb 3-2.3.4.3: SerialNumber: XXXXXXXX
[  691.638164] usbcore: registered new interface driver usbserial_generic
[  691.638188] usbserial: USB Serial support registered for generic
[  691.652049] usbcore: registered new interface driver ftdi_sio
[  691.652068] usbserial: USB Serial support registered for FTDI USB Serial Device
[  691.652327] ftdi_sio 3-2.3.4.3:1.0: FTDI USB Serial Device converter detected
[  691.652400] usb 3-2.3.4.3: Detected FT232B
[  691.654062] usb 3-2.3.4.3: FTDI USB Serial Device converter now attached to ttyUSB0
```
Note: The serialNumber is X-ed by the author ;-)

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
Most commands consist of six bytes.
Usually command starts with hex 06 and ends with hex 3b.
Button names are taken from software PC Link V2.51 (screenshot needs to be added).

![The screenshot shows the scope surface as its rendered in PC Link 2.51](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/PCLink251-ScopeSurface.png "Scope surfave shown in PC Link 2.51")


|button name          |function             |command (hex)    |response                                |
|---------------------|---------------------|----------------:|---------------------------------------:|
|Power on             |lock local buttons   |53 54 41 52 54 3b|00 50 45 52 4D 49 54 00 data 00 4F 4B 00|
|Power off            |unlock local buttons |   53 54 4f 50 3b|                                45 4E 44|
|n.a.                 |send screen copy/data|06 00 00 00 00 3b|                     00 data 00 4F 4B 00|
|Ch1 Y-div decrease   |fewer volts per div  |06 31 30 00 00 3b|                     00 data 00 4F 4B 00|
|Ch1 Y-div increase   |more volts per div   |06 31 31 00 00 3b|                     00 data 00 4F 4B 00|
|Ch2 Y-div decrease   |fewer volts per div  |06 31 32 00 00 3b|                     00 data 00 4F 4B 00|
|Ch2 Y-div increase   |more volts per div   |06 31 33 00 00 3b|                     00 data 00 4F 4B 00|
|X-div increase       |more time per div    |06 31 34 00 00 3b|                     00 data 00 4F 4B 00|
|X-div decrease       |fewer time per div   |06 31 35 00 00 3b|                     00 data 00 4F 4B 00|
|rotary button ccw    |                     |06 31 3e 00 00 3b|                     00 data 00 4F 4B 00|
|rotary button cw     |                     |06 31 3f 00 00 3b|                     00 data 00 4F 4B 00|
|rotary button press  |                     |06 37 30 00 00 3b|                     00 data 00 4F 4B 00|
|F1                   |funtion key 1        |06 33 30 00 00 3b|                     00 data 00 4F 4B 00|
|F2                   |funtion key 2        |06 33 31 00 00 3b|                     00 data 00 4F 4B 00|
|F3                   |funtion key 3        |06 33 32 00 00 3b|                     00 data 00 4F 4B 00|
|F4                   |funtion key 4        |06 33 33 00 00 3b|                     00 data 00 4F 4B 00|
|TRIGGER              |                     |06 32 30 00 00 3b|                     00 data 00 4F 4B 00|
|MEASURE              |                     |06 32 31 00 00 3b|                     00 data 00 4F 4B 00|
|CURSOR               |                     |06 32 34 00 00 3b|                     00 data 00 4F 4B 00|
|SAVE/LOAD            |                     |06 32 35 00 00 3b|                     00 data 00 4F 4B 00|
|USER                 |                     |06 32 37 00 00 3b|                     00 data 00 4F 4B 00|
|SCOPE                |                     |06 32 38 00 00 3b|                     00 data 00 4F 4B 00|
|METER                |                     |06 32 39 00 00 3b|                     00 data 00 4F 4B 00|
|LOGGER               |                     |06 32 41 00 00 3b|                     00 data 00 4F 4B 00|
|RUN/STOP             |                     |06 34 31 00 00 3b|                     00 data 00 4F 4B 00|
|AUTOSCALE            |                     |06 34 32 00 00 3b|                     00 data 00 4F 4B 00|
|MANUAL R.            |                     |06 34 33 00 00 3b|                     00 data 00 4F 4B 00|
|TRIG. MODE           |                     |06 37 31 00 00 3b|                     00 data 00 4F 4B 00|

Note 1: The data bytes depend on the actual running function/mode (scope, meter,...).<br>
Note 2: The menu options controlled by the function keys are not transmitted via the response. So the remote control software must know the menu structure.
Note 3: There seems to be a buffer for the response. For example: If scope is running and METER is send, the response contains scope data. The next response contains meter data.

### Examples
![The screenshot shows PulseView record from sending the START-command and part of the response](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/U1604A_cmd_PowerOn.png "PulseView decoding the PowerOn command and the response")

![The screenshot shows PulseView record from sending the update-screen-command and part of the response](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/U1604A_cmd_ScreenUpdate.png "PulseView decoding the ScreenUpdate command and the response")

![The screenshot shows PulseView record from sending the STOP-command and the response](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/U1604A_cmd_PowerOff.png "PulseView decoding the PowerOff command and the response")

