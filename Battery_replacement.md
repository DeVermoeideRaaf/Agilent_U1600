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
* four LFP cells in series (4x 3.2V = 12.8V nominal voltage; 10.8V...14.4V range)
* possible cell type: generic IFR18650 (about 2Ah)
* BMS needed, balancer possible but not necessary

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
I need to check the voltage level vs. battery state indicator. Then I need to find out the PCB traces and install a voltage divider.

## Charging circuit modification
[MAX713](https://www.analog.com/media/en/technical-documentation/data-sheets/MAX712-MAX713.pdf) based charger.
Charger can deliver constant current. Charger is set to eight cells. Therefore:
* open PGM0
* connect PGM1 to battery minus

Charging needs to be documentated (green LED, threshhold about 100mA,...)

## External charger
Current-limited step-down converter set to 13.4V and 1 Ampere. Current limit is needed because the internal charger only limits the current when the pack is not completely empty.
The external charger needs to be set to 14V to get 13.6V on the battery pack. This will lead to 13mA charging current at 13.6V pack voltage.
