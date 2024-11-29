The ADC is a 10-bit sigma-delta ADC with a single-ended input multiplexer and an integrated reference generator. The multiplexer selects an input channel from either the 8 external analog input signals or the internal power supply inputs. The external analog input signals are defined as alternate functions on GPIO, as shown in [Table 11-1](#table11-1-max78000-adc-input-pins-for-81-ctbga-package).

The 10 - bit ADC conversions are stored as a 16-bit value selectable as MSB or LSB aligned. The 8 external analog inputs can be configured by software as 4 two-input comparators with interrupt capabilities. Comparator 0, CMP0, is configurable to wake the device from SLEEP, LPM, UPM, STANDBY, and BACKUP. The remaining three comparators, CMP1, CMP2, and CMP3, are configurable as wake-up sources from SLEEP, LPM, and UPM.

## Features

- Maximum 8MHz ADC clock rate
- Two reference source options
    - An internal 1.22V bandgap
    - V<sub>DDA</sub>/2 supply
- 8 external analog inputs configurable as 4 two-input comparators
- 8 internal power supply monitor inputs
- Fixed 10-bit word conversion time of 1024 ADC clock cycles
- Programmable out-of-range (limit) detection
- Interrupt generation for limit detection, conversion start, conversion complete, and internal reference powered on
- Serial ADC data measurements
- ADC conversion 10-bit output either MSB or LSB aligned

## Instances
*Table 11-1: MAX78000 ADC Input Pins for the 81-CTBGA Package*
<a name="table11-1-max78000-adc-input-pins-for-81-ctbga-package"></a>

<table border="1" cellpadding="5" cellspacing="0">
<tr>
    <th>Function</th>
    <th>81 CTBGA Pin</th>
    <th>81 CTBGA Alternate Function</th>
</tr>
<tbody>
<tr>
    <td>AIN0 / AIN0N</td>
    <td>P2.0</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN1 / AIN0P</td>
    <td>P2.1</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN2 / AIN1N</td>
    <td>P2.2</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN3 / AIN1P</td>
    <td>P2.3</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN4 / AIN2N</td>
    <td>P2.4</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN5 / AIN2P</td>
    <td>P2.5</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN6 / AIN3N</td>
    <td>P2.6</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN7 / AIN3P</td>
    <td>P2.7</td>
    <td>AF1</td>
</tr>
</table>

## Architecture
The ADC is a first-order sigma-delta converter with 10-bit output. The ADC operates at a maximum frequency of 8MHz with a fixed-sample rate as shown in [Equation 11-1](#equation11-1-adc-10-bit-word-sample-rate). Details of selecting the ADC clock frequency, fadcclk, are covered in the Clock Configuration section.

*Equation 11-1: ADC 10-bit Word Sample Rate*
<a name="equation11-1-adc-10-bit-word-sample-rate"></a>

$$ 
t_{\text{adc_sample}} = 1024 \times \left( \frac{1}{f_{\text{adcclk}}} \right)
$$

The ADC offset is factory trimmed and automatically loaded into the ADC controller during system power-up.

The ADC uses a switched capacitor network to perform the conversion; this results in dynamic switching current and requires settling time for the external analog input signals (AIN0 – AIN7). This dynamic switching current sets the upper limit of the source impedance of the external analog input signals to approximately 10kΩ.

The ADC supports a gain of 2 × to provide additional conversion resolution if the input signals are less than half the reference voltage.

*Figure 11-1: Analog to Digital Converter Block Diagram*
<a name="figure11-1-analog-to-digital-converter-block-diagram"></a>

![Figure 11-1](assets/images/figure11-1.svg)

## Clock Configuration
The ADC clock, adcclk, is controlled by the GCR_PCLKDIV.adcfrq register field. Configure this field to achieve the target ADC sample frequency. The maximum clock frequency supported by the ADC is 8MHz. The divisor selection, GCR_PCLKDIV.adcfrq, for the ADC depends on the peripheral clock. [Equation 11-2](#equation11-2-adc-clock-frequency) shows the calculation for the ADC clock.

*Equation 11-2: ADC Clock Frequency*
<a name="equation11-2-adc-clock-frequency"></a>

$$
f_{\text{adcclk}} = \frac{f_{\text{PCLK}}}{\text{GCR_PCKDIV.adcfrq}}
$$

The GCR_PCLKDIV.adcfrq field setting must result in a value for $f_{\text{adcclk}} \le 8\,\text{MHz}$
as shown in [Table 11-2](#table11-2-max78000-adc-clock-frequency) with IPO set as the system clock.   

*Table 11-2: MAX78000 ADC Clock Frequency and ADC Conversion Time with the System Clock set to the IPO*
<a name="table11-2-max78000-adc-clock-frequency"></a>

<table border="1" cellpadding="5" cellspacing="0">
<tr>
<td>GCR_PCLKDIV.adcfrq</td>
<td>ADC Clock Frequency (Hz)<p>
<span class="math display"><strong>f</strong><sub><strong>adcclk</strong></sub></span></td>
<td>10-Bit Word Conversion Time (𝜇s)<br />
<span class="math display"><strong>t</strong><sub><strong>adc_sample</strong></sub></span></td>
</tr>
<tr>
    <td>0 – 7</td>
    <td>Invalid</td>
    <td>Invalid</td>
</tr>
<tr>
    <td>8</td>
    <td>6,250,000</td>
    <td>164</td>
</tr>
<tr>
    <td>9</td>
    <td>5,555,555</td>
    <td>184</td>
</tr>
<tr>
    <td>10</td>
    <td>5,000,000</td>
    <td>205</td>
</tr>
<tr>
    <td>11</td>
    <td>4,545,454</td>
    <td>225</td>
</tr>
<tr>
    <td>12</td>
    <td>4,166,666</td>
    <td>246</td>
</tr>
<tr>
    <td>13</td>
    <td>3,846,153</td>
    <td>266</td>
</tr>
<tr>
    <td>14</td>
    <td>3,571,428</td>
    <td>287</td>
</tr>
<tr>
    <td>15</td>
    <td>3,333,333</td>
    <td>307</td>
</tr>
</table>

## Power-Up Sequence
Complete the following steps to configure the ADC:

1. Disable the ADC clock by setting the ADC_CTRL.clk_en field to 0.
2. Set the ADC clock (f<sub>adcclk</sub>) using the GCR_PCLKDIV.adcfrq field. See Clock Configuration for details.
3. Enable the ADC clock by setting the ADC_CTRL.clk_en field to 1
4. Clear the ADC reference ready interrupt flag by writing a 1 to ADC_INTR.ref_ready_if.
5. Optionally enable the ADC reference ready interrupt by setting the ADC_INTR.ref_ready_ie field to 1 and enable the ADC interrupt handler (ADC_IRQn).
6. Select one of the following ADC reference sources:

    a. Internal 1.22V bandgap reference (ADC_CTRL.ref_sel = 0).

    b. V<sub>DDA</sub>/2 reference (ADC_CTRL.ref_sel = 1).

7. Complete the following steps to enable power to the ADC and optionally the internal ADC reference:

    a. Set ADC_CTRL.pwr to 1 to turn on the ADC.

    b. Set ADC_CTRL.refbuf_pwr to 1 to turn on the internal reference buffer If using the internal reference.

    c. Wait until hardware sets the ADC_INTR.ref_ready_if field to 1, indicating the internal reference is fully powered on and ready.

    d. Clear the ADC reference ready interrupt flag by writing 1 to ADC_INTR.ref_ready_if.

    e. Optionally disable the ADC reference ready interrupt by clearing the ADC_INTR.ref_ready_ie field to 0.

## Conversion
After the power-up sequence is complete, the ADC is ready for data conversion. Complete the following steps to perform a data conversion.

1. Select the ADC input channel for the conversion by setting the ADC_CTRL.ch_sel field. See ADC Channel Select for details.
2. Optionally set input and reference scaling. See Scale Limitations for All Other Input Channels for details on each input channel’s scale requirements.
3. Set the data alignment for the conversion output data using the ADC_CTRL.data_align field.

    a. 0 for LSB alignment or 1 for MSB alignment. See Table 11-3 for alignment details of the ADC_DATA register.

4. Clear the ADC done interrupt flag by writing 1 to the ADC_INTR.done_if field.
5. Optionally enable the ADC done interrupt (ADC_INTR.done_ie = 1) and enable the ADC interrupt vector (ADC_IRQn).
6. Start the ADC conversion by setting the ADC_CTRL.start field to 1.
7. Poll the ADC_INTR.done_if flag until it reads 1 or wait for the ADC interrupt to occur if enabled.
8. Read the data from the ADC_DATA register and clear the ADC done interrupt flag by writing 1 to the ADC_INTR.done_if field.

### Data Conversion Output Alignment
The ADC outputs 10-bits per conversion and stores the data in the ADC_DATA register LSB justified by default. [Table 11-3](#table11-3-adc-data-register-alignment-options) shows the ADC data alignment based on the value of the ADC_CTRL.data_align bit.

*Table 11-3: ADC Data Register Alignment Options*
<a name="table11-3-adc-data-register-alignment-options"></a>

<table border="1" cellpadding="5" cellspacing="0">
<tr>
<td colspan="18"><strong><em>ADC_CTRL.data_align</em> = 0</strong></td>
</tr>
<tr>
<td colspan="2" rowspan="2"></td>
<td><strong>MSB</strong></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td><strong>LSB<strong></td>
</tr>
<tr>
<td>15</td>
<td>14</td>
<td>13</td>
<td>12</td>
<td>11</td>
<td>10</td>
<td>9</td>
<td>8</td>
<td>7</td>
<td>6</td>
<td>5</td>
<td>4</td>
<td>3</td>
<td>2</td>
<td>1</td>
<td>0</td>
</tr>
<tr>
<td colspan="2"><em>ADC_DATA</em></td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td colspan="10">data</td>
</tr>
</tbody>
</table>

<table border="1" cellpadding="5" cellspacing="0">
<tr>
<td colspan="18"><strong><em>ADC_CTRL.data_align</em> = 1</strong></td>
</tr>
<tr>
<td colspan="2" rowspan="2"></td>
<td><strong>MSB</strong></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td><strong>LSB</strong></td>
</tr>
<tr>
<td>15</td>
<td>14</td>
<td>13</td>
<td>12</td>
<td>11</td>
<td>10</td>
<td>9</td>
<td>8</td>
<td>7</td>
<td>6</td>
<td>5</td>
<td>4</td>
<td>3</td>
<td>2</td>
<td>1</td>
<td>0</td>
</tr>
<tr>
<td colspan="2"><em>ADC_DATA</em></td>
<td colspan="10">data</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
</tbody>
</table>

### Data Conversion Value Equations
Use the following equations to calculate the ADC data value for a conversion for the selected channel. If using the internal reference, V<sub>REF</sub> = 1.22V; otherwise, V<sub>REF</sub> = V<sub>DDA</sub>.

*Equation 11-3: ADC Data Calculation for Input Signal ADC_CTRL.ch_sel = 0 through 7 (AIN0 - AIN7)*
<a name="equation11-3-adc-data-calculation"></a>

$$
\text{ADC_DATA} = \text{round}\left( \left( \frac{\frac{\text{Input Signal}}{2^{\text{scale}} \cdot (\text{adc_divsel} + 1)}}{\frac{V_{\text{REF}}}{2^{\text{ref_scale}}}} \right) \cdot \left( 2^{10} - 1 \right) \right)
$$

*Note: Must satisfy [Equation 11-6](#equation11-6-input-and-reference-scale-requirement-equation).*

*Equation 11-4: ADC Data Equation for Input Signal ADC_CTRL.ch_sel = 8 through 12 (V<sub>COREA</sub>, V<sub>COREB</sub>, V<sub>RXOUT</sub>, V<sub>TXOUT</sub>, V<sub>DDA</sub>)*

$$
\text{ADC_DATA} = \text{round} \{ \left( \frac{\frac{\text{Input Signal}}{2^{\text{scale}}}}{\frac{V_{\text{REF}}}{2^{\text{ref_scale}}}} \right) \cdot \left( 2^{10} - 1 \right) \}
$$

*Note: See[ Table 11-4](#table11-4-input-and-reference-scale-support) for limitations.*

*Equation 11-5: ADC Data Calculation Input Signal ADC_CTRL.ch_sel = 14 through 16 (V<sub>DDIO</sub>, V<sub>DDIOH</sub>, V<sub>REGI</sub>)*

$$
\text{ADC_DATA} = \text{round}\left\{ \frac{\left( \frac{\frac{\text{Input Signal}}{4}}{2^{\text{scale}}} \right)}{\left( \frac{V_{\text{REF}}}{2^{\text{ref_scale}}} \right)} \cdot \left( 2^{10} - 1 \right) \right\}
$$

*Note: See[ Table 11-4](#table11-4-input-and-reference-scale-support) for limitations.*

## Reference Scaling and Input Scaling
For small signals, the ADC input, ADC reference, or both can be scaled by 50%. This enables flexibility to achieve better resolution on the ADC conversion. Each input channel supports the default of no scaling of the input (ADC_CTRL.scale = 0) and no reference scaling (ADC_CTRL.ref_scale = 0). The following sections describe the scale options for each of the ADC input channels.

### AIN0 – AIN7 Scale Limitations
The external inputs, AIN0 through AIN7, support scaling of the input by 50%, the reference by 50%, or both by 50%. Also, the scaling can further be modified by additional factors of 2, 3, or 4 as defined by ADC_CTRL.adc_divsel. The scale settings for the given input signal and reference must satisfy [Equation 11-6](#equation11-6-input-and-reference-scale-requirement-equation) to be valid:

*Equation 11-6: Input and Reference Scale Requirements Equation*
<a name="equation11-6-input-and-reference-scale-requirement-equation"></a>

$$
\frac{AINn}{2^{\text{scale}}} < \frac{V_{\text{REF}}}{2^{\text{ref_scale}}}
$$

### Scale Limitations for All Other Input Channels
The scale settings must either be disabled or enabled for the remaining internal input channels, as shown in [Table 11-4](#table11-4-input-and-reference-scale-support).

*Table 11-4: Input and Reference Scale Support by ADC Input Channel*
<a name="table11-4-input-and-reference-scale-support"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
<th>ADC Channel</th>
<th>ADC Input Signal</th>
<th><em>ADC_CTRL</em>.<em>scale</em></th>
<th><em>ADC_CTRL</em>.<em>ref_scale</em></th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2">8</td>
<td rowspan="2"><em>V</em><sub><em>COREA</em></sub></td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td rowspan="2">9</td>
<td rowspan="2"><em>V</em><sub><em>COREB</em></sub></td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td rowspan="2">10</td>
<td rowspan="2"><em>V</em><sub><em>RXOUT</em></sub></td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td rowspan="2">11</td>
<td rowspan="2"><em>V</em><sub><em>TXOUT</em></sub></td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td rowspan="2">12</td>
<td rowspan="2"><em>V</em><sub><em>DDA</em></sub></td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td rowspan="2">14</td>
<td rowspan="2"><em>V</em><sub><em>DDIO</em></sub> / 4</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td rowspan="2">15</td>
<td rowspan="2"><em>V</em><sub><em>DDIOH</em></sub> / 4</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td rowspan="2">16</td>
<td rowspan="2"><em>V</em><sub><em>REGI</em></sub> / 4</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>1</td>
</tr>
</tbody>
</table>

## Data Limits and Out of Range Interrupts
Channel limits are implemented to minimize power consumption for power supply monitoring. The ADC includes four limit registers, ADC_LIMIT0 to ADC_LIMIT3, that can be used to set a high limit, low limit, and the ADC channel number to apply the limits against. A block diagram of the limit engine for each of the four limit registers is shown in [Figure 11-2](#figure11-2-adc-limit-engine).

*Figure 11-2: ADC Limit Engine*
<a name="figure11-2-adc-limit-engine"></a>

![Figure 11-2](assets/images/figure11-2.svg)

When a measurement is taken on the ADC, the limit engine determines if the channel measured matches one of the channels selected by the limit registers. If it does and the data converted is above or below the high or low limit, an interrupt flag is set, resulting in an ADC interrupt if the interrupt is enabled.

Complete the following steps to enable a high and low limit for an ADC input channel using the ADC_LIMIT0 register. Perform these steps after the ADC is configured for measurement, and the configuration is identical for all four limit registers except for the limit register name:

1. Verify that the ADC is not actively taking a measurement by checking ADC_STATUS.active until it reads 0.
2. Set the ADC_LIMIT0.ch_sel field to the selected channel for the high and low limits.
3. Set the high limit, ADC_LIMIT0.ch_hi_limit, to the selected 10-bit trip point. An ADC measurement greater than this field on the channel selected (ADC_LIMIT0.ch_sel) generates an ADC interrupt when enabled.
4. Set the low limit, ADC_LIMIT0.ch_lo_limit, to the selected 10-bit low trip point. An ADC measurement lower than this field on the channel selected (ADC_LIMIT0.ch_sel) generates an ADC interrupt when enabled.
5. Enable the high limit, the low limit, or both interrupt signals by writing a 1 to ADC_LIMIT0.ch_high_limit_en, ADC_LIMIT0.ch_low_limit_en, or both. Note: Each limit register is independently enabled for high- and low-limit interrupts.
6. Clear the ADC interrupt high and low interrupt flags by writing 1 to ADC_INTR.hi_limit_if and ADC_LIMIT0.lo_limit_if.
7. Enable the high, low, or both interrupts for the ADC by setting ADC_INTR.hi_limit_if to 1, ADC_INTR.lo_limit_ie to 1, or both to 1.
8. If an ADC conversion occurs that is above or below the enabled limits, an ADC_IRQn is generated with the ADC_LIMIT0.adc_high_limit_if, ADC_LIMIT0.adc_low_limit_if, or both set to 1. The ADC_CTRL.ch_sel value indicates the channel that caused the interrupt, and the value of the ADC conversion that is out of bounds is in the ADC_DATA register.

## Power-Down Sequence

Complete the following steps to power down the ADC:

1. Set ADC_CTRL.pwr to 0, disabling the ADC converter power.
2. ADC_CTRL.refbuf_pwr to 0, disabling the internal reference buffer power.
3. Set ADC_CTRL.clk_en to 0, disabling the ADC internal clock.

## Comparator Operation
### Comparator 0 Usage
Comparator 0 is controlled individually using the MCR_CMP_CTRL register. Enable comparator 0 by setting the MCR_CMP_CTRL.en field to 1. Comparator 0s output is readable using the MCR_CMP_CTRL.out. Enable interrupt events for comparator 0 by setting the MCR_CMP_CTRL.int_en field to 1. Interrupts for comparator 0 occur when the output changes to its active state. The active state is controlled using the MCR_CMP_CTRL.pol field. When the output state is active, hardware automatically sets the MCR_CMP_CTRL.if flag to 1. To clear the interrupt flag, write 1 to MCR_CMP_CTRL.if.

### Low-Power Comparators 1, 2, and 3 Usage
Comparators 1, 2, and 3 are controlled using the low-power comparator, LPCOMPn, registers.

### Using Comparator 0 as a Wake-Up Source
After configuring Comparator 0, configure it as a wake-up source from SLEEP, LPM, UPM, STANDBY, and BACKUP by performing the following steps:

1. Enable comparator wake-up events by setting GCR_PM.aincomp_we to 1.
2. Enable comparator 0 as a wake-up source by setting PWRSEQ_LPPWST.comp0 to 1.
3. If desired, provide an interrupt handler for the comparators (LPCMP_IRQn).

After the device exits a low-power mode, determine if the wake-up event resulted from comparator 0 by checking the PWRSEQ_LPPWST.comp0 and the MCR_CMP_CTRL.if. Wake-up events generated by comparator 0 from STANDBY and BACKUP mode result in the PWRSEQ_LPPWST.comp0 bit being set. Write 1 to clear the PWRSEQ_LPPWST.comp0 bit and the MCR_CMP_CTRL.if bit.

### Using Low-Power Comparators 1, 2, and 3 as a Wake-Up Source
Wake up from the low-power comparators, LPCMPn, by setting PWRSEQ_LPPWST.aincomp0 bit to 1. If any of the three low-power comparators (LPCMPn) cause the device to wake up, the specific comparator’s interrupt flag is set to 1. Inspection of each comparator’s interrupt flag identifies which comparator resulted in the wake-up event. See LPCMPn.if for details.

Enable wake-up events from the low-power comparators by setting the GCR_PM.aincomp_we field to 1. If a comparator event occurs and wakes the device from a low-power operating mode, the PWRSEQ_LPPWST.aincomp0 field is set to 1. Clear the comparator wake-up status flag by writing 1 to PWRSEQ_LPPWST.aincomp0.

*Note: Comparator 0, if enabled, wakes the device from SLEEP, LPM, UPM, STANDBY, and BACKUP. If enabled, comparators 1, 2, and 3 wake the device from SLEEP, LPM, and UPM.*

## ADC Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 11-5: ADC Registers Summary*
<a name="table11-5-adc-registers-summary"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Offset</th>
        <th>Name</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>[0x0000]</td>
        <td><a href="#adc-control-register">ADC_CTRL</a></td>
        <td>ADC Control Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#adc-status-register">ADC_STATUS</a></td>
        <td>ADC Status Register</td>
    </tr>
    <tr>
        <td>[0x0008]</td>
        <td><a href="#adc-output-data-register">ADC_DATA</a></td>
        <td>ADC Output Data Register</td>
    </tr>
    <tr>
        <td>[0x000C]</td>
        <td><a href="#adc-interrupt-control-register">ADC_INTR</a></td>
        <td>ADC Interrupt Control Register</td>
    </tr>
    <tr>
        <td>[0x0010]</td>
        <td><a href="#adc-limit0-register">ADC_LIMIT0</a></td>
        <td>ADC Limit 0 Register</td>
    </tr>
    <tr>
        <td>[0x0014]</td>
        <td><a href="#adc-limit0-register">ADC_LIMIT1</a></td>
        <td>ADC Limit 1 Register</td>
    </tr>
    <tr>
        <td>[0x0018]</td>
        <td><a href="#adc-limit0-register">ADC_LIMIT2</a></td>
        <td>ADC Limit 2 Register</td>
    </tr>
    <tr>
        <td>[0x001C]</td>
        <td><a href="#adc-limit0-register">ADC_LIMIT3</a></td>
        <td>ADC Limit 3 Register</td>
    </tr>
</tbody>
</table>

### ADC Register Details

*Table 11-6: ADC Control Register*
<a name="adc-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">ADC Control</td>
       <td colspan="1">ADC_CTRL</td>
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
       <td>31:21</td>
       <td>-</td>
       <td>RO</td>
       <td>0x050</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
       <td>20</td>
       <td>data_align</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>ADC Data Alignment</strong><br>
       This field selects the alignment of the 16-bit data conversion stored in the ADC_DATA register.
        <div style="margin-left: 20px">
            0: Data is LSB justified in the 16-bit ADC_DATA register. ADC_DATA[15:10] = 0.<br>
            1: Data is MSB justified in the 16-bit ADC_DATA register. ADC_DATA[5:0] = 0.
        </div>
       </td>
    </tr>
    <tr>
       <td>19</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong><br></td>
    </tr>
    <tr>
       <td>18:17</td>
       <td>adc_divsel</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>External Input Scale</strong><br>
       Scales the external inputs AIN0-AIN7. All eight of the external inputs are scaled by the same value.
        <div style="margin-left: 20px">
            0: No scaling.<br>
            1: Divide by 2<br>
            2: Divide by 3<br>
            3: Divide by 4
        </div>
       </td>
    </tr>
    <tr>
       <td>16:12</td>
       <td>ch_sel</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>ADC Channel Select</strong><br>
       Selects the active channel for the next ADC conversion.
        <div style="margin-left: 20px">
                <table border="1" cellpadding="5" cellspacing="0">
                <tbody>
                <tr>
                <td><strong>ch_sel</td>
                <td><strong>ADC Input Channel</td>
                <td><strong>Input</td>
                </tr>
                <tr>
                <td>0x00</td>
                <td>0</td>
                <td>AIN0</td>
                </tr>
                <tr>
                <td>0x01</td>
                <td>1</td>
                <td>AIN1</td>
                </tr>
                <tr>
                <td>0x02</td>
                <td>2</td>
                <td>AIN2</td>
                </tr>
                <tr>
                <td>0x03</td>
                <td>3</td>
                <td>AIN3</td>
                </tr>
                <tr>
                <td>0x04</td>
                <td>4</td>
                <td>AIN4</td>
                </tr>
                <tr>
                <td>0x05</td>
                <td>5</td>
                <td>AIN5</td>
                </tr>
                <tr>
                <td>0x06</td>
                <td>6</td>
                <td>AIN6</td>
                </tr>
                <tr>
                <td>0x07</td>
                <td>7</td>
                <td>AIN7</td>
                </tr>
                <tr>
                <td>0x08</td>
                <td>8</td>
                <td><em>V</em><sub><em>COREA</em></sub></td>
                </tr>
                <tr>
                <td>0x09</td>
                <td>9</td>
                <td><em>V</em><sub><em>COREB</em></sub></td>
                </tr>
                <tr>
                <td>0x0A</td>
                <td>10</td>
                <td><em>V</em><sub><em>RXOUT</em></sub></td>
                </tr>
                <tr>
                <td>0x0B</td>
                <td>11</td>
                <td><em>V</em><sub><em>TXOUT</em></sub></span></td>
                </tr>
                <tr>
                <td>0x0C</td>
                <td>12</td>
                <td><em>V</em><sub><em>DDA</em></sub></td>
                </tr>
                <tr>
                <td>0x0D</td>
                <td>13</td>
                <td>Reserved</td>
                </tr>
                <tr>
                <td>0x0E</td>
                <td>14</td>
                <td><em>V</em><sub><em>DDIO</em></sub> /4</td>
                </tr>
                <tr>
                <td>0x0F</td>
                <td>15</td>
                <td><em>V</em><sub><em>DDIOH</em></sub> /4</td>
                </tr>
                <tr>
                <td>0x10</td>
                <td>16</td>
                <td><em>V</em><sub><em>REGI</em></sub> /4</td>
                </tr>
                <tr>
                <td>0x11 – 0x1F</td>
                <td>Reserved</td>
                <td>Reserved</td>
                </tr>
                </tbody>
                </table>
        </div>
       </td>
    </tr>
    <tr>
       <td>11</td>
       <td>clk_en</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>ADC Clock Enable</strong>
        <div style="margin-left: 20px">
            0: Disabled<br>
            1: Enabled
        </div>
       </td>
    </tr>
    <tr>
       <td>10</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong><br></td>
    </tr>
    <tr>
       <td>9</td>
       <td>scale</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>ADC Input Scale</strong><br>
       This field scales the ADC input by 50 percent.
        <div style="margin-left: 20px">
            0: ADC input is not scaled<br>
            1: ADC input is scaled by ½.
        </div>
        <em>Note: See Data Conversion Output Alignment for valid settings for each ADC input.</em>
       </td>
    </tr>
    <tr>
       <td>8</td>
       <td>ref_scale</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Reference Scale</strong><br>
        This field scales the internal bandgap reference by 50 percent.
        <div style="margin-left: 20px">
            0: Internal bandgap reference is not scaled.<br>
            1: Internal bandgap reference is scaled by ½.
        </div>
        <em>Note: See Data Conversion Output Alignment for valid settings for each ADC input.</em>
       </td>
    </tr>
    <tr>
       <td>7:5</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
       <td>4</td>
       <td>ref_sel</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>ADC Reference Select</strong>
        <div style="margin-left: 20px">
            0: Internal bandgap reference is used for the ADC reference<br>
            1: VDDA ÷ 2 is used for the ADC reference
        </div>
       </td>
    </tr>
    <tr>
       <td>3</td>
       <td>refbuf_pwr</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Reference Buffer Power Enable</strong>
        <div style="margin-left: 20px">
            0: Disabled<br>
            1: Enabled
        </div>
       </td>
    </tr>
    <tr>
       <td>2</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
       <td>1</td>
       <td>pwr</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>ADC Power Enable</strong><br>
       Set this field to 1 to enable power to the ADC peripheral.
        <div style="margin-left: 20px">
            0: Disabled<br>
            1: Enabled
        </div>
       </td>
    </tr>
    <tr>
       <td>0</td>
       <td>start</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Start ADC Conversion</strong><br>
      Write this bit to 1 to start an ADC conversion. When the conversion is complete, the hardware automatically sets this bit to 0, indicating the conversion is complete.
        <div style="margin-left: 20px">
            0: ADC inactive or data conversion complete.<br>
            1: Start ADC conversion. The field remains set until the conversion completes.
        </div>
       </td>
    </tr>
</table>

*Table 11-7: ADC Status Register*
<a name="adc-status-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">ADC Status</td>
       <td colspan="1">ADC_STATUS</td>
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
       <td>31:4</td>
       <td>-</td>
       <td>RO</td>
       <td>0x050</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
       <td>3</td>
       <td>overflow</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>ADC Overflow Flag</strong>
        <div style="margin-left: 20px">
            0: No overflow on the last conversion<br>
            1: Overflow on the last conversion
        </div>
       </td>
    </tr>
    <tr>
       <td>2</td>
       <td>afe_pwr_up_active</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>ADC Power-Up State</strong><br>
       This field is set to 1 when the ADC charge pump is powering up.
        <div style="margin-left: 20px">
            0: AFE is not in power-up delay.<br>
            1: AFE is currently in the power-up delay state.
        </div>
       </td>
    </tr>
    <tr>
       <td>1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
       <td>0</td>
       <td>active</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>ADC Conversion in Progress</strong>
        <div style="margin-left: 20px">
            0: ADC is idle<br>
            1: ADC conversion is in progress
        </div>
       </td>
    </tr>
</table>

*Table 11-8: ADC Data Register*
<a name="adc-output-data-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">ADC Data</td>
       <td colspan="1">ADC_DATA</td>
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
       <td>15:0</td>
       <td>data</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>ADC Data</strong><br>
       This field holds the ADC conversion output data. See Table 11-3 for details.
       </td>
    </tr>
</table>

*Table 11-9: ADC Interrupt Control Register*
<a name="adc-interrupt-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">ADC Interrupt Control</td>
       <td colspan="1">ADC_INTR</td>
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
        <td>31:23</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td>Reserved</td>
    </tr>
    <tr>
        <td>22</td>
        <td>pending</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>ADC Interrupt Pending</strong>
        <div style="margin-left: 20px">
        0: No ADC interrupt pending.<br>
        1: At least one ADC interrupt is pending, and the corresponding interrupt enable bit is set.</td>
    </tr>
    <tr>
        <td>21</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</td>
    </tr>
        <tr>
        <td>20</td>
        <td>overflow_if</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>ADC Overflow Interrupt Flag</strong>
        <div style="margin-left: 20px">
        1: The last conversion resulted in an overflow
        </div>
        </td>
    </tr>
    <tr>
    <td>19</td>
    <td>lo_limit_if</td>
    <td>R/W1C</td>
    <td>0</td>
    <td><strong>ADC Low Limit Interrupt Flag</strong>
    <div style="margin-left: 20px">
    1: The last conversion resulted in a low-limit condition for one of the limit registers.
    </div>
    </td>
    </tr>
    <tr>
        <td>18</td>
        <td>hi_limit_if</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>ADC High Limit Interrupt Flag</strong>
        <div style="margin-left: 20px">
            1: The last conversion resulted in a high-limit condition for one of the limit registers.
        </div>
        </td>
    </tr>
    <tr>
        <td>17</td>
        <td>ref_ready_if</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>ADC Reference Ready Interrupt Flag</strong>
        <div style="margin-left: 20px">
        0: Not Ready<br>
        1: Ready.
        </div>
        </td>
    </tr>
    <tr>
        <td>16</td>
        <td>done_if</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>ADC Conversion Complete Interrupt Flag</strong> <br>
        Set by the ADC hardware when an ADC conversion is complete.
        <div style="margin-left: 20px">
        1: ADC conversion complete
        </div>
        </td>
    </tr>
    <tr>
        <td>15:5</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</td>
    </tr>
    <tr>
        <td>4</td>
        <td>overflow_ie</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>ADC Overflow Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled.<br>
        1: Enables interrupt assertion when the hardware sets <em>ADC_INTR.overflow_if</em>.
        </div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>lo_limit_ie</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>ADC Low Limit Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled.<br>
        1: Enables interrupt assertion when the hardware sets the <em>ADC_INTR</em>.<em>lo_limit_if</em>.
        </div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>hi_limit_ie</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>ADC High Limit Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled.<br>
        1: Enables interrupt assertion when the hardware sets <em>ADC_INTR</em>.<em>lo_limit_if</em>.</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>ref_ready_ie</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>ADC Reference Ready Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled.<br>
        1: Enables interrupt assertion when the hardware sets <em>ADC_INTR</em>.<em>ref_ready_if</em>.
        </div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>done_ie</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>ADC Conversion Complete</strong>
        <div style="margin-left: 20px">
        0: Disabled.<br>
        1: Enables interrupt assertion when the hardware sets <em>ADC_INTR.done_if</em>.
        </div>
        </td>
    </tr>
</table>

*Table 11-10: ADC Limit 0 to 3 Registers*
<a name="adc-limit0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">ADC Limit 0</td>
       <td colspan="1">ADC_LIMIT0</td>
       <td>[0x0010]</td>
   </tr>
   <tr>
       <td colspan="3">ADC Limit 1</td>
       <td colspan="1">ADC_LIMIT1</td>
       <td>[0x0014]</td>
   </tr>
   <tr>
       <td colspan="3">ADC Limit 2</td>
       <td colspan="1">ADC_LIMIT2</td>
       <td>[0x0018]</td>
   </tr>
   <tr>
       <td colspan="3">ADC Limit 3</td>
       <td colspan="1">ADC_LIMIT3</td>
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
        <td>31</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>30</td>
        <td>ch_hi_limit_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>High Limit Monitoring Enable</strong><br>
        If set, then an ADC conversion that results in a value greater than the ch_hi_limit field generates an ADC interrupt if the ADC high-limit interrupt is enabled (ADC_INTR.hi_limit_ie = 1).
        <div style="margin-left: 20px">
        1: The high-limit comparison for the ch_sel channel is active.<br>
        0: The high-limit comparison is not enabled.
        </div>
        </td>
    </tr>
    <tr>
        <td>29</td>
        <td>ch_lo_limit_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Low Limit Monitoring Enable</strong><br>
        If set, then an ADC conversion that results in a value less than the ch_hi_limit field generates an ADC interrupt if the ADC low-limit interrupt is enabled (ADC_INTR.lo_limit_ie = 1).
        <div style="margin-left: 20px">
        1: The low-limit comparison for the ch_sel channel is active.<br>
        0: The low-limit comparison is not enabled.
        </div>
        </td>
    </tr>
    <tr>
        <td>28:24</td>
        <td>ch_sel</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>ADC Channel for Limit Monitoring</strong><br>
        This field sets the ADC input channel for high- and low-limit thresholds. See ADC_CTRL.ch_sel for valid values for this field.</td>
    </tr>
    <tr>
        <td>23:22</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>21:12</td>
        <td>ch_hi_limit</td>
        <td>R/W</td>
        <td>0x3FF</td>
        <td><strong>High Limit Threshold</strong><br>
        This field sets the threshold for high-limit comparisons. This field is a 10-bit value compared against any ADC conversion on the channel set in the ch_sel field. ADC conversions greater than this field are over threshold and can result in interrupt assertion if the ch_hi_limit_en field is set.<br>
        Valid values for this field are 0x000 to 0x3FF.
        </td>
    </tr>
    <tr>
        <td>11:10</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>9:0</td>
        <td>ch_lo_limit</td>
        <td>R/W</td>
        <td>0x3FF</td>
        <td><strong>Low Limit Threshold</strong><br>
        This field sets the threshold for low-limit comparisons. This field is a 10-bit value compared against any ADC conversion on the channel set in the ch_sel field. ADC conversions less than this field are under threshold and can result in interrupt assertion if the ch_lo_limit_en field is set.<br>
        Valid values for this field are 0x000 to 0x3FF.
        </td>
    </tr>
</table>

## Low-Power Comparator Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 11-11: Low-Power Comparator Registers Summary*
<a name="table11-11-low-power-comparator-registers-summary"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Offset</th>
        <th>Name</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>[0x0000]</td>
        <td><a href="#low-power-comparator-register">LPCMP1</a></td>
        <td>Low-Power Comparator 1 Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#low-power-comparator-register">LPCMP2</a></td>
        <td>Low-Power Comparator 2 Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#low-power-comparator-register">LPCMP3</a></td>
        <td>Low-Power Comparator 3 Register</td>
    </tr>
</tbody>
</table>


### Low-Power Comparator Register Details

*Table 11-10: ADC Limit 0 to 3 Registers*
<a name="low-power-comparator-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Low-Power Comparator 1</td>
       <td colspan="1">LPCMP1</td>
       <td>[0x0010]</td>
   </tr>
   <tr>
       <td colspan="3">Low-Power Comparator 2</td>
       <td colspan="1">LPCMP2</td>
       <td>[0x0014]</td>
   </tr>
   <tr>
       <td colspan="3">Low-Power Comparator 3</td>
       <td colspan="1">LPCMP3</td>
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
        <td>31:16</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>15</td>
        <td>if</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Low-Power Comparator n Interrupt Flag</strong><br>
        This field is set to 1 by hardware when the comparator output changes to the active state, as set using the pol field. Write 1 to clear this flag.
        <div style="margin-left: 20px">
            0: No interrupt<br>
            1: Interrupt occurred
        </div>
        </td>
    </tr>
    <tr>
        <td>14</td>
        <td>out</td>
        <td>RO</td>
        <td>*</td>
        <td><strong>Low-Power Comparator n Output</strong><br>
        This field is the comparator’s output state.
        <div style="margin-left: 20px">
            0: Output low.<br>
            1: Output high.
        </div>
        </td>
    </tr>
    <tr>
        <td>13:7</td>
        <td>-</td>
        <td>0</td>
        <td>*</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>6</td>
        <td>int_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Low-Power Comparator n Interrupt Enable</strong><br>
        Set this field to 1 to enable the interrupt for the low-power comparator.
        <div style="margin-left: 20px">
            0: Disabled<br>
            1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>5</td>
        <td>pol</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Comparator n Interrupt Polarity Select</strong><br>
        Set this field to select the polarity of the output change that generates a low-power comparator interrupt.
        <div style="margin-left: 20px">
            0: Interrupt occurs from a transition from low to high.<br>
            1: Interrupt occurs from a transition from high to low.
        </div>
        </td>
    </tr>
    <tr>
        <td>4:1</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Low-Power Comparator n Enable</strong><br>
        Set this field to 1 to enable the comparator.
        <div style="margin-left: 20px">
            0: Disabled<br>
            1: Enable
        </div>
        </td>
    </tr>
</table>



