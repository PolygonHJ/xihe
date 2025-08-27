# Technical Sheet

> [!note]
> - [ ] Add grounding vias.
> - [ ] Check spreadsheet from PCBWay and update KiCad BoM to match

> [!warning]
> External energy modules will need to have matching voltages with the on board super capacitor. The `CAP` jumper may be disconnected to improve available selection, but charging limits are still set by on-board resistors.

> [!warning]
> Sensor modules must work down to 2.5V to match the potential low threshold of operation as the super capacitor depletes.

## Power
Part selections detailed below. The following assumptions/decisions have been made:
- $50\mu A$ average power consumption (assumption),
- $1mW/cm^2$ power from ambient indoor lightning.

### Charging
Using the [KXOB25-03X4F-TR](https://waf-e.dubudisk.com/anysolar.dubuplus.com/techsupport@anysolar.biz/O18AzRx/DubuDisk/www/Gen3/KXOB25-03X4F%20DATA%20SHEET%2020210127.pdf) would generated approximately $0.25 \times 1m \times 1.39 = 347.5\mu W$.

The capacitor charge time can be calculated with:  
$Pt = \frac{1}{2}C(V_{final}-V_{initial})^2 \rightarrow t = \frac{CV^2}{2 \eta P}$

Where:
- $\eta$: Conversion efficiency; 80%,
- $V_{final}$: Final capacitor voltage; 5.3 based on set limits,
- $V_{initial}$: Initial capacitor voltage, 0V empty, or 2.5V for active system,
- $C$: Energy storage device capacitance; 470mF,
- $P$: Power from solar cell, $347.5\mu W$.

For an empty capacitor:  
$t = \frac{0.47 \times 5.3^2}{2 \times 0.8 \times 0.348} = 6h36min$ to fully charge.

For the running systme ($V_{initial}=2.5$):  
$t = \frac{0.47 \times (5.3-2.5)^2}{2 \times 0.8 \times 0.348} = 1h50min$ to fully charge.

> [!note] Note
> Ignores the sleep mode discharge.

### Discharging
For the system to fully use the available energy in the capacitor:  
$t = \frac{C \times (V_{initial}-V_{final})}{I_{avg}} = \frac{470m \times (5.3-2.5)}{50\mu} \approx 7h20min$

This assumes a $50\mu A$ average current draw based on a [forums post online](https://devzone.nordicsemi.com/f/nordic-q-a/122432/nrf54l15-zigbee---high-current-consumption) for a 1minute period zigbee transmittion achieving similar results.

Using the assumptions of:
- $40ms @ 10mA$ every 5minutes,
- $100ms @ 1mA$ every 5minutes,
- $10\mu A$ standby consumption.

$I_{avg} = \frac{40m \times 10m}{300} + \frac{100m \times 1m}{300} + 10\mu = 12\mu A$  

$t = \frac{470m \times (5.3-2.5)}{12\mu} \approx 30h28min$


## XIAO NRF54L15
The schematic for the [XIAO NRF54L15](https://files.seeedstudio.com/wiki/XIAO_nRF54L15/Getting_Start/nRF54L15_Schematic.pdf) is available online.

### Power
The MCU VDD pins are directly connected to the 3V3 pin on the SoM, therefore supply can be provided externally on this pin. There is some bulk capacitance ($10\mu F$ and $100nF$ here).

The potential leakage current that could feed from the energy storage element to the unrequired system (e.g. debugger) comes through the step down DC `VBUS` to `VSYS_3V3`. The circuit uses a [TPS62843](https://www.ti.com/lit/ds/symlink/tps62843.pdf) with an advertised leakage current of $0 \rarr 45nA$.

### User Button
The user button on the module will be used to put the device in pairing mode.

## Solar Cell
The [KXOB25-03X4F-TR](https://waf-e.dubudisk.com/anysolar.dubuplus.com/techsupport@anysolar.biz/O18AzRx/DubuDisk/www/Gen3/KXOB25-03X4F%20DATA%20SHEET%2020210127.pdf) was chosen for its dimensions and cost that would reasonably fit on the board. See the power section for information on generated power.

The device has:
-  a solar surface area of $0.528 \times 0.659 \times 4 = 1.39cm^2$,
-  25% efficiency.

## Harvester



> [!warning]
> Continue...

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

# Ambient Sense Module  
The ambient sense module is an initial design with a few different sensors to help with development.

The board has a reed switch (for open/close detection), a humidity/temperature sensor and a light sensor. The sensors will use I2C for simplicity with a $10k \Omega$ pull-up.

## Reed
[RI80SMDM-0510-J1](https://comus-intl.com/wp-content/uploads/2017/04/RI-80SMDM.pdf) is a normally open small formfactor reed switch. Ideally a lower profile switch can be found but this is suitable for now. It is connected to a GPIO pin which is to be configured an an input. It is default pulled down, and pulls high on switch closure.


## Light  
[OPT3002](https://www.ti.com/lit/ds/symlink/opt3002.pdf) is a light to digital sensor that can measure light in the range 300nm to 1000nm at power levels from $1.2nW/cm^2$ to $10mW/cm^2$.

The sensor was chosen for it's I2C, low voltage and low power operation:
- $V_{supply}$ down to 1.6V,
- Quiescent current in shutdown <500nA.

It can be run in continuous or single shot mode. Single shot mode will be used to reduce overall power consumption.


## Temperature / Humidity
[HDC2010](https://www.ti.com/lit/ds/symlink/hdc2010.pdf) is a humidity and temperature digital sensor. It can measure for $-40\degree C$ to $125\degree C$ and $0-100\%$. 

The sensor was chosen for it's I2C, low voltage and low power operation:
- $V_{supply}$ down to 1.6V,
- Quiescent current in shutdown <100nA.

