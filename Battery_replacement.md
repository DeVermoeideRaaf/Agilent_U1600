# Battery Replacement
I got the scope without its original battery pack. So I had to decide:
* Using an original NiMH battery pack. It will have a short life because it is not often used.
* Using the already here AA sized Panasonic eneloops as replaceable cells (not as a pack). Up to eight cells with battery holder will fit. The battery holder must be from better quality, the usual cheap (maker) ones have too high contact resistance.
* Using a custom LiPo / Li-ion (NMC) pack. The voltage level differs from the original pack. Li-ion (NMC) will significantly age wether used or not. Cycle life is about 600 cycles. And there is the problem of thermal runaway. Charger needs to be modified.
* Using a custom LFP pack. The voltage level differs from the original pack. Cycle life is about 2000 cycles. Aging effect should be small enough. There is no thermal runaway effect known for this chemistry. Charger needs to be modified.

The original charger seems to be rated 12V and max. 2A. If the DC input of the scope is connected to a 12V voltage source, the scope is operational and the 12V can be measured at the battery connector.
So I guess that the scope can run from a up to 12V battery pack.

## Original battery pack
* six NiMH cells in series (6x 1.2V = 7.2V nominal voltage)
* possible cell type: GP 450LAH (size: 4/3A, 4/3R23; 18.3x67mm; capacity: 4.5Ah; about 32Wh)
* 10K thermistor for temperature sensing (cell protection / fast charge control)
* connector type: tbd.

## Seperate NiMH cells
* eight Panasonic eneloop in series (8x 1.2V = 9.6V; capacity: 1.5Ah; about 14Wh)
* Battery holder must to have low contact resistance
* possible battery holder (not tested): Keystone 1013 or Keystone 1015
* PCB as carrier for the cells
* Changing the cells is a bit cumbersome because of the stand

## LFP replacement
* option 1: six LFP cells 3S2P (3x 3.2V = 9.6V nominal voltage; 8.1V...10.8V range; about 38Wh)
* option 2: four LFP cells in series (4x 3.2V = 12.8V nominal voltage; 10.8V...14.4V range; about 25Wh)
* possible cell type: generic IFR18650 (about 2Ah)
* BMS needed, balancer possible but not necessary
* when using six cells the space for BMS PCB is very limited -> no standard PCB, development of own BMS PCB necessary
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

## External charger / wall adapter
The original charger (U1570A AC power adapter) seems to be a 12V 2A constant voltage converter. This is not usable with the LPF battery pack.

At the moment a current-limited step-down converter set to 14.0V and 1 Ampere is used (cheap LM2596S board). Current limit is needed because the internal charger only limits the current when the pack is not completely empty (need to be checked).
The external charger needs to be set to 14V to get 13.6V on the battery pack. This will lead to 13mA charging current at 13.6V pack voltage.
A current threshold circuit to end charging at this low current needs to be added.
Better option: Using an LFP charger.<br>
More better option: Connecting DC input directly to the battery pack for faster charging.

### LM2576S problems
The CV/CC curve seems to be not steep enough resulting in long charge time with lower than set current. Charger needs to be improved.


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
Measured voltage levels at real usage:
* third bar off at 12.5V
* second bar off at 12.24V
* first bar off at 11.66V
* Battery discharged warning at 10.92V
* deep discharge switch off at 10.3V (not sure if protection PCB or scope)

## Charging circuit modification
[MAX713](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX712-MAX713.pdf) based charger.
Charger can deliver constant current. Charger is set to eight cells. Therefore:
* PGM0: open
* PGM1: Bat-
* PGM2: Bat-
* PGM3: Bat-

This does not work. Charging takes 7 hours.

Second try with 9 cell config and disabled slope detection:
* PGM0: Ref
* PGM1: V+
* PGM2: V+
* PGM3: Bat-

No difference. This may be a problem of the LM2567S "charger".
