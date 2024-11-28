The ADC is a 10-bit sigma-delta ADC with a single-ended input multiplexer and an integrated reference generator. The multiplexer selects an input channel from either the 8 external analog input signals or the internal power supply inputs. The external analog input signals are defined as alternate functions on GPIO, as shown in Table 11-1.

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
    <td>AIN0/AIN0N</td>
    <td>P2.0</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN1/AIN0P</td>
    <td>P2.1</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN2/AIN1N</td>
    <td>P2.2</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN3/AIN1P</td>
    <td>P2.3</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN4/AIN2N</td>
    <td>P2.4</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN5/AIN2P</td>
    <td>P2.5</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN6/AIN3N</td>
    <td>P2.6</td>
    <td>AF1</td>
</tr>
<tr>
    <td>AIN7/AIN3P</td>
    <td>P2.7</td>
    <td>AF1</td>
</tr>
</table>

## Architecture
The ADC is a first-order sigma-delta converter with 10-bit output. The ADC operates at a maximum frequency of 8MHz with a fixed-sample rate as shown in Equation 11-1. Details of selecting the ADC clock frequency, fadcclk, are covered in the Clock Configuration section.

*Equation 11-1: ADC 10-bit Word Sample Rate*
<a name="equation11-1-adc-10-bit-word-sample-rate"></a>

$$ 
t_{\text{adc\_sample}} = 1024 \times \left( \frac{1}{f_{\text{adcclk}}} \right)
$$

The ADC offset is factory trimmed and automatically loaded into the ADC controller during system power-up.

The ADC uses a switched capacitor network to perform the conversion; this results in dynamic switching current and requires settling time for the external analog input signals (AIN0 – AIN7). This dynamic switching current sets the upper limit of the source impedance of the external analog input signals to approximately 10kΩ.

The ADC supports a gain of 2 × to provide additional conversion resolution if the input signals are less than half the reference voltage.

*Figure 11-1: Analog to Digital Converter Block Diagram*
<a name="figure11-1-analog-to-digital-converter-block-diagram"></a>

## Clock Configuration
The ADC clock, adcclk, is controlled by the GCR_PCLKDIV.adcfrq register field. Configure this field to achieve the target ADC sample frequency. The maximum clock frequency supported by the ADC is 8MHz. The divisor selection, GCR_PCLKDIV.adcfrq, for the ADC depends on the peripheral clock. Equation 11-2 shows the calculation for the ADC clock.

*Equation 11-2: ADC Clock Frequency*
<a name="equation11-2-adc-clock-frequency"></a>

$$
f_{\text{adcclk}} = \frac{f_{\text{PCLK}}}{\text{GCR\_PCKDIV.adcfrq}}
$$

The GCR_PCLKDIV.adcfrq field setting must result in a value for 
$$
f_{\text{adcclk}} \le 8\,\text{MHz}
$$
as shown in Table 11-2 with IPO set as the system clock.   

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
The ADC outputs 10-bits per conversion and stores the data in the ADC_DATA register LSB justified by default. Table 11-3 shows the ADC data alignment based on the value of the ADC_CTRL.data_align bit.

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
\text{ADC\_DATA} = \text{round}\left( \left( \frac{\frac{\text{Input Signal}}{2^{\text{scale}} \cdot (\text{adc\_divsel} + 1)}}{\frac{V_{\text{REF}}}{2^{\text{ref\_scale}}}} \right) \cdot \left( 2^{10} - 1 \right) \right)
$$

Note: Must satisfy Equation 11-6.

*Equation 11-4: ADC Data Equation for Input Signal ADC_CTRL.ch_sel = 8 through 12 (V<sub>COREA</sub>, V<sub>COREB</sub>, V<sub>RXOUT</sub>, V<sub>TXOUT</sub>, V<sub>DDA</sub>)*

$$
\text{ADC\_DATA} = \text{round} \{ \left( \frac{\frac{\text{Input Signal}}{2^{\text{scale}}}}{\frac{V_{\text{REF}}}{2^{\text{ref\_scale}}}} \right) \cdot \left( 2^{10} - 1 \right) \}
$$

*Equation 11-5: ADC Data Calculation Input Signal ADC_CTRL.ch_sel = 14 through 16 (V<sub>DDIO</sub>, V<sub>DDIOH</sub>, V<sub>REGI</sub>)*

$$
ADC\_ DATA = round\left\{ \left( \frac{\left( \frac{\frac{Input\ Signal}{4}}{2^{scale}} \right)}{\left( \frac{V_{REF}}{2^{ref\_ scale}} \right)} \right) \times \left( 2^{10} - 1 \right) \right\}
$$


Note: See Table 11-4 for limitations.

## Reference Scaling and Input Scaling
For small signals, the ADC input, ADC reference, or both can be scaled by 50%. This enables flexibility to achieve better resolution on the ADC conversion. Each input channel supports the default of no scaling of the input (ADC_CTRL.scale = 0) and no reference scaling (ADC_CTRL.ref_scale = 0). The following sections describe the scale options for each of the ADC input channels.

### AIN0 – AIN7 Scale Limitations
The external inputs, AIN0 through AIN7, support scaling of the input by 50%, the reference by 50%, or both by 50%. Also, the scaling can further be modified by additional factors of 2, 3, or 4 as defined by ADC_CTRL.adc_divsel. The scale settings for the given input signal and reference must satisfy Equation 11-6 to be valid:

*Equation 11-6: Input and Reference Scale Requirements Equation*

### Scale Limitations for All Other Input Channels
The scale settings must either be disabled or enabled for the remaining internal input channels, as shown in Table 11-4.

*Table 11-4: Input and Reference Scale Support by ADC Input Channel*

## Data Limits and Out of Range Interrupts
Channel limits are implemented to minimize power consumption for power supply monitoring. The ADC includes four limit registers, ADC_LIMIT0 to ADC_LIMIT3, that can be used to set a high limit, low limit, and the ADC channel number to apply the limits against. A block diagram of the limit engine for each of the four limit registers is shown in Figure 11-2.

## Power-Down Sequence

## Comparator Operation

### Comparator 0 Usage

### Low-Power Comparators 1, 2, and 3 Usage

### Using Comparator 0 as a Wake-Up Source

### Using Low-Power Comparators 1, 2, and 3 as a Wake-Up Source

## ADC Registers

*Table 11-5: ADC Registers Summary*

### ADC Register Details

*Table 11-6: ADC Control Register*

## Low-Power Comparator Registers

### Low-Power Comparator Register Details

