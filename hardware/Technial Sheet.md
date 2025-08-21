# Technical Sheet

> [!note]
> - [ ] Add grounding vias.
> - [ ] Check if diodes are needed between charging circuits

> [!warning]
> External energy modules will need to have matching voltages with the on board super capacitor. The `CAP` jumper may be disconnected to improve available selection, but charging limits are still set by on-board resistors.

> [!warning]
> Sensor modules must work down to 2.5V to match the potential low threshold of operation as the super capacitor depletes.

## XIAO NRF54L15
The schematic for the [XIAO NRF54L15](https://files.seeedstudio.com/wiki/XIAO_nRF54L15/Getting_Start/nRF54L15_Schematic.pdf) is available online.

### Power
The MCU VDD pins are directly connected to the 3V3 pin on the SoM, therefore supply can be provided externally on this pin. There is some bulk capacitance ($10\mu F$ and $100nF$ here).

The potential leakage current that could feed from the energy storage element to the unrequired system (e.g. debugger) comes through the step down DC `VBUS` to `VSYS_3V3`. The circuit uses a [TPS62843](https://www.ti.com/lit/ds/symlink/tps62843.pdf) with an advertised leakage current of $0 \rarr 45nA$.

### User Button
The user button on the module will be used to put the device in pairing mode.


## Harvester
### VBAT_OK
The `VBAT_OK` pin is controlled through a PFET to `VSTOR`. Therefore the output is held at `VSTOR` when the battery charge is above the configured threshold, and pulled to ground when the signal is below the configured threshold (i.e. an external pull resistor is not required).

The `VSTOR` voltage could range from $1.95V$ to $5.5V$ depending on the capacitor charge. Possible solutions are: NMOS/PMOS level shifter, zener diode, level shifter IC.

The possible options are reviewed below. Based on this, a level shifter will be used.

#### Zener
A 3V zener is the simplest solution, but means the input signal will not work when the `VSTOR` voltage drops below the MCU threshold for a high signal. Available diodes have a leakage current of $~800nA$.

#### Load switch
A high side PMOS load switch could be used. Even with a $1M\Omega$ resistor this will have a $1\mu A$ drain when `VBAT` is high.

An inverting NMOS switch will have the same $1\mu A$ drain. his could be done for ~£0.14.


#### Level Shifter
The [AXP1T34](https://www.digikey.co.uk/en/products/detail/nexperia-usa-inc/AXP1T34GMX/22216650) is a 100nA max leakage current load switch for £0.27 with an option for a package down to 1x1mm.

It is an active push-pull device so no pull resistors are required.

## Capacitor
The [EDC224Z5R5H](https://www.digikey.co.uk/en/products/detail/cornell-dubilier-knowles/EDC224Z5R5H/10412911) is a 470mF 5.5V capacitor which sits at approximately the same off board height as the USB connector. Therefore it should have minimal impact on overall board size. 

An approximate running time can be calculated with:  
$t = \frac{C \times (V_{initial}-V_{final})}{I_{avg}} = \frac{470m \times (5.5-2.5)}{50\mu} \approx 7h50min$

This assumes a $50\mu A$ average current draw based on a [forums post online](https://devzone.nordicsemi.com/f/nordic-q-a/122432/nrf54l15-zigbee---high-current-consumption) for a 1minute period zigbee transmittion achieving similar results.

### Possible Substitutes
- [KR-5R5H474-R](https://www.digikey.co.uk/en/products/detail/eaton-electronics-division/KR-5R5H474-R/1556249),
- [SCE5R5H474](https://www.digikey.co.uk/en/products/detail/sruite-electronic-technology-us/SCE5R5H474/13901746)/[AliExpress](https://www.aliexpress.com/item/1005007532080868.html) (**UNCLEAR** if dimensionally compatible),
- [PSB401010H](https://www.maxell.eu/news/maxell-developed-long-life-all-solid-state-battery-based-module-for-industrial-equipment-power-backup/) is a solid state battery which looks like a very interesting and form suitable alternative but does not appear to be available for purchase yet.

## Energy Module Connector
A few options are valid for the energy module connector.

A right angle 0.1" header is a simple, cheap option which should have low risk of short. The [801-83-002-20-001101](https://www.mouser.co.uk/ProductDetail/Preci-dip/801-83-002-20-001101) is an option which fits in the available space on the board.

### Other options
- [Pogo Mates](https://www.aliexpress.com/item/1005003656767263.html) seem good, but this part has poor documented dimensions and high risk of shorting.
- [Pogo Pin](https://precidip.com/product/811-ss-002-20-679101/) something like this could be used on the module side.
