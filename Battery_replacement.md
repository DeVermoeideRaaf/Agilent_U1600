# Battery Replacement
I got the scope without its original battery pack. So I had to decide:
* Using an original NiMH battery pack. It will have a short life because it is not often used.
* Using the already here AA sized Panasonic eneloops as replaceable cells (not as a pack). Mechanical fitting was the problem.
* Using a custom LiPo / Li-ion (NMC) pack. The voltage level differs from the original pack. Li-ion (NMC) will significantly age wether used or not. Cycle life is about 600 cycles. And there is the problem of thermal runaway.
* Using a custom LFP pack. The voltage level differs from the original pack. Cycle life is about 2000 cycles. Aging effect should be small enough. There is no thermal runaway effect known for this chemistry.

The original charger delivers 12V. If the DC input of the scope is connected to a 12V voltage source, the scope is operational and the 12V can be measured at the battery connector. So I guess that the scope can run from a up to 12V battery pack.

## Original battery pack
* six NiMH cells in series (6x 1.2V = 7.2V nominal voltage)
* possible cell type: GP 450LAH (size: 4/3A, 4/3R23; 18.3x67mm; capacity: 4.5Ah)
* 10K thermistor for temperature sensing (cell protection or charge control)
* connector type: tbd.

## LFP replacement
* six LFP cells 3S2P (3x 3.2V = 9.6V nominal voltage; 8.1V...10.8V range)
* four LFP cells in series (4x 3.2V = 12.8V nominal voltage; 10.8V...14.4V range)
* possible cell type: generic IFR18650 (about 2Ah)
* BMS needed, balancer possible but not necessary
* when using six cells the space for BMS PCB is very limited -> no standard PCB, developing own BMS PCB necessary
* when using four cells there is space for a standard BMS PCB

## New LFP pack
Modifying a pack was cheaper and easier than buying cells and BMS seperatly.
Used pack: [EREMIT 12V 2Ah 25.6Wh](https://www.eremit.de/p/12v-2ah-flacher-lifepo4-mit-bms)

Label:
![Label of LFP pack](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/LiFePO4-Akku-Typ.jpg "Label")

During dismantling the pack: This is the BMS pcb. You can see the original arrangement of the for cells and the BMS.
![BMS of partly dismanteled LFP pack](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/LiFePO4-Akku-BMS.jpg "BMS of partly dismantelt pack")

This is how the cells will be arranged in the future. The pack is split into two halfes and folded. No cell connector needs to be opened.
![rearranged cells](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/LiFePO4-Akku-vorbereitet.jpg "rearranged cells")

The cells are placed into heat shrink tube. The BMS will be laying next to the cells. Not the best solution. Some foam will helb to survive vibration.
![LFP pack installed in battery compartment](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/LiFePO4-Akku-eingebaut.jpg "pack installed")

## Battery gauge
The battery gauge is calibrated for the 7.2V NiMH pack. Now the state is even full if the BMS is right before switching of to prevent deep discharge.
The relevant parts are:<br>
* U509 (op 2, working as buffer amp, output directly to U505 pin 75)
* R101 (10k)
* R125 (100k)
* R126 (40.2k)
* U505 (AI0, pin 75; main processor; Samsung S3C44B0X01L)

![Schematic of battery gauge](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/Battery-gauge.png "Schematic of battery gauge")<br>
Note: The "semiconductor switch" may be some transistor which disconnects a power bus if the scope is switched off. Only the voltage drop when switched on is relevant in this case. Thats why it's shown as voltage source.

Voltage levels at U509 pin 5 to GND (not very accurate, values seems to vary a bit?):
* third bar: 2.123V...2.108V
* second bar: 2.070V...2.062V
* first bar: 1.984V...1.986V
* Battery discharged warning: 1.847V

If the new battery discharged warning should come up at about 10.8V R126 should be changed to 20.65k (20.5k + 150R).<br>
<br>
Test setup (20k + 470R + 180R):
![Three resistors as replacement for R126](https://github.com/DeVermoeideRaaf/Agilent_U1600/blob/main/resources/Batterielevel-mod-provisorisch.jpg "Three resistors as replacement for R126")<br>
<br>
Measured voltage levels (need to be confirmed at real usage):
* third bar: 12.48V...12.4V
* second bar: 12.15V...12.1V
* first bar: 11.65V...11.62V
* Battery discharged warning: 10.85V

## Charging circuit modification
[MAX713](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX712-MAX713.pdf) based charger.
Charger can deliver constant current. Charger is set to eight cells. Therefore:
* open PGM0
* connect PGM1 to battery minus

Charging needs to be documentated (green LED, threshhold about 100mA,...)

## External charger / wall adapter
The original charger (U1570A AC power adapter) is a 12V 2A constant voltage converter. This is not usable with the LPF battery pack.

At the moment a current-limited step-down converter set to 13.4V and 1 Ampere is used. Current limit is needed because the internal charger only limits the current when the pack is not completely empty (need to be checked).
The external charger needs to be set to 14V to get 13.6V on the battery pack. This will lead to 13mA charging current at 13.6V pack voltage.
A current threshold circuit to end charging at this low current needs to be added.
Better option: Using an LFP charger.<br>
More better option: Connecting DC input direcly to the battery pack for faster charging.

