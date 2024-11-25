General-purpose I/O (GPIO) pins can be individually configured to operate in a digital I/O mode or in an alternate function (AF) mode, which maps a signal associated with an enabled peripheral to that GPIO. Each GPIO supports dynamic switching between I/O mode and alternate function mode. Configuring a pin for an alternate function supersedes its use as a digital I/O; however, the state of the GPIO is still readable through the [GPIOn_IN](#gpio-port-input-register) register.

The electrical characteristics of a GPIO pin are identical whether the pin is configured as an I/O or as an alternate function, except where explicitly noted in the data sheet electrical characteristics tables.

The GPIO are divided logically into ports of 32 pins. Package variants may not implement all pins of a specific 32-bit GPIO port.

Each port pin has an interrupt function that can be independently enabled and configured as a level-sensitive or edge-sensitive interrupt. All GPIOs of a given port share the same interrupt vector as detailed in GPIO Interrupt Handling.

*Note: The register set used to control the GPIO are identical across multiple Analog Devices microcontrollers; however, the behavior of several registers varies depending on the specific device. The behavior of the registers should not be assumed to be the same from one device to a different device. Specifically the registers [GPIOn_PADCTRL0](#gpio-port-pad-configuration1-register), [GPIOn_PADCTRL1](#gpio-port-pad-configuration2-register), [GPIOn_HYSEN](#gpio-port-hysteresis-enable-register), [GPIOn_SRSEL](#gpio-port-slew-rate-select-register), [GPIOn_DS0](#gpio-port-output-drive-strength-bit0-register), [GPIOn_DS1](#gpio-port-output-drive-strength-bit1-register), and [GPIOn_VSSEL](#gpio-port-voltage-select-register) are device-dependent in their usage. GPIO3 is controlled differently and has different features than the other GPIO ports in the MAX78000. See MCR_GPIO3_CTRL for details on using GPIO3.*

The features for each GPIO pin include:

- Full CMOS outputs with configurable drive strength settings
- Input modes/options:
    - High impedance
    - Weak pullup/pulldown
    - Strong pullup/pulldown
- Output data can be from the [GPIOn_OUT](#gpio-port-output-register) register or an enabled peripheral.
- Input data can be read from the [GPIOn_IN](#gpio-port-input-register) input register or the enabled peripheral.
- Bit set and clear registers for efficient bit-wise write access to the pins and configuration registers.
- Wake from low-power modes using edge-triggered inputs.
- Selectable GPIO voltage supply for GPIO0, GPIO1, and GPIO2:
    - V<sub>DDIO</sub>
    - <p>V<sub>DDIOH</sub>
- Selectable interrupt events:
    - Level triggered low
    - Level triggered high
    - Edge triggered rising edge.
    - Edge triggered falling edge.
    - Edge triggered rising and falling edge.
- All GPIO pins default to input mode with weak-pullup during power-on-reset events.


### Instances
<a href="#table6-1-max78000-gpio-pin-count">Table 6‑1</a> shows the number of GPIO available on each IC package. Some packages and part numbers do not implement all bits of a 32-bit GPIO port. Register fields corresponding to unimplemented GPIO contain indeterminate values and should not be modified.

*Table6-1: MAX78000 GPIO Pin Count* 
<a name="table6-1-max78000-gpio-pin-count"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
<th>Package</th>
<th>GPIO</th>
<th>PINS</th>
</tr>
</thead>
<tbody>
<tr>
    <td rowspan="4">81-CTBGA</td>
    <td>GPIO0[30:0]</td>
    <td>31</td>
</tr>
<tr>
    <td>GPIO1[9:0]</td>
    <td>10</td>
</tr>
<tr>
    <td>GPIO2[7:0]</td>
    <td>8</td>
</tr>
<tr>
    <td>GPIO3[1:0]<sup>+</sup></td>
    <td>2</td>
</tr>
</tbody>
</table>

*Note: See [Power Sequencer Registers (PWRSEQ)](system-power-clocks-reset.md#power-sequencer-registers-pwrseq) for details on using GPIO3.*

*Note: Refer to the device data sheet for descriptions of each GPIO port pin's alternate functions.*

## Configuration
Each device pin is individually configurable as a GPIO or an alternate function. The correct alternate function setting must be selected for each pin of a given multi-pin peripheral for proper operation.

### Power-On-Reset Configuration
All I/O default to GPIO mode during a POR event as high impedance inputs except the SWDIO and SWDCLK pins. After a POR, the SWD is enabled by default with AF1 selected by hardware. See the Bootloader chapter for exceptions.

Following a POR event, all GPIO, except device pins that have the SWDIO and SWDCLK function, are configured with the following default settings:

- GPIO mode enabled
    - [GPIOn_EN0](#gpio-port-configuration-enable-bit0-register).*en[pin]* = 1
    - [GPIOn_EN1](#gpio-port-configuration-enable-bit1-register).*en[pin]* = 0
    - [GPIOn_EN2](#gpio-port-configuration-enable-bit2-register).*en[pin]* = 0
- Pullup/pulldown disabled, I/O in Hi-Z mode
    - [GPIOn_PADCTRL0](#gpio-port-pad-configuration1-register).*mode[pin]* = 0
    - [GPIOn_PADCTRL1](#gpio-port-pad-configuration2-register).*mode[pin]*
- Output mode disabled
    - [GPIOn_OUTEN](#gpio-port-output-enable-register).*en[pin]* = 0
- Interrupt disabled
    - [GPIOn_INTEN](#gpio-port-interrupt-enable-register).*en[pin]* = 0


### Serial Wire Debug Configuration

Perform the following steps to configure the SWDIO and SWDCLK device pins for SWD mode:

1. Set the device pin P0.28 for AF1 mode:

    a. [GPIOn_EN0](#gpio-port-configuration-enable-bit0-register).*config[28]* = 0
    
    b. [GPIOn_EN1](#gpio-port-configuration-enable-bit1-register).*config[28]* = 0

    c. [GPIOn_EN2](#gpio-port-configuration-enable-bit2-register).*config[28]* = 0

2. Set device pin P0.29 for AF1 mode:

    a. [GPIOn_EN0](#gpio-port-configuration-enable-bit0-register).*config[29]* = 0

    b. [GPIOn_EN1](#gpio-port-configuration-enable-bit1-register).*config[29]* = 0

    c. [GPIOn_EN2](#gpio-port-configuration-enable-bit2-register).*config[29]* = 0

*Note: To use the SWD pins in GPIO mode, set the desired GPIO pins for SWD AF and disable the SWD (GCR_SYSCTRL.swd_dis = 1).*

### Pin Function Configuration
[Table 6‑2](#table6-2-max78000-gpio-pin-function-configuration) depicts the bit settings for the [GPIOn_EN0](#gpio-port-configuration-enable-bit0-register), [GPIOn_EN1](#gpio-port-configuration-enable-bit1-register), and [GPIOn_EN2](#gpio-port-configuration-enable-bit2-register) registers to configure a GPIO port pin's function. Each of the bits within these registers represents the configuration of a single pin on the GPIO port. For example, [GPIOn_EN0](#gpio-port-configuration-enable-bit0-register).*config[25]*, [GPIOn_EN1](#gpio-port-configuration-enable-bit1-register).*config[25]*, and [GPIOn_EN2](#gpio-port-configuration-enable-bit2-register).*config[25]* all represent configuration for device pin P0.25. See Table 6‑5 for a detailed example of how each of these bits applies to each GPIO device pin.

*Table 6-2: MAX78000 GPIO Pin Function Configuration*
<a name="table6-2-max78000-gpio-pin-function-configuration"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
<th>MODE</th>
<th><a href="#gpio-port-configuration-enable-bit0-register">GPIOn_EN0</a>.<em>config[pin]</em></th>
<th><a href="##gpio-port-configuration-enable-bit1-register">GPIOn_EN1</a>.<em>config[pin]</em></th>
<th><a href="#gpio-port-configuration-enable-bit2-register">GPIOn_EN2</a>.<em>config[pin]</em></th>
</tr>
</thead>
<tbody>
<tr>
    <td>AF1</td>
    <td>0</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>AF2</td>
    <td>0</td>
    <td>1</td>
    <td>0</td>
</tr>
<tr>
    <td>I/O (transition to AF1)</td>
    <td>1</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>I/O (transition to AF2)</td>
    <td>1</td>
    <td>1</td>
    <td>0</td>
</tr>
</tbody>
</table>

### Input Mode Configuration
[Table 6‑3](#table6-3-max78000-input-mode-configuration) depicts the bit settings for the digital I/O input mode. Each of the bits within these registers represents the configuration of a single pin on the GPIO port. For example, [GPIOn_PADCTRL1](#gpio-port-pad-configuration2-register).*config[25]*, [GPIOn_PADCTRL0](#gpio-port-pad-configuration1-register).*config[25]*, [GPIO0_PS](#gpio-port-pulldown-pullup-strength-select-register).*pull_sel[25]*, and [GPIO0_VSSEL](#[GPIOn_VSSEL](#gpio-port-voltage-select-register)).*v_sel[25]* all represent configuration for device pin P0.25. See Table 6‑8 for a detailed example of how each of these bits applies to each GPIO device pin. Refer to the device data sheet for details of specific electrical characteristics.

*Table 6-3: MAX78000 Input Mode Configuration*
<a name="table6-3-max78000-input-mode-configuration"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th rowspan="2">Input Mode</th>
    <th colspan="2" align="center">Mode Select</th>
    <th align="center">Pullup/Pulldown Strength</th>
    <th align="center">Power Supply</th>
</tr>
<tr>
    <th><a href="##gpio-port-pad-configuration2-register">GPIOn_PADCTRL1</a>.<em>config[pin]</em></th>
    <th><a href="#gpio-port-pad-configuration1-register">GPIOn_PADCTRL0</a>.<em>config[pin]</em></th>
    <th><a href="#gpio-port-pulldown-pullup-strength-select-register">GPIOn_PS</a>.<em>pull_sel[pin]</em></th>
    <th><a href="#gpio-port-voltage-select-register">GPIOn_VSSEL</a>.<em>v_sel[pin]</em></th>
</tr>
</thead>
<tbody>
<tr>
    <td>High-impedance</td>
    <td>0</td>
    <td>0</td>
    <td>N/A</td>
    <td>N/A</td>
</tr>
<tr>
    <td>Weak Pullup to V<sub>DDIO</sub> (1MΩ)</td>
    <td>0</td>
    <td>1</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>Strong Pullup to V<sub>DDIO</sub> (25KΩ)</td>
    <td>0</td>
    <td>1</td>
    <td>1</td>
    <td>0</td>
</tr>
<tr>
    <td>Weak Pulldown to V<sub>DDIOH</sub> (1MΩ)</td>
    <td>1</td>
    <td>0</td>
    <td>0</td>
    <td>1</td>
</tr>
<tr>
    <td>Strong Pulldown to V<sub>DDIOH</sub> (25KΩ)</td>
    <td>1</td>
    <td>0</td>
    <td>1</td>
    <td>1</td>
</tr>
<tr>
    <td>Reserved</td>
    <td>1</td>
    <td>1</td>
    <td>N/A</td>
    <td>N/A</td>
</tr>
</tbody>
</table>

### Output Mode Configuration
[Table 6‑4](#table6-4-max78000-output-mode-configuration) shows the configuration options for digital I/O in output mode. Each of the bits within these registers represents the configuration of a single pin on the GPIO port. For example, GPIO2_DS0.config[25], GPIO2_DS1.config[25], and GPIO2_VSSEL.v_sel[25] all represent configuration for GPIO port 2 pin 25 (device pin P0.25). See Table 6‑8 for a detailed example of how each of these bits applies to each GPIO device pin. Refer to the device data sheet for details of specific electrical
characteristics.

*Table 6-4: MAX78000 Output Mode Configuration*
<a name="table6-4-max78000-output-mode-configuration"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
<th rowspan="2">Input Mode</th>
<th colspan="2" align="center">Drive Strength</th>
<th align="center">Power Supply</th>
</tr>
<tr>
    <th><a href="#gpio-port-output-drive-strength-bit1-register">GPIOn_DS1.<em>config[pin]</em></th>
    <th><a href="#gpio-port-output-drive-strength-bit0-register">GPIOn_DS0</a>.<em>config[pin]</em></th>
    <th><a href="#gpio-port-voltage-select-register">GPIOn_VSSEL</a>.<em>v_sel[pin]</em></th>
</tr>
</thead>
<tbody>
<tr>
    <td>Output Drive Strength 0, V<sub>DDIO</sub> Supply</td>
    <td>0</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>Output Drive Strength 1, V<sub>DDIO</sub> Supply</td>
    <td>0</td>
    <td>1</td>
    <td>0</td>
</tr>
<tr>
    <td>Output Drive Strength 2, V<sub>DDIO</sub> Supply</td>
    <td>1</td>
    <td>0</td>
    <td>0</td>
</tr>
<tr>
    <td>Output Drive Strength 3, V<sub>DDIO</sub> Supply</td>
    <td>1</td>
    <td>1</td>
    <td>0</td>
</tr>
<tr>
    <td>Output Drive Strength 0, V<sub>DDIOH</sub> Supply</td>
    <td>0</td>
    <td>0</td>
    <td>1</td>
</tr>
<tr>
    <td>Output Drive Strength 1, V<sub>DDIOH</sub> Supply</td>
    <td>0</td>
    <td>1</td>
    <td>1</td>
</tr>
<tr>
    <td>Output Drive Strength 2, V<sub>DDIOH</sub> Supply</td>
    <td>1</td>
    <td>0</td>
    <td>1</td>
</tr>
<tr>
    <td>Output Drive Strength 3, V<sub>DDIOH</sub> Supply</td>
    <td>1</td>
    <td>1</td>
    <td>1</td>
</tr>
</tbody>
</table>

Each GPIO port is assigned a dedicated interrupt vector, as shown in <a href="#table6-9-max78000-gpio-port-interrupt-vector-mapping">Table 6‑9</a>.

### Reference Tables
The tables in this section provide example references for register bit assignment to configure a device's GPIO port 0 pins. Other GPIO port pins are configured similarly using the respective GPIO1 or GPIO2 registers.

*Table 6-5: MAX78000 GPIO0 Alternate Function Configuration Reference*
<a name="table6-5-max78000-gpio0-alternate-function-configuration-reference"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th>Device Pin</th>
    <th colspan="3" align="center">Alternate Function Configuration Bits</th>
</tr>
</thead>
<tbody>
<tr>
    <td>P0.0</td>
    <td><em>GPIO0_EN0.config[0]</em></td>
    <td><em>GPIO0_EN1.config[0]</em></td>
    <td><em>GPIO0_EN2.config[0]</em></td>
</tr>
<tr>
    <td>P0.1</td>
    <td><em>GPIO0_EN0.config[1]</em></td>
    <td><em>GPIO0_EN1.config[1]</em></td>
    <td><em>GPIO0_EN2.config[1]</em></td>
</tr>
<tr>
    <td>…</td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
</tr>
<tr>
    <td>P0.30</td>
    <td><em>GPIO0_EN0.config[30]</em></td>
    <td><em>GPIO0_EN1.config[30]</em></td>
    <td><em>GPIO0_EN2.config[30]</em></td>
</tr>
<tr>
    <td>P0.31</td>
    <td><em>GPIO0_EN0.config[31]</em></td>
    <td><em>GPIO0_EN1.config[31]</em></td>
    <td><em>GPIO0_EN2.config[31]</em></td>
</tr>
</tbody>
</table>

*Table 6-6: MAX78000 GPIO0 Output/Input Configuration Reference*
<a name="table6-6-max78000-gpio0-output-input-configuration-reference"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th align="center">Device Pin</th>
    <th align="center">GPIO Output Enable</th>
    <th align="center">GPIO Output Write</th>
    <th align="center">GPIO Input Enable</th>
    <th align="center">GPIO Input Read</th>
</tr>
</thead>
<tbody>
<tr>
    <td>P0.0</td>
    <td><em>GPIO0_OUTEN.en[0]</em></td>
    <td><em>GPIO0_OUT.level[0]</em></td>
    <td><em>GPIO0_INEN.en[0]</em></td>
    <td><em>GPIO0_IN.level[0]</em></td>
</tr>
<tr>
    <td>P0.1</td>
    <td><em>GPIO0_OUTEN.en[1]</em></td>
    <td><em>GPIO0_OUT.level[1]</em></td>
    <td><em>GPIO0_INEN.en[1]</em></td>
    <td><em>GPIO0_IN.level[1]</em></td>
</tr>
<tr>
    <td>…</td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
</tr>
<tr>
    <td>P0.30</td>
    <td><em>GPIO0_OUTEN.en[30]</em></td>
    <td><em>GPIO0_OUT.level[30]</em></td>
    <td><em>GPIO0_INEN.en[30]</em></td>
    <td><em>GPIO0_IN.level[30]</em></td>
</tr>
<tr>
    <td>P0.31</td>
    <td><em>GPIO0_OUTEN.en[31]</em></td>
    <td><em>GPIO0_OUT.level[31]</em></td>
    <td><em>GPIO0_INEN.en[31]</em></td>
    <td><em>GPIO0_IN.level[31]</em></td>
</tr>
</tbody>
</table>

*Table 6-7: MAX78000 GPIO0 Interrupt Configuration Reference*
<a name="table6-7-max78000-gpio0-interrupt-configuration-reference"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th align="center">Device Pin</th>
    <th align="center">Enable</th>
    <th align="center">Status</th>
    <th align="center">Dual Edge</th>
    <th align="center">Polarity</th>
    <th align="center">Trigger</th>
    <th align="center">Wakeup</th>
</tr>
</thead>
<tbody>
<tr>
    <td>P0.0</td>
    <td><em>GPIO0_INTEN.en[0]</em></td>
    <td><em>GPIO0_INTFL.config[0]</em></td>
    <td><em>GPIO0_DUALEDGE.dualedge[0]</em></td>
    <td><em>GPIO0_INTPOL.pol[0]</em></td>
    <td><em>GPIO0_INTMODE.gpio_intmode[0]</em></td>
    <td><em>GPIO0_WKEN.en[0]</em></td>
</tr>
<tr>
    <td>P0.1</td>
    <td><em>GPIO0_INTEN.en[1]</em></td>
    <td><em>GPIO0_INTFL.config[1]</em></td>
    <td><em>GPIO0_DUALEDGE.config[1]</em></td>
    <td><em>GPIO0_INTPOL.pol[1]</em></td>
    <td><em>GPIO0_INTMODE.gpio_intmode[1]</em></td>
    <td><em>GPIO0_WKEN.en[1]</em></td>
</tr>
<tr>
    <td>…</td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
</tr>
<tr>
    <td>P0.30</td>
    <td><em>GPIO0_INTEN.en[30]</em></td>
    <td><em>GPIO0_INTFL.int[30]</em></td>
    <td><em>GPIO0_DUALEDGE.gpio_dualedge[30]</em></td>
    <td><em>GPIO0_INTPOL.pol[30]</em></td>
    <td><em>GPIO0_INTMODE.gpio_intmode[30]</em></td>
    <td><em>GPIO0_WKEN.en[30]</em></td>
</tr>
<tr>
    <td>P0.31</td>
    <td><em>GPIO0_INTEN.en[31]</em></td>
    <td><em>GPIO0_INTFL.int[31]</em></td>
    <td><em>GPIO0_DUALEDGE.gpio_dualedge[31]</em></td>
    <td><em>GPIO0_INTPOL.pol[31]</em></td>
    <td><em>GPIO0_INTMODE.gpio_intmode[31]</em></td>
    <td><em>GPIO0_WKEN.en[31]</em></td>
</tr>
</tbody>
</table>

*Table 6-8: MAX78000 GPIO0 Pullup/Pulldown/Drive Strength/Voltage Configuration Reference*
<a name="table6-8-max78000-gpio0-pullup-pulldown-drive-strength-voltage-configuration-reference"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th align="center">Device Pin</th>
    <th colspan="3" align="center">Pullup/Pulldown/Strength Select</th>
    <th colspan="2" align="center">Drive Strength</th>
    <th align="center">Voltage</th>
</tr>
</thead>
<tbody>
<tr>
    <td>P0.0</td>
    <td><em>GPIO0_PADCTRL0.config[0]</em></td>
    <td><em>GPIO0_PADCTRL1.config[0]</em></td>
    <td><em>GPIO0_PS.pull_sel[0]</em></td>
    <td><em>GPIO0_DS0.config[0]</em></td>
    <td><em>GPIO0_DS1.config[0]</em></td>
    <td><em>GPIOn_VSSEL.v_sel[0]</em></td>
</tr>
<tr>
    <td>P0.1</td>
    <td><em>GPIO0_PADCTRL0.config[1]</em></td>
    <td><em>GPIO0_PADCTRL1.config[1]</em></td>
    <td><em>GPIO0_PS.pull_sel[1]</em></td>
    <td><em>GPIO0_DS0.config[1]</em></td>
    <td><em>GPIO0_DS1.config[1]</em></td>
    <td><em>GPIOn_VSSEL.v_sel[1]</em></td>
</tr>
<tr>
    <td>…</td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
    <td><em>…</em></td>
</tr>
<tr>
    <td>P0.30</td>
    <td><em>GPIO0_PADCTRL0.config[30]</em></td>
    <td><em>GPIO0_PADCTRL1.config[30]</em></td>
    <td><em>GPIO0_PS.pull_sel[30]</em></td>
    <td><em>GPIO0_DS0.config[30]</em></td>
    <td><em>GPIO0_DS1.config[30]</em></td>
    <td><em>GPIOn_VSSEL.v_sel[30]</em></td>
</tr>
<tr>
    <td>P0.31</td>
    <td><em>GPIO0_PADCTRL0.config[31]</em></td>
    <td><em>GPIO0_PADCTRL1.config[31]</em></td>
    <td><em>GPIO0_PS.pull_sel[31]</em></td>
    <td><em>GPIO0_DS0.config[31]</em></td>
    <td><em>GPIO0_DS1.config[31]</em></td>
    <td><em>GPIOn_VSSEL.v_sel[31]</em></td>
</tr>
</tbody>
</table>


## Usage
### Reset State
During a power-on-reset event, each GPIO is reset to the default input mode with the weak pullup resistor enabled as follows:

1. The GPIO configuration enable bits shown in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a> are set to I/O (transition to AF1) mode.
2. Input mode is enabled ([GPIOn_INTEN](#gpio-port-interrupt-enable-register).*en[pin]* = 1).
3. High impedance mode enabled ([GPIOn_PADCTRL1](#gpio-port-pad-configuration2-register).*config[pin]* = 0, [GPIOn_PADCTRL0](#gpio-port-pad-configuration1-register).*config[pin]* = 0), pullup and pulldown disabled.
4. Output mode disabled ([GPIOn_OUTEN](#gpio-port-output-enable-register).*en[pin]* = 0).
5. Interrupt disabled ([GPIOn_INTEN](#gpio-port-interrupt-enable-register).*en[pin]* = 0).


### Input Mode Configuration
Perform the following steps to configure one or more pins for input mode:

1. Set the GPIO Configuration Enable bits shown in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a> to any one of the I/O mode settings.
2. Configure the electrical characteristics of the pin as desired, as shown in <a href="#table6-3-max78000-input-mode-configuration">Table 6‑3</a>.
3. Enable the input buffer connected to the GPIO pin by setting [GPIOn_INTEN](#gpio-port-interrupt-enable-register).*en[pin]* to 1.
4. Read the input state of the pin using the [GPIOn_IN](#gpio-port-input-register).*level[pin]* field.


### Output Mode Configuration
Perform the following steps to configure a pin for output mode:

1. Set the GPIO Configuration Enable bits shown in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a> to any one of the I/O mode settings.
2. Configure the electrical characteristics of the pin as desired, as shown in <a href="#table6-4-max78000-output-mode-configuration">Table 6‑4</a>.
3. Set the output logic high or logic low using the [GPIOn_OUT](#gpio-port-output-register).*level[pin]* bit.
4. Enable the output buffer for the pin by setting [GPIOn_OUTEN](#gpio-port-output-enable-register).*en[pin]* to 1.


### Alternate Function Configuration
Most GPIO support one or more alternate functions selected with the GPIO configuration enable bits shown in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a>. The bits that select the AF must only be changed while the pin is in one of the I/O modes ([GPIOn_EN0](#gpio-port-configuration-enable-bit0-register) = 1). The specific I/O mode must match the desired AF. For example, if a transition to AF1 is desired, first select the setting corresponding to I/O (transition to AF1). Then enable the desired mode by selecting the AF1 mode.

1. Set the GPIO configuration enable bits shown in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a> to the I/O mode corresponding to the desired new AF setting. For example, select "I/O (transition to AF1)" if switching to AF1. Switching between different I/O mode settings does not affect the state or electrical characteristics of the pin.
2. Configure the electrical characteristics of the pin. See <a href="#table6-3-max78000-input-mode-configuration">Table 6‑3</a> if the assigned alternate function uses the pin as an input. See [Table 6‑4](#output-mode-configuration) if the assigned alternate function uses the pin as an output.
3. Set the GPIO Configuration Enable bits shown in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a> to the desired alternate function.

## Configuring GPIO (External) Interrupts
Each GPIO pin supports external interrupt events when the GPIO is configured for I/O mode and the input mode is enabled. If the GPIO is configured for an alternate peripheral function, the interrupts are peripheral-controlled.

GPIO interrupts can be individually enabled and configured as an edge or level triggered independently on a pin-by-pin basis. The edge trigger can be a rising, falling, or both transitions.

Each GPIO pin has a dedicated status bit in its corresponding [GPIOn_INTFL](#gpio-port-interrupt-status-register) register. A GPIO interrupt occurs when the status bit transitions from 0 to 1 if the corresponding bit is set in the corresponding [GPIOn_INTEN](#gpio-port-interrupt-enable-register) register. Note that the interrupt status bit is always set when the current interrupt configuration event occurs, but an interrupt is only generated if explicitly enabled.

The following procedure details the steps for enabling *ACTIVE* mode interrupt events for a GPIO pin:

1. Disable interrupts by setting the [GPIOn_INTEN](#gpio-port-interrupt-enable-register).*en[pin]* field to 0. Disabling interrupts prevents any new interrupts on the pin from triggering but does not clear previously triggered (pending) interrupts. The application can disable all interrupts for a GPIO port by writing 0 to the [GPIOn_INEN](#gpio-port-input-enable-register) register. To maintain previously enabled interrupts, read the GPIOn_INEN register and save the state before setting the register to 0.
2. Clear pending interrupts by writing 1 to the [GPIOn_INTFL_CLR](#gpio-port-interrupt-clear-register).*clr[pin]* bit.
3. Configure the pin for the desired interrupt event.
4. Set [GPIOn_INTMODE](#gpio-port-interrupt-mode-register).*mode[pin]* to select the desired interrupt.
5. For level triggered interrupts, the interrupt triggers on an input high ([GPIOn_INTPOL](#gpio-port-interrupt-polarity-register).*pol[pin]* = 0) or input low level.
6. For edge triggered interrupts, the interrupt triggers on a transition from low to high([GPIOn_INTPOL](#gpio-port-interrupt-polarity-register).*pol[pin]* = 0) or high to low ([GPIOn_INTPOL](#gpio-port-interrupt-polarity-register).*pol[pin]* = 1).
7. Optionally set [GPIOn_DUALEDGE](#gpio-port-interrupt-dial-edge-mode-register).*de_en[pin]* to 1 to trigger on both the rising and falling edges of the input signal.
   
    a. Set [GPIOn_INTEN](#gpio-port-interrupt-enable-register).*en[pin]* to 1 to enable the interrupt for the pin.


### GPIO Interrupt Handling
Each GPIO port is assigned a dedicated interrupt vector, as shown in <a href="#table6-9-max78000-gpio-port-interrupt-vector-mapping">Table 6‑9</a>.

*Table 6-9: MAX78000 GPIO Port Interrupt Vector Mapping*
<a name="table6-9-max78000-gpio-port-interrupt-vector-mapping"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
<th>GPIO Interrupt Source</th>
<th>GPIO Interrupt Status Register</th>
<th>CM4 Interrupt Vector Number</th>
<th>RV32 Interrupt Vector Number</th>
<th>GPIO Interrupt Vector</th>
</tr>
</thead>
<tbody>
<tr>
    <td>GPIO0[31:0]</td>
    <td><a href="#gpio-port-interrupt-status-register">GPIOn_INTFL</a</td>
    <td>40</td>
    <td>25</td>
    <td>GPIO0_IRQn</td>
</tr>
<tr>
    <td>GPIO1[9:0]</td>
    <td><a href="#gpio-port-interrupt-status-register">GPIOn_INTFL</a</td>
    <td>41</td>
    <td>26</td>
    <td>GPIO1_IRQn</td>
</tr>
<tr>
    <td>GPIO2[7:0]</td>
    <td><a href="#gpio-port-interrupt-status-register">GPIOn_INTFL</a</td>
    <td>42</td>
    <td>27</td>
    <td>GPIO2_IRQn</td>
</tr>
</tbody>
</table>

To handle GPIO interrupts in your interrupt vector handler, complete the following steps:

1. Read the [GPIOn_INTFL](##gpio-port-interrupt-status-register) register to determine the GPIO pin that triggered the interrupt.
2. Complete interrupt tasks associated with the interrupt source pin (application-defined).
3. Clear the interrupt flag in the [GPIOn_INTFL](#gpio-port-interrupt-status-register) register by writing a 1 to the [GPIOn_INTFL_CLR](#gpio-port-interrupt-clear-register) bit position that triggered the interrupt; this also clears and rearms the edge detectors for edge-triggered interrupts.
4. Return from the interrupt vector handler.

### Using GPIO for Wake Up from Low-Power Modes
Low-power modes support an asynchronous wake up from edge-triggered interrupts on the GPIO ports. Level triggered interrupts are not supported for wake up because the system clock must be active to detect
levels.

A single wake-up interrupt vector, GPIOWAKE_IRQn, is assigned for all pins of all GPIO ports. When the GPIO wake-up event occurs, the application software must interrogate each [GPIOn_INTFL](#gpio-port-interrupt-status-register) register to determine which external port pin caused the wake-up event.

*Table 6-10: MAX78000 GPIO Wakeup Interrupt Vector*
<a name="table6-10-max78000-gpio-wakeup-interrupt-vector"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th>GPIO Wake Interrupt Source</th>
    <th>GPIO Wake Interrupt Status Register</th>
    <th>CM4 Interrupt Vector Number</th>
    <th>RV32 Interrupt Vector Number</th>
    <th>GPIO Wake Interrupt Vector</th>
</tr>
</thead>
<tbody>
<tr>
    <td>GPIO0</td>
    <td><a href="#gpio-port-interrupt-status-register">GPIO0_INTFL</a></td>
    <td>70</td>
    <td>6</td>
    <td>GPIOWAKE_IRQn</td>
</tr>
<tr>
    <td>GPIO1</td>
    <td><a href="#gpio-port-interrupt-status-register">GPIO1_INTFL</a></td>
    <td>70</td>
    <td>6</td>
    <td>GPIOWAKE_IRQn</td>
</tr>
<tr>
    <td>GPIO2</td>
    <td><a href="#gpio-port-interrupt-status-register">GPIO2_INTFL</a></td>
    <td>70</td>
    <td>6</td>
    <td>GPIOWAKE_IRQn</td>
</tr>
</tbody>
</table>

To enable a low-power mode to wake up from *SLEEP*, *DEEPSLEEP*, *LPM*, *UPM*, and *BACKUP* using an external GPIO interrupt, complete the following steps:

1. Clear pending interrupt flags by writing a logic 1 to [GPIOn_INTFL_CLR](#gpio-port-interrupt-clear-register).*clr[pin]*.
2. Activate the GPIO wake-up function by writing a logic 1 to [GPIOn_WKEN](#gpio-port-wakeup-enable-register).*we[pin]*.
3. Configure the power manager to use the GPIO as a wake-up source by GCR_PM.gpio_we field to 1.

## Registers

See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. If multiple instances of the peripheral are provided, each instance has its own independent set of the registers shown in [Table 6‑11](#table6-11-gpio-register-summary). Register names for a specific instance are defined by replacing "n" with the instance number. For example, a register PERIPHERALn_CTRL resolves to PERIPHERAL0_CTRL and PERIPHERAL1_CTRL for instances 0 and 1, respectively.

See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific
resets.

*Table 6-11: GPIO Register Summary*
<a name="table6-11-gpio-register-summary"></a>

<table border="1" cellpadding="5" cellspacing="0">
<tr>
    <th>Offset</th>
    <th>Register</th>
    <th>Description</th>
</tr>
</thead>
<tbody>
<tr>
    <td>[0x0000]</td>
    <td><a href="#gpio-port-configuration-enable-bit0-register">GPIOn_EN0</a></td>
    <td>GPIO Port n Configuration Enable Bit 0 Register</td>
</tr>
<tr>
    <td>[0x0004]</td>
    <td><a href="#gpio-port-configuration-enable-atomic-set-bit0-register">GPIOn_EN0_SET</a></td>
    <td>GPIO Port n Configuration Enable Atomic Set Bit 0 Register</td>
</tr>
<tr>
    <td>[0x0008]</td>
    <td><a href="#gpio-port-configuration-enable-atomic-clear-bit0-register">GPIOn_EN0_CLR</a></td>
    <td>GPIO Port n Configuration Enable Atomic Clear Bit 0 Register</td>
</tr>
<tr>
    <td>[0x000C]</td>
    <td><a href="#gpio-port-output-enable-register">GPIOn_OUTEN</a></td>
    <td>GPIO Port n Output Enable Register</td>
</tr>
<tr>
    <td>[0x0010]</td>
    <td><a href="#gpio-port-output-enable-atomic-set-register">GPIOn_OUTEN_SET</a></td>
    <td>GPIO Port n Output Enable Atomic Set Register</td>
</tr>
<tr>
    <td>[0x0014]</td>
    <td><a href="#gpio-port-output-enable-atomic-clear-register">GPIOn_OUTEN_CLR</a></td>
    <td>GPIO Port n Output Enable Atomic Clear Register</td>
</tr>
<tr>
    <td>[0x0018]</td>
    <td><a href="#gpio-port-output-register">GPIOn_OUT</a></td>
    <td>GPIO Port n Output Register</td>
</tr>
<tr>
    <td>[0x001C]</td>
    <td><a href="#gpio-port-output-atomic-set-register">GPIOn_OUT_SET</a></td>
    <td>GPIO Port n Output Atomic Set Register</td>
</tr>
<tr>
    <td>[0x0020]</td>
    <td><a href="#gpio-port-output-atomic-clear-register">GPIOn_OUT_CLR</a></td>
    <td>GPIO Port n Output Atomic Clear Register</td>
</tr>
<tr>
    <td>[0x0024]</td>
    <td><a href="#gpio-port-input-register">GPIOn_IN</a></td>
    <td>GPIO Port n Input Register</td>
</tr>
<tr>
    <td>[0x0028]</td>
    <td><a href="#gpio-port-interrupt-mode-register">GPIOn_INTMODE</a></td>
    <td>GPIO Port n Interrupt Mode Register</td>
</tr>
<tr>
    <td>[0x002C]</td>
    <td><a href="#gpio-port-interrupt-polarity-register">GPIOn_INTPOL</a></td>
    <td>GPIO Port n Interrupt Polarity Register</td>
</tr>
<tr>
    <td>[0x0030]</td>
    <td><a href="#gpio-port-input-enable-register">GPIOn_INEN</a></td>
    <td>GPIO Port n Input Enable Register</td>
</tr>
<tr>
    <td>[0x0034]</td>
    <td><a href="#gpio-port-interrupt-enable-register">GPIOn_INTEN</a></td>
    <td>GPIO Port n Interrupt Enable Register</td>
</tr>
<tr>
    <td>[0x0038]</td>
    <td><a href="#gpio-port-interrupt-enable-atomic-set-register">GPIOn_INTEN_SET</a></td>
    <td>GPIO Port n Interrupt Enable Atomic Set Register</td>
</tr>
<tr>
    <td>[0x003C]</td>
    <td><a href="#gpio-port-interrupt-enable-atomic-clear-register">GPIOn_INTEN_CLR</a></td>
    <td>GPIO Port n Interrupt Enable Atomic Clear Register</td>
</tr>
<tr>
    <td>[0x0040]</td>
    <td><a href="#gpio-port-interrupt-status-register">GPIOn_INTFL</a></td>
    <td>GPIO Port n Interrupt Status Register</td>
</tr>
<tr>
    <td>[0x0048]</td>
    <td><a href="#gpio-port-interrupt-clear-register">GPIOn_INTFL_CLR</a></td>
    <td>GPIO Port n Interrupt Clear Register</td>
</tr>
<tr>
    <td>[0x004C]</td>
    <td><a href="#gpio-port-wakeup-enable-register">GPIOn_WKEN</a></td>
    <td>GPIO Port n Wakeup Enable Register</td>
</tr>
<tr>
    <td>[0x0050]</td>
    <td><a href="#gpio-port-wakeup-enable-atomic-set-register">GPIOn_WKEN_SET</a></td>
    <td>GPIO Port n Wakeup Enable Atomic Set Register</td>
</tr>
<tr>
    <td>[0x0054]</td>
    <td><a href="#gpio-port-wakeup-enable-atomic-clear-register">GPIOn_WKEN_CLR</a></td>
    <td>GPIO Port n Wakeup Enable Atomic Clear Register</td>
</tr>
<tr>
    <td>[0x005C]</td>
    <td><a href="#gpio-port-interrupt-dial-edge-mode-register">GPIOn_DUALEDGE</a></td>
    <td>GPIO Port n Interrupt Dual Edge Mode Register</td>
</tr>
<tr>
    <td>[0x0060]</td>
    <td><a href="#gpio-port-pad-configuration1-register">GPIOn_PADCTRL0</a></td>
    <td>GPIO Port n Pad Configuration 1 Register</td>
</tr>
<tr>
    <td>[0x0064]</td>
    <td><a href="#gpio-port-pad-configuration2-register">GPIOn_PADCTRL1</a></td>
    <td>GPIO Port n Pad Configuration 2 Register</td>
</tr>
<tr>
    <td>[0x0068]</td>
    <td><a href="#gpio-port-configuration-enable-bit1-register">GPIOn_EN1</a></td>
    <td>GPIO Port n Configuration Enable Bit 1 Register</td>
</tr>
<tr>
    <td>[0x006C]</td>
    <td><a href="#gpio-port-configuration-enable-atomic-set-bit1-register">GPIOn_EN1_SET</a></td>
    <td>GPIO Port n Configuration Enable Atomic Set Bit 1 Register</td>
</tr>
<tr>
    <td>[0x0070]</td>
    <td><a href="#gpio-port-configuration-enable-atomic-clar-bit1-register">GPIOn_EN1_CLR</a></td>
    <td>GPIO Port n Configuration Enable Atomic Clear Bit 1 Register</td>
</tr>
<tr>
    <td>[0x0074]</td>
    <td><a href="#gpio-port-configuration-enable-bit2-register">GPIOn_EN2</a></td>
    <td>GPIO Port n Configuration Enable Bit 2 Register</td>
</tr>
<tr>
    <td>[0x0078]</td>
    <td><a href="#gpio-port-configuration-enable-atomic-set-bit2-register">GPIOn_EN2_SET</a></td>
    <td>GPIO Port n Configuration Enable Atomic Set Bit 2 Register</td>
</tr>
<tr>
    <td>[0x007C]</td>
    <td><a href="#gpio-port-configuration-enable-atomic-clear-bit2-register">GPIOn_EN2_CLR</a></td>
    <td>GPIO Port n Configuration Enable Atomic Clear Bit 2 Register</td>
</tr>
<tr>
    <td>[0x00A8]</td>
    <td><a href="#gpio-port-hysteresis-enable-register">GPIOn_HYSEN</a></td>
    <td>GPIO Port n Hysteresis Enable Register</td>
</tr>
<tr>
    <td>[0x00AC]</td>
    <td><a href="#gpio-port-slew-rate-select-register">GPIOn_SRSEL</a></td>
    <td>GPIO Port n Slew Rate Select Register</td>
</tr>
<tr>
    <td>[0x00B0]</td>
    <td><a href="#gpio-port-output-drive-strength-bit0-register">GPIOn_DS0</a></td>
    <td>GPIO Port n Output Drive Strength Bit 0 Register</td>
</tr>
<tr>
    <td>[0x00B4]</td>
    <td><a href="#gpio-port-output-drive-strength-bit1-register">GPIOn_DS11</a></td>
    <td>GPIO Port n Output Drive Strength Bit 1 Register</td>
</tr>
<tr>
    <td>[0x00B8]</td>
    <td><a href="#gpio-port-pulldown-pullup-strength-select-register">GPIOn_PS</a></td>
    <td>GPIO Port n Pulldown/Pullup Strength Select Register</td>
</tr>
<tr>
    <td>[0x00C0]</td>
    <td><a href="#gpio-port-voltage-select-register">GPIOn_VSSEL</a></td>
    <td>GPIO Port n Voltage Select Register</td>
</tr>
</tbody>
</table>

### Register Details

*Table 6-12: GPIO Port n Configuration Enable Bit 0 Register*
<a name="gpio-port-configuration-enable-bit0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">GPIO Port n Configuration Enable Bit 0</td>
       <td colspan="1">GPIOn_EN0</td>
       <td>[0x0000]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
    <tr>
        <td>31:0</td>
        <td>config</td>
        <td>R/W</td>
        <td>1</td>
        <td><strong>GPIO Configuration Enable Bit 0</strong><br>
        In conjunction with the bits in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a>, this field configures the corresponding device pin for digital I/O or an alternate function mode. This field can be modified directly by writing to this register or indirectly through <a href="#gpio-port-configuration-enable-atomic-set-bit0-register">GPIOn_EN0_SET</a> or <a href="#gpio-port-configuration-enable-atomic-clear-bit0-register">GPIOn_EN0_CLR</a>. 
        <p><a href="#table6-5-max78000-gpio0-alternate-function-configuration-reference">Table 6‑5</a> depicts a detailed example of how each of these bits applies to each of the GPIO device pins</p>
        <p><em>Note: Some GPIO are not implemented in all devices. Bits associated with unimplemented GPIO should not be changed from their default value.</em></p>
        <p><em>Note: This register setting does not affect the input and interrupt functionality of the associated pin.</em></p>
        </td>
    </tr>
</tbody>
</table>

*Table 6-13: GPIO Port n Configuration Enable Atomic Set Bit 0 Register*
<a name="gpio-port-configuration-enable-atomic-set-bit0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">GPIO Port n Configuration Enable Atomic Set Bit 0 Register</td>
       <td colspan="1">GPIOn_EN0_SET</td>
       <td>[0x0004]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
    <tr>
        <td>31:0</td>
        <td>set</td>
        <td>R/W1</td>
        <td>0</td>
            <td><strong>GPIO Configuration Enable Atomic Set Bit 0</strong><br> 
            Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-configuration-enable-bit0-register">GPIOn_EN0</a> register.<br>
            <div style="margin-left: 20px">
            0: No effect.<br>
            1: Corresponding bits in <a href="#gpio-port-configuration-enable-bit0-register">GPIOn_EN0</a> register set to 1.
            </div>
        </td>
</tr>
</table>

*Table 6-14: GPIO Port n Configuration Enable Atomic Clear Bit 0 Register*
<a name="gpio-port-configuration-enable-atomic-clear-bit0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">GPIO Port n Configuration Enable Atomic Clear Bit 0</td>
       <td colspan="1">GPIOn_EN0_CLR</td>
       <td>[0x0008]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>R/W1</td>
        <td>0</td>
            <td><strong>GPIO Configuration Enable Atomic Clear Bit 0</strong><br> 
            Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-configuration-enable-bit0-register">GPIOn_EN0</a> register.<br>
            <div style="margin-left: 20px">
            0: No effect.<br>
            1: Corresponding bits in <a href="#gpio-port-configuration-enable-bit0-register">GPIOn_EN0</a> register cleared to 0.
            </div>
        </td>
</tr>
</table>

*Table 6-15: GPIO Port n Output Enable Register*
<a name="gpio-port-output-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">GPIO Port n Output Enable</td>
       <td colspan="1">GPIOn_OUTEN</td>
       <td>[0x000C]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
    <tr>
        <td>31:0</td>
        <td>en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Output Enable</strong><br>
        Set bit to 1 to enable the output driver for the corresponding GPIO pin. A bit can be enabled directly by writing to this register or indirectly through <a href="#gpio-port-output-enable-atomic-set-register">GPIOn_OUTEN_SET</a> or <a href="#gpio-port-output-enable-atomic-clear-register">GPIOn_OUTEN_CLR</a>.<br>
        <div style="margin-left: 20px">
        0: Pin is set to input mode; output driver disabled.<br>
        1: Pin is set to output mode.
        </div>
        </td>
</tr>
</table>

*Table 6-16: GPIO Port n Output Enable Atomic Set Register*
<a name="gpio-port-output-enable-atomic-set-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">GPIO Port n Output Enable Atomic Set</td>
       <td colspan="1">GPIOn_OUTEN_SET</td>
       <td>[0x0010]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
    <tr>
        <td>31:0</td>
        <td>set</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Output Enable Atomic Set</strong><br>
        Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-output-enable-register">GPIOn_OUTEN</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-output-enable-register">GPIOn_OUTEN</a> set to 1.</p>
        </div>
        </td>
    </tr>
</table>

*Table 6-17: GPIO Port n Output Enable Atomic Clear Register*
<a name="gpio-port-output-enable-atomic-clear-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">GPIO Port n Output Enable Atomic Clear</td>
       <td colspan="1">GPIOn_OUTEN_CLR</td>
       <td>[0x0014]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Output Enable Atomic Clear</strong><br> Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-output-enable-register">GPIOn_OUTEN</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-output-enable-register">GPIOn_OUTEN</a> cleared to 0.</p></div>
        </td>
    </tr>
</table>

*Table 6-18: GPIO Port n Output Register*
<a name="gpio-port-output-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Output</td>
       <td colspan="1">GPIOn_OUT</td>
       <td>[0x0018]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>level</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Output</strong><br>Set the corresponding output pin high or low.
        <div style="margin-left: 20px">
        0: Drive the corresponding output pin low (logic 0).<br>
        1: Drive the corresponding output pin high (logic 1).
        </div>
        </td>
    </tr>
</table>

*Table 6-19: GPIO Port n Output Atomic Set Register*
<a name="gpio-port-output-atomic-set-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Output Atomic Set</td>
       <td colspan="1">GPIOn_OUT_SET</td>
       <td>[0x001C]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>set</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Output Atomic Set</strong><br>Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-output-register">GPIOn_OUT</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-output-enable-register">GPIOn_OUTEN</a> set to 1.
        </div>
        </td>
    </tr>
</table>

*Table 6-20: GPIO Port n Output Atomic Clear Register*
<a name="gpio-port-output-atomic-clear-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Output Atomic Clear</td>
       <td colspan="1">GPIOn_OUT_CLR</td>
       <td>[0x0020]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>WO</td>
        <td>0</td>
        <td><strong>GPIO Output Atomic Clear</strong><br>
        Writing 1 to one or more bits clears the corresponding bits in the <a href="#gpio-port-output-register">GPIOn_OUT</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-output-enable-register">GPIOn_OUTEN</a> cleared to 0.
        </div>
        </td>
    </tr>
</table>

*Table 6-21: GPIO Port n Input Register*
<a name="gpio-port-input-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Input</td>
       <td colspan="1">GPIOn_IN</td>
       <td>[0x0024]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>level</td>
        <td>RO</td>
        <td>-</td>
        <td><strong>GPIO Input</strong><br> Returns the state of the input pin only if the corresponding bit in the <a href="#gpio-port-input-enable-register">GPIOn_INEN</a> register is set. The state is not affected by the pin's configuration as an output or alternate function.<br>
        <div style="margin-left: 20px">
        0: Input pin low<br>
        1: Input pin high.</div>
        </td>
    </tr>
</table>

*Table 6-22: GPIO Port n Interrupt Mode Register*
<a name="gpio-port-interrupt-mode-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Interrupt Mode</td>
       <td colspan="1">GPIOn_INTMODE</td>
       <td>[0x0028]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Mode</strong><br>
        Selects interrupt mode for the corresponding GPIO pin.<br>
        <div style="margin-left: 20px">
        0: Level triggered interrupt.<br>
        1: Edge triggered interrupt.
        </div>
        <p><em>Note: This bit has no effect unless the corresponding bit in the
        <a href="#gpio-port-interrupt-enable-register">GPIOn_INTEN</a> register is set.</em></p>
        </td>
    </tr>
</table>

*Table 6-23: GPIO Port n Interrupt Polarity Register*
<a name="gpio-port-interrupt-polarity-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Interrupt Polarity</td>
       <td colspan="1">GPIOn_INTPOL</td>
       <td>[0x002C]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>pol</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Polarity</strong><br>
        Interrupt polarity selection bit for the corresponding GPIO pin.<br>
        Level triggered mode (<a href="#gpio-port-interrupt-mode-register">GPIOn_INTMODE</a>.<em>mode[pin]</em> = 0):<br>
            <div style="margin-left: 20px">
            0: Input low (logic 0) triggers interrupt.<br>
            1: Input high (logic 1) triggers interrupt.
            </div> <br>
        Edge triggered mode
        (<a href="#gpio-port-interrupt-mode-register">GPIOn_INTMODE</a>.<em>mode[pin]</em> = 1):
        <div style="margin-left: 20px">
        0: Falling edge triggers interrupt<br>
        1: Rising edge triggers interrupt.
        </div>
        <p><em>Note: This bit has no effect unless the corresponding bit in the
        <a href="#gpio-port-interrupt-enable-register">GPIOn_INTEN</a> register is set.</em></p></td>
    </tr>
</table>

*Table 6-24: GPIO Port n Input Enable Register*
<a name="gpio-port-input-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Input Enable</td>
       <td colspan="1">GPIOn_INEN</td>
       <td>[0x0030]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>en</td>
        <td>R/W</td>
        <td>1</td>
        <td><strong>GPIO Input Enable</strong><br> This field connects the corresponding input pad to the specified input pin for reading the pin state using the <a href="#gpio-port-input-register">GPIOn_IN</a> register.<br>
            <div style="margin-left: 20px">
            0: Input not connected.<br>
            1: Input pin connected to the pad for reading through <a href="#gpio-port-input-register">GPIOn_IN</a> register.
            </div>
        </td>
    </tr>
</table>

*Table 6-25: GPIO Port n Interrupt Enable Register*
<a name="gpio-port-interrupt-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Interrupt Enable</td>
       <td colspan="1">GPIOn_INTEN</td>
       <td>[0x0034]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Enable</strong><br>
        Enable or disable the interrupt for the corresponding GPIO pin.<br>
        <div style="margin-left: 20px">
        0: Disabled.<br>
        1: Enabled.
        </div>
        <p><em>Note: Disabling a GPIO interrupt does not clear pending interrupts
        for the associated pin. Use the <a href="#gpio-port-interrupt-clear-register">GPIOn_INTFL_CLR</a> register to clear
        pending interrupts.</em></p>
        </td>
    </tr>
</table>

*Table 6-26: GPIO Port n Interrupt Enable Atomic Set Register*
<a name="gpio-port-interrupt-enable-atomic-set-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port Interrupt Enable Atomic Set</td>
       <td colspan="1">GPIOn_INTEN_SET</td>
       <td>[0x0038]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>set</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Enable Atomic Set</strong><br>
        Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-interrupt-enable-register">GPIOn_INTEN</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-interrupt-enable-register">GPIOn_INTEN</a> register set to 1.
        </div>
        </td>
    </tr>
</tbody>
</table>

*Table 6-27: GPIO Port n Interrupt Enable Atomic Clear Register*
<a name="gpio-port-interrupt-enable-atomic-clear-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port Interrupt Enable Atomic Clear</td>
       <td colspan="1">GPIOn_INTEN_CLR</td>
       <td>[0x003C]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Enable Atomic Clear</strong><br>
        Writing 1 to one or more bits clears the corresponding bits in the <a href="#gpio-port-interrupt-enable-register">GPIOn_INTEN</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-interrupt-enable-register">GPIOn_INTEN</a> register cleared to 0.
        </div>
        </td>
</tr>
</table>

*Table 6-28: GPIO Port n Interrupt Status Register*
<a name="gpio-port-interrupt-status-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port Interrupt Status</td>
       <td colspan="1">GPIOn_INTFL</td>
       <td>[0x0040]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>if</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Status</strong><br>
        An interrupt is pending for the associated GPIO pin when this bit reads 1.<br>
        <div style="margin-left: 20px">
        0: No GPIO interrupt is pending for the associated GPIO pin.<br>
        1: A GPIO interrupt is pending for the associated GPIO pin.
        </div>
        <p><em>Note: Write a 1 to the corresponding bit in the <a href="#gpio-port-interrupt-clear-register">GPIOn_INTFL_CLR</a>
        register to clear the interrupt pending status flag.</em></p>
        </td>
    </tr>
</table>

*Table 6-29: GPIO Port n Interrupt Clear Register*
<a name="gpio-port-interrupt-clear-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port Interrupt Clear</td>
       <td colspan="1">GPIOn_INTFL_CLR</td>
       <td>[0x0048]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Clear</strong><br>
        Write 1 to clear the associated interrupt status (<a href="#gpio-port-interrupt-status-register">GPIOn_INTFL</a>).<br>
        <div style="margin-left: 20px">
        0: No effect on the associated <a href="#gpio-port-interrupt-status-register">GPIOn_INTFL</a> flag.<br>
        1: Clear the associated interrupt pending flag in the <a href="#gpio-port-interrupt-status-register">GPIOn_INTFL</a> register.
        </div>
        </td>
</tr>
</table>

*Table 6-30: GPIO Port n Wakeup Enable Register*
<a name="gpio-port-wakeup-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Wakeup Enable</td>
       <td colspan="1">GPIOn_WKEN</td>
       <td>[0x004C]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>we</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Wakeup Enable</strong><br>
        Enable the I/O as a wake-up source from <em>SLEEP</em>, <em>DEEPSLEEP</em>, and <em>BACKUP</em>.<br>
        <div style="margin-left: 20px">
        0: The GPIO pin is not enabled as a wake-up source from low-power modes.<br>
        1: The GPIO pin is enabled as a wake-up source from low-power modes.
        </div>
        </td>
</tr>
</table>

*Table 6-31: GPIO Port n Wakeup Enable Atomic Set Register*
<a name="gpio-port-wakeup-enable-atomic-set-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port Wakeup Enable Atomic Set</td>
       <td colspan="1">GPIOn_WKEN_SET</td>
       <td>[0x0050]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>set</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Wakeup Enable Atomic Set</strong><br>
        Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-wakeup-enable-register">GPIOn_WKEN</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-wakeup-enable-register">GPIOn_WKEN</a> register set to 1.
        </div>
        </td>
</tr>
</table>

*Table 6-32: GPIO Port n Wakeup Enable Atomic Clear Register*
<a name="gpio-port-wakeup-enable-atomic-clear-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port Wakeup Enable Atomic Clear</td>
       <td colspan="1">GPIOn_WKEN_CLR</td>
       <td>[0x0054]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Wakeup Enable Atomic Clear</strong><br>
        Writing 1 to one or more bits clears the corresponding bits in the <a href="#gpio-port-wakeup-enable-register">GPIOn_WKEN</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-wakeup-enable-register">GPIOn_WKEN</a> register cleared to 0.
        </td>
    </tr>
</table>

*Table 6-33: GPIO Port n Interrupt Dual Edge Mode Register*
<a name="gpio-port-interrupt-dial-edge-mode-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
       <td colspan="3">GPIO Port n Interrupt Dual Edge Mode</td>
       <td colspan="1">GPIOn_DUALEDGE</td>
       <td>[0x005C]</td>
    </tr>
    <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>de_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Interrupt Dual-Edge Mode Select</strong><br>
        Setting this bit triggers interrupts on both the rising and falling edges of the corresponding GPIO if the associated <a href="#gpio-port-interrupt-mode-register">GPIOn_INTMODE</a> bit is set to edge-triggered. The associated polarity (<a href="#gpio-port-interrupt-polarity-register">GPIOn_INTPOL</a>) setting has no effect when this bit is set.<br>
        <div style="margin-left: 20px">
        0: Disable<br>
        1: Enable
        </div>
        </td>
    </tr>
</table>


*Table 6-34: GPIO Port n Pad Configuration 1 Register*
<a name="gpio-port-pad-configuration1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Pad Configuration 1</td>
        <td colspan="1">GPIOn_PADCTRL0</td>
        <td>[0x0060]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>config</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Pad Configuration 1</strong><br>
        Input mode configuration for the associated GPIO pin. Input mode selection and the choice of a weak or strong pullup, or weak or strong pulldown resistor, are described in <a href="#table6-3-max78000-input-mode-configuration">Table 6‑3</a>.
        </td>
    </tr>
</table>

*Table 6-35: GPIO Port n Pad Configuration 2 Register*
<a name="gpio-port-pad-configuration2-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Pad Configuration 2</td>
        <td colspan="1">GPIOn_PADCTRL1</td>
        <td>[0x0064]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>config</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Pad Configuration 2</strong><br>
        Input mode configuration for the associated GPIO pin. Input mode selection and the choice of a weak or strong pullup, or weak or strong pulldown resistor, are described in <a href="#table6-3-max78000-input-mode-configuration">Table 6‑3</a>.
        </td>
    </tr>
</table>

*Table 6-36: GPIO Port n Configuration Enable Bit 1 Register*
<a name="gpio-port-configuration-enable-bit1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Configuration Enable Bit 1</td>
        <td>GPIOn_EN1</td>
        <td>[0x0068]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>config</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Configuration Enable Bit 1</strong><br>
        In conjunction with the bits in Table 6-2, this field configures the corresponding device pin for digital I/O or an alternate function mode. This field can be modified directly by writing to this register or indirectly through the <a href="#gpio-port-configuration-enable-atomic-set-bit1-register">GPIOn_EN1_SET</a> or <a href="#gpio-port-configuration-enable-atomic-clar-bit1-register">GPIOn_EN1_CLR</a> registers.
        <p><a href="#table6-5-max78000-gpio0-alternate-function-configuration-reference">Table 6‑5</a> depicts a detailed example of how each of these bits applies to each of the GPIO device pins</p>
        <p><em>Note: Some GPIO are not implemented in all devices. The bits associated with unimplemented GPIO should not be changed from their default value.</em></p>
        <p><em>Note: This register setting does not affect input and interrupt functionality of the associated pin.</em></p>
        </td>
    </tr>
</table>

*Table 6-37: GPIO Port n Configuration Enable Atomic Set Bit 1 Register*
<a name="gpio-port-configuration-enable-atomic-set-bit1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Configuration Enable Atomic Set Bit 1</td>
        <td>GPIOn_EN1_SET</td>
        <td>[0x006C]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>set</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Configuration Enable Atomic Set Bit 1</strong><br>
        Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-configuration-enable-bit1-register">GPIOn_EN1</a> register.<br>
        <div style="margin-left: 20px">
        0: No effect.<br>
        1: Corresponding bits in <a href="#gpio-port-configuration-enable-bit1-register">GPIOn_EN1</a> register set to 1.
        </div>
        </td>
    </tr>
</table>

*Table 6-38: GPIO Port n Configuration Enable Atomic Clear Bit 1 Register*
<a name="gpio-port-n-configuration-enable-atomic-clear-bit-1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Configuration Enable Atomic Clear Bit 1</td>
        <td>GPIOn_EN1_CLR</td>
        <td>[0x0070]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Configuration Enable Atomic Clear Bit 1</strong><br>
        Writing 1 to one or more bits clears the corresponding bits in the <a href="#gpio-port-configuration-enable-bit1-register">GPIOn_EN1</a> register.<br>
        <div style="margin-left: 20px;">
            0: No effect.<br>
            1: Corresponding bits in <a href="#gpio-port-configuration-enable-bit1-register">GPIOn_EN1</a> register cleared to 0.
        </div>
        </td>
    </tr>
</table>

*Table 6-39: GPIO Port n Configuration Enable Bit 2 Register*
<a name="gpio-port-configuration-enable-bit2-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Configuration Enable Bit 2</td>
        <td>GPIOn_EN2</td>
        <td>[0x0074]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>config</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Configuration Enable Bit 2</strong><br>
        In conjunction with the bits in <a href="#table6-2-max78000-gpio-pin-function-configuration">Table 6‑2</a>, this field configures the corresponding device pin for digital I/O or an alternate function mode. This field can be modified directly by writing to this register or indirectly through <a href="#gpio-port-configuration-enable-atomic-set-bit2-register">GPIOn_EN2_SET</a> or <a href="#gpio-port-configuration-enable-atomic-clear-bit2-register">GPIOn_EN2_CLR</a>.
        <p><a href="#table6-5-max78000-gpio0-alternate-function-configuration-reference">Table 6‑5</a> depicts a detailed example of how each of these bits applies to each of the GPIO device pins.</p>
        <p><em>Note: Some GPIO are not implemented in all devices. The bits associated with unimplemented GPIO should not be changed from their default value.</p>
        <em>Note:This register setting does not affect input and interrupt functionality of the associated pin.</em>
        </td>
    </tr>
</table>

*Table 6-40: GPIO Port n Configuration Enable Atomic Set Bit 2 Register*
<a name="gpio-port-configuration-enable-atomic-set-bit2-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Configuration Enable Atomic Set Bit 2</td>
        <td>GPIOn_EN2_SET</td>
        <td>[0x0078]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>set</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Alternate Function Select Atomic Set Bit 2</strong><br>
        Writing 1 to one or more bits sets the corresponding bits in the <a href="#gpio-port-configuration-enable-bit2-register">GPIOn_EN2</a> register.<br>
        <div style="margin-left: 20px;">
            0: No effect.<br>
            1: Corresponding bits in <a href="#gpio-port-configuration-enable-bit2-register">GPIOn_EN2</a> register set to 1.
        </div>
        </td>
    </tr>
</table>

*Table 6-41: GPIO Port n Configuration Enable Atomic Clear Bit 2 Register*
<a name="gpio-port-configuration-enable-atomic-clear-bit2-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Configuration Enable Atomic Clear Bit 2</td>
        <td>GPIOn_EN2_CLR</td>
        <td>[0x007C]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>clr</td>
        <td>R/W1</td>
        <td>0</td>
        <td><strong>GPIO Alternate Function Select Atomic Clear Bit 2</strong><br>
        Writing 1 to one or more bits clears the corresponding bits in the <a href="#gpio-port-configuration-enable-bit2-register">GPIOn_EN2</a> register.<br>
        <div style="margin-left: 20px;">
            0: No effect.<br>
            1: Corresponding bits in <a href="#gpio-port-configuration-enable-bit2-register">GPIOn_EN2</a> register cleared to 0.
        </div>
        </td>
    </tr>
</table>

*Table 6-42: GPIO Port n Hysteresis Enable Register*
<a name="gpio-port-hysteresis-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Hysteresis Enable</td>
        <td>GPIOn_HYSEN</td>
        <td>[0x00A8]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>en</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
</table>

*Table 6-43: GPIO Port n Slew Rate Select Register*
<a name="gpio-port-slew-rate-select-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Slew Rate Select Register</td>
        <td>GPIOn_SRSEL</td>
        <td>[0x00AC]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>sr_sel</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
</table>

*Table 6-44: GPIO Port n Output Drive Strength Bit 0 Register*
<a name="gpio-port-output-drive-strength-bit0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Output Drive Strength Bit 0</td>
        <td>GPIOn_DS0</td>
        <td>[0x00B0]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>config</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Output Drive Strength Selection 0</strong><br>
        See <a href="#table6-4-max78000-output-mode-configuration">Table 6-4</a> for details on setting the GPIO output drive strength and other electrical characteristics.
        </td>
    </tr>
</table>

*Table 6-45: GPIO Port n Output Drive Strength Bit 1 Register*
<a name="gpio-port-output-drive-strength-bit1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Output Drive Strength Bit 1</td>
        <td>GPIOn_DS11</td>
        <td>[0x00B4]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>config</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>GPIO Output Drive Strength Selection 1</strong><br>
        See <a href="#table6-4-max78000-output-mode-configuration">Table 6-4</a> for details on setting the GPIO output drive strength and other electrical characteristics.
        </td>
    </tr>
</table>

*Table 6-46: GPIO Port n Pulldown/Pullup Strength Select Register*
<a name="gpio-port-pulldown-pullup-strength-select-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Pulldown/Pullup Strength Select Register</td>
        <td>GPIOn_PS</td>
        <td>[0x00B8]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>pull_sel</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>GPIO Pulldown/Pullup Strength Select</strong><br>
            This field selects the strength of the pullup or pulldown resistor for a pin configured for input mode.<br>
            <div style="margin-left: 20px;">
                0: Weak pulldown/pullup resistor for input pin.<br>
                1: Strong pulldown/pullup resistor for input pin.
            </div>
            <p><em>Note: Refer to the data sheet for specific electrical characteristics of the pulldown/pullup resistances.</em></p>
        </td>
    </tr>
</table>

*Table 6-47: GPIO Port n Voltage Select Register*
<a name="gpio-port-voltage-select-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">GPIO Port n Voltage Select</td>
        <td>GPIOn_VSSEL</td>
        <td>[0x00C0]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>v_sel</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>GPIO Supply Voltage Select</strong><br>
            This field selects the voltage rail used for the GPIO pin.<br>
            <div style="margin-left: 20px;">
                0: V<sub>DDIO</sub><br>
                1: V<sub>DDIOH</sub>
            </div>
        </td>
    </tr>
</table>