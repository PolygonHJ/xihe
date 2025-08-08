# Technical Sheet

> [!warning]
> Check PCBWay manufacturing. Might be able to route VSTOR over the top of the board between the ground pad and pins.

## XIAO NRF54L15
The schematic for the [XIAO NRF54L15](https://files.seeedstudio.com/wiki/XIAO_nRF54L15/Getting_Start/nRF54L15_Schematic.pdf) is available online.

### Power
The MCU VDD pins are directly connected to the 3V3 pin on the SoM, therefore supply can be provided externally on this pin. There is some bulk capacitance ($10\mu F$ and $100nF$ here).

The potential leakage current that could feed from the energy storage element to the unrequired system (e.g. debugger) comes through the step down DC $VBUS$ to $VSYS\_3V3$. The circuit uses a [TPS62843](https://www.ti.com/lit/ds/symlink/tps62843.pdf) with an advertised leakage current of $0 \rarr 45nA$.


## Harvester
### VBAT_OK
The $VBAT\_OK$ pin is controlled through a PFET to $VSTOR$. Therefore the output is held at $VSTOR$ when the battery charge is above the configured threshold, and pulled to ground when the signal is below the configured threshold (i.e. an external pull resistor is not required).

The $VSTOR$ voltage could range from $1.95V$ to $5.5V$ depending on the capacitor charge. Possible solutions are: NMOS/PMOS level shifter, zener diode, level shifter IC.

The possible options are reviewed below. Based on this, a level shifter will be used.

### Zener
A 3V zener is the simplest solution, but means the input signal will not work when the $VSTOR$ voltage drops below the MCU threshold for a high signal. Available diodes have a leakage current of $~800nA$.

### Load switch
A high side PMOS load switch could be used. Even with a $1M\Omega$ resistor this will have a $1\mu A$ drain when $VBAT$ is high.

An inverting NMOS switch will have the same $1\mu A$ drain. his could be done for ~£0.14.


### Level Shifter
The [AXP1T34](https://www.digikey.co.uk/en/products/detail/nexperia-usa-inc/AXP1T34GMX/22216650) is a 100nA max leakage current load switch for £0.27 with an option for a package down to 1x1mm.

It is an active push-pull device so no pull resistors are required.

