## Overview
The RTC is a 32-bit binary timer that keeps the time of day up to 136
years. It provides time-of-day and sub-second alarm functionality in the
form of system interrupts.

The RTC operates on an external 32.768kHz time base. It can be generated
from the internal crystal oscillator driving an external 32.768kHz
crystal between the 32KIN and 32KOUT pins (ERTCO) or a 32.768kHz square
wave driven directly into the 32KIN pin. Refer to the device data sheet
for the required electrical characteristics of the external crystal.

A user-configurable, digital frequency trim is provided for applications requiring higher accuracy.

The 32-bit seconds register, [RTC_SEC](#rtc-seconds-counter-register), is incremented every time the [RTC_SSEC](#rtc-sub-second-counter-register).*ssec* sub-seconds field rolls over.

Two alarm functions are provided:

1.  A programmable time-of-day alarm provides a single event, alarm
    timer using the <a href="#rtc-time-of-day-alarm-register">RTC_TODA</a> alarm register, [RTC_SEC](#rtc-seconds-counter-register) register, and <a href="#rtc-control-register">RTC_CTRL</a>.*tod_alarm_ie* field.

2. A programmable sub-second register provides a recurring alarm using the RTC sub-second alarm register (<a href="#rtc-sub-second-alarm-register">RTC_SSECA</a>) and the <a href="#rtc-control-register">RTC_CTRL</a>.*ssec_alarm* field

The RTC is powered in the AoD or while there is a valid voltage on the V<sub>COREA</sub> device pin.

Disabling the RTC, <a href="#rtc-control-register">RTC_CTRL</a>.*en* cleared to 0, stops incrementing the [RTC_SSEC](#rtc-sub-second-counter-register), [RTC_SEC](#rtc-seconds-counter-register), and the internal RTC sub-second counter, but preserves their current values. The 32kHz oscillator is not affected by the *<a href="#rtc-control-register">RTC_CTRL</a>.*en* field. While the RTC is enabled, <a href="#rtc-control-register">RTC_CTRL</a>.*en* set to 1, the <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a>.*vrtc_tmr* field is also incremented every 32 seconds.

*Figure 18-1: MAX78000 RTC Block Diagram (12-bit Sub-Second Counter)*
<a name="figure18-1"></a>

![Figure 18-1](assets/images/figure18-1.svg)

## Instances

One instance of the RTC peripheral is provided. The RTC counter and alarm register details and description are shown in [Table 18‑1](#table18-1).

*Table 18-1: RTC Seconds, Sub-Seconds, Time-of-Day Alarm and Sub-Seconds Alarm Register Details*
<a name="table18-1"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Register</th>
        <th>Width (bits)</th>
        <th>Counter Increment</th>
        <th>Minimum</th>
        <th>Maximum</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td><a href="#rtc-seconds-counter-register">RTC_SEC</a></td>
        <td>32</td>
        <td>1 second</td>
        <td>1 second</td>
        <td>136 years</td>
        <td>Seconds Counter Register</td>
    </tr>
    <tr>
        <td><a href="#rtc-sub-second-counter-register">RTC_SSEC</a></td>
        <td>12</td>
        <td>244 µs (1 / 4096Hz)</td>
        <td>244 µs</td>
        <td>1 second</td>
        <td>Sub-Seconds Counter Register</td>
    </tr>
    <tr>
        <td><a href="#rtc-time-of-day-alarm-register">RTC_TODA</a></td>
        <td>20</td>
        <td>1 second</td>
        <td>1 second</td>
        <td>12 days</td>
        <td>Time-of-Day Alarm Register</td>
    </tr>
    <tr>
        <td><a href="#rtc-sub-second-alarm-register">RTC_SSECA</a></td>
        <td>32</td>
        <td>244 µs (1 / 4096Hz)</td>
        <td>244 µs</td>
        <td>12 days</td>
        <td>Sub-Second Alarm Register</td>
    </tr>
</tbody>
</table>

## Register Access Control
Access protection mechanisms prevent the software from accessing critical registers and fields while the hardware is updating them. Monitoring the <a href="#rtc-control-register">RTC_CTRL</a>.*busy* and <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* fields allows the software to determine when it is safe to write to registers and when registers read valid results.

*Table 18-2: RTC Register Access*
<a name="table18-2"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Register</th>
        <th>Field</th>
        <th>Read Access</th>
        <th>Write Access</th>
        <th><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 1 during Writes</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td><a href="#rtc-seconds-counter-register">RTC_SEC</a></td>
        <td>All</td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0<br />
        <a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy</em> = 1<sup>✝</sup></td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0<br />
        <a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy</em> = 1<sup>✝</sup></td>
        <td>Y</td>
        <td>Seconds Counter</td>
    </tr>
    <tr>
        <td><a href="#rtc-sub-second-counter-register">RTC_SSEC</a></td>
        <td><em>ssec</em></td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0<br />
        <a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy</em> = 1<sup>✝</sup></td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0<br />
        <a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy</em> = 1<sup>✝</sup></td>
        <td>Y</td>
        <td>Sub-Seconds Counter</td>
    </tr>
    <tr>
        <td><a href="#rtc-time-of-day-alarm-register">RTC_TODA</a></td>
        <td>All</td>
        <td>Always</td>
        <td><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0 AND</p>
        <p>(<a href="#rtc-control-register">RTC_CTRL</a>.<em>tod_alarm_ie</em> = 0 OR <a href="#rtc-control-register">RTC_CTRL</a>.<em>en</em>)</p></td>
        <td>Y</td>
        <td>Time-of-Day Alarm</td>
    </tr>
    <tr>
        <td><a href="#rtc-sub-second-alarm-register">RTC_SSECA</a></td>
        <td>All</td>
        <td>Always</td>
        <td><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0 AND</p>
        <p>(<a href="#rtc-control-register">RTC_CTRL</a>.<em>ssec_alarm_ie</em> = 0 OR
        <a href="#rtc-control-register">RTC_CTRL</a>.<em>en</em>)</p></td>
        <td>Y</td>
        <td>Sub-Seconds Alarm</td>
    </tr>
    <tr>
        <td><a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a></td>
        <td>All</td>
        <td>Always</td>
        <td><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0</p>
        <p><a href="#rtc-control-register">RTC_CTRL</a>.<em>wr_en</em> = 1</p></td>
        <td>Y</td>
        <td>Trim</td>
    </tr>
    <tr>
        <td><a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a></td>
        <td>All</td>
        <td>Always</td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>wr_en</em> = 1</td>
        <td>N</td>
        <td>Oscillator Control</td>
    </tr>
    <tr>
        <td rowspan="2"><a href="#rtc-control-register">RTC_CTRL</a></td>
        <td><em>en</em></td>
        <td>Always</td>
        <td><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> = 0</p>
        <p><a href="#rtc-control-register">RTC_CTRL</a>.<em>wr_en</em> = 1</p></td>
        <td>Y</td>
        <td>RTC Enable Field</td>
    </tr>
    <tr>
        <td>All other bits</td>
        <td>Always</td>
        <td><sup>✝✝</sup></td>
        <td>Y</td>
        <td></td>
        </tr>
        <tr>
        <td colspan="6"><p><sup>✝</sup> See the <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> Read Access Control</em> section for details.</p>
        <p><sup>✝✝</sup> See the <a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> field description for limitations on specific bits.</p></td>
    </tr>
</tbody>
</table>


### RTC_SEC and RTC_SSEC Read Access Control
Software reads of the [RTC_SEC](#rtc-seconds-counter-register) and [RTC_SSEC](#rtc-sub-second-counter-register) registers return invalid results if the read operation occurs on the same cycle that the register is being updated by the hardware (<a href="#rtc-control-register">RTC_CTRL</a>.*rdy* = 0). To avoid this,
the hardware sets the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field to 1 for 120 µs when the [RTC_SEC](#rtc-seconds-counter-register) and [RTC_SSEC](#rtc-sub-second-counter-register) registers are valid and readable by the software.

Alternately, the software can set the <a href="#rtc-control-register">RTC_CTRL</a>.*rd_en* field to 1 to allow asynchronous reads of both the [RTC_SEC](#rtc-seconds-counter-register) and [RTC_SSEC](#rtc-sub-second-counter-register) registers.

Three methods are available to ensure valid results when reading [RTC_SEC](#rtc-seconds-counter-register) and [RTC_SSEC](#rtc-sub-second-counter-register):

1.  The software clears the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field to 0.

    a. The software polls the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field until it reads 1 before reading the registers.

    b. The software must read the [RTC_SEC](#rtc-seconds-counter-register) and [RTC_SSEC](#rtc-sub-second-counter-register) registers within 120µs to ensure valid register data.

2.  The software sets the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy_ie* field to 1 to generate an RTC interrupt when the hardware sets the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field to 1.

    a. The software must service the RTC interrupt and read the [RTC_SEC](#rtc-seconds-counter-register) register and the [RTC_SSEC](#rtc-sub-second-counter-register) register while the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field is 1 to ensure valid data. This avoids the overhead associated with polling the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field.

3.  The software sets the <a href="#rtc-control-register">RTC_CTRL</a>.*rd_en* field to 1 enabling asynchronous reads of both the [RTC_SEC](#rtc-seconds-counter-register) register and the [RTC_SSEC](#rtc-sub-second-counter-register) register.

    a. The software must read consecutive identical values of each of the [RTC_SEC](#rtc-seconds-counter-register) register and the [RTC_SSEC](#rtc-sub-second-counter-register) register to ensure valid data.

### RTC Write Access Control
The read-only status field <a href="#rtc-control-register">RTC_CTRL</a>.*busy* is set to 1 by the hardware following a software instruction that writes to specific registers. The bit remains 1 while the software updates are being synchronized into the RTC. The software should not write to any registers until the hardware indicates the synchronization is complete by clearing <a href="#rtc-control-register">RTC_CTRL</a>.*busy* to 0.

## RTC Alarm Functions
The RTC provides time-of-day and sub-second interval alarm functions. The time-of-day alarm is implemented by matching the count values in the counter register with the value stored in the alarm register. The sub-second interval alarm provides an auto-reload timer driven by the trimmed RTC clock source.

### Time-of-Day Alarm
Program the RTC time-of-day alarm register (<a href="#rtc-time-of-day-alarm-register">RTC_TODA</a>) to configure the time-of-day alarm. The alarm triggers when the value stored in
<a href="#rtc-time-of-day-alarm-register">RTC_TODA</a>.*tod_alarm* matches the lower 20 bits of the [RTC_SEC](#rtc-seconds-counter-register) seconds count register. This allows programming the time-of-day-alarm to any future value between 1 second and 12 days relative to the current
time with a resolution of 1 second. Disable the time-of-day alarm before
changing the <a href="#rtc-time-of-day-alarm-register">RTC_TODA</a>.*tod_alarm* field.

When the alarm occurs, a single event sets the time-of-day alarm
interrupt flag (<a href="#rtc-control-register">RTC_CTRL</a>.*tod_alarm*) to 1.

Setting the <a href="#rtc-control-register">RTC_CTRL</a>.*tod_alarm* bit to 1 in the software results in an interrupt request to the processor if the alarm time‑of‑day interrupt enable (<a href="#rtc-control-register">RTC_CTRL</a>.*tod_alarm_ie*) bit is set to 1, and the RTC's system interrupt enable is set.

### Sub-Second Alarm
The <a href="#rtc-sub-second-alarm-register">RTC_SSECA</a> and <a href="#rtc-control-register">RTC_CTRL</a>.*ssec_alarm_ie* field control the
sub-second alarm. Writing <a href="#rtc-sub-second-alarm-register">RTC_SSECA</a> sets the starting value for the
sub-second alarm counter. Writing the sub-second alarm enable
(<a href="#rtc-control-register">RTC_CTRL</a>.*ssec_alarm_ie*) bit to 1 enables the sub‑second alarm. Once enabled, an internal alarm counter begins incrementing from the <a href="#rtc-sub-second-alarm-register">RTC_SSECA</a> value. When the counter rolls over from 0xFFFF FFFF to 0x0000 0000, the hardware sets the <a href="#rtc-control-register">RTC_CTRL</a>.*ssec_alarm* bit, triggering the alarm. At the same time, the hardware also reloads the counter with the value previously written to <a href="#rtc-sub-second-alarm-register">RTC_SSECA</a>.*ssec_alarm*.

Disable the sub-second interval alarm, <a href="#rtc-control-register">RTC_CTRL</a>.*ssec_alarm_ie*, before changing the interval alarm value, <a href="#rtc-sub-second-alarm-register">RTC_SSECA</a>.

The delay (uncertainty) associated with enabling the sub-second alarm is up to one period of the sub-second clock. This uncertainty is propagated to the first interval alarm. Thereafter, if the interval alarm remains enabled, the alarm triggers after each sub-second interval as defined without the first alarm uncertainty because the sub-second alarm is an auto-reload timer. Enabling the sub-second alarm with the sub‑second alarm register set to 0 (<a href="#rtc-sub-second-alarm-register">RTC_SSECA</a> = 0) results in the maximum sub-second alarm interval.

### RTC Interrupt and Wakeup Configuration
The following is a list of conditions that, when enabled, generate an RTC interrupt.

1.  Time-of-day alarm

2.  Sub-second alarm

3.  <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field asserted high, signaling read access permitted 

The RTC can be configured so the time-of-day and sub-second alarms are a wake-up source for exiting the following low‑power modes:

1. *SLEEP*
2. *DEEPSLEEP*
3. *UPM*
4. *BACKUP*

*Figure 18-2: RTC Interrupt/Wakeup Diagram Wake-Up Function*
<a name="figure18-2"></a>

![Figure 18-2](assets/images/figure18-2.svg)


Use the following procedure to enable the RTC as a wake-up source:

1. Configure the RTC interrupt enable bits, so one or more interrupt conditions generate an RTC interrupt.
2. Create an RTC interrupt handler function and register the address of the RTC_IRQn using the NVIC.
3. Set the *GCR_PM*.*rtc_we* field to 1 to enable the system to wake up from the RTC.
4. Enter the desired low-power mode. See *Operating Modes* for details.

### Square Wave Output

The RTC can output a 50% duty cycle square wave signal derived from the
32kHz oscillator on a selected device pin. See [Table 18‑3](#table18-3) for the
device pins, frequency options, and control fields specific to this
device. Frequencies noted as compensated are used during the RTC
frequency calibration procedure because they incorporate the frequency
adjustments provided by the digital trim function.

*Table 18-3: MAX78000 RTC Square Wave Output Configuration*
<a name="table18-3"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Function</th>
        <th>Option</th>
        <th>Control Field</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>Output Pin</td>
        <td>P3.1: SQWOUT</td>
        <td><em>MCR_OUTEN</em>.<em>sqwout_en</em> = 1</td>
    </tr>
    <tr>
        <td rowspan="4">Frequency Selection</td>
        <td>1Hz (Compensated)</td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>sqw_sel</em> = 0</td>
    </tr>
    <tr>
        <td>512Hz (Compensated)</td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>sqw_sel</em> = 1</td>
    </tr>
    <tr>
        <td>4kHz</td>
        <td><a href="#rtc-control-register">RTC_CTRL</a>.<em>sqw_sel</em> = 2</td>
    </tr>
    <tr>
        <td>32kHz</td>
        <td><a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a>.<em>32k_out</em> = 1</td>
    </tr>
    <tr>
        <td rowspan="4">Enable Frequency Output</td>
        <td>1Hz (Compensated)</td>
        <td><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>sqw_en</em> = 1</p>
        <p><a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a>.<em>32k_out</em> = 0</p></td>
    </tr>
    <tr>
        <td>512Hz (Compensated)</td>
        <td><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>sqw_en</em> = 1</p>
        <p><a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a>.<em>32k_out</em> = 0</p></td>
    </tr>
    <tr>
        <td>4kHz</td>
        <td><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>sqw_en</em> = 1</p>
        <p><a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a>.<em>32k_out</em> = 0</p></td>
    </tr>
    <tr>
        <td>32kHz</td>
        <td><a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a>.<em>32k_out</em> = 1</td>
    </tr>
</tbody>
</table>

Use the following software procedure to generate and output the square wave:

1. Select the desired frequency to output:

    a. Set the field <a href="#rtc-control-register">RTC_CTRL</a>.*sqw_sel* to 0 for a 1Hz compensated output frequency.
  
    b. Set the field <a href="#rtc-control-register">RTC_CTRL</a>.*sqw_sel* to 1 for a 512Hz compensated output frequency.

    c. Set the field <a href="#rtc-control-register">RTC_CTRL</a>.*sqw_sel* to 2 for a 4kHz output frequency.

    d. Set the field <a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a>.*32k_out* to 1 for the 32kHz frequency output.

2. Enable the system level output pin by setting the output pin as shown in [Table 18‑3](#table18-3).

3. If the selected frequency is 1Hz, 512Hz, or 4kHz, set the <a href="#rtc-control-register">RTC_CTRL</a>.*sqw_en* field to 1 to output the selected output frequency.

## RTC Calibration

A digital trim facility provides the ability to compensate for RTC inaccuracies of up to ± 127ppm when compared against an external reference clock. The trimming function utilizes an independent dedicated
timer that increments the sub-second register based on a user-supplied, twos-complement value in the <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a> register, as shown in [Figure 18‑3](#figure18-3).

*Figure 18-3: Internal Implementation of 4kHz Digital Trim*
<a name="figure18-3"></a>

![Figure 18-3](assets/images/figure18-3.svg)

Complete the following steps to perform an RTC calibration:

1. The software must configure and enable one of the compensated calibration frequencies, as described in section *Square Wave Output*.

2. Measure the frequency on the square wave output pin and compute the deviation from an accurate reference clock.

3. Clear the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field to 0.

4. Wait for the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* to be set to 1 by the hardware:

    a. Set the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy_ie* to 1 to generate an interrupt when the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field is set to 1, or

    b. Poll the <a href="#rtc-control-register">RTC_CTRL</a>.*rdy* field until it reads 1.

5. Poll the <a href="#rtc-control-register">RTC_CTRL</a>.*busy* field until it reads 0 to allow any active operations to complete.

6. Set the <a href="#rtc-control-register">RTC_CTRL</a>.*wr_en* field to 1 to allow access to the <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a>.*trim* field.

7. Write a trim value to the <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a>.*trim* field to correct for the measured inaccuracy.

8. Poll the <a href="#rtc-control-register">RTC_CTRL</a>.*busy* field until it reads 0

9. Clear the <a href="#rtc-control-register">RTC_CTRL</a>.*wr_en* field to 0.

10. Repeat the process as needed until the desired accuracy is achieved.

## Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 18-4: RTC Register Summary*
<a name="table18-4"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Offset</th>
        <th>Register</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
<tr>
    <td>[0x0000]</td>
    <td><a href="#rtc-seconds-counter-register">RTC_SEC</a></td>
    <td>RTC Seconds Counter Register</td>
</tr>
<tr>
    <td>[0x0004]</td>
    <td><a href="#rtc-sub-second-counter-register">RTC_SSEC</a></td>
    <td>RTC Sub-Second Counter Register</td>
</tr>
<tr>
    <td>[0x0008]</td>
    <td><a href="#rtc-time-of-day-alarm-register">RTC_TODA</a></td>
    <td>RTC Time-of-Day Alarm Register</td>
</tr>
<tr>
    <td>[0x000C]</td>
    <td><a href="#rtc-sub-second-alarm-register">RTC_SSECA</a></td>
    <td>RTC Sub-Second Alarm Register</td>
</tr>
<tr>
    <td>[0x0010]</td>
    <td><a href="#rtc-control-register">RTC_CTRL</a></td>
    <td>RTC Control Register</td>
</tr>
<tr>
    <td>[0x0014]</td>
    <td><a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a></td>
    <td>RTC 32KHz Oscillator Digital Trim Register</td>
</tr>
<tr>
    <td>[0x0018]</td>
    <td><a href="#rtc-oscillator-control-register">RTC_OSCCTRL</a></td>
    <td>RTC 32KHz Oscillator Control Register</td>
</tr>
</tbody>
</table>

### Register Details

*Table 18-5: RTC Seconds Counter Register*
<a name="rtc-seconds-counter-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <thead>
        <tr>
            <th colspan="3">RTC Seconds Counter</th>
            <th colspan="1">RTC_SEC</th>
            <th>[0x0000]</th>
        </tr>
        <tr>
            <th>Bits</th>
            <th>Field</th>
            <th>Access</th>
            <th>Reset</th>
            <th>Description</th>
        </tr>
    </thead>
<tbody>
    <tr>
        <td>31:0</td>
        <td>sec</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Seconds Counter</strong><br> This register is a binary count of seconds.
        </td>
    </tr>
</tbody>
</table>

*Table 18-6: RTC Sub-Second Counter Register (12-bit)*
<a name="rtc-sub-second-counter-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <thead>
        <tr>
            <th colspan="3">RTC Sub-Seconds Counter</th>
            <th colspan="1">RTC_SSEC</th>
            <th>[0x0004]</th>
        </tr>
        <tr>
            <th>Bits</th>
            <th>Field</th>
            <th>Access</th>
            <th>Reset</th>
            <th>Description</th>
        </tr>
    </thead>
<tbody>
    <tr>
        <td>31:12</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>11:0</td>
        <td>ssec</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Sub-Seconds Counter (12-bit)</strong><br> <a href="#rtc-seconds-counter-register">RTC_SEC</a> increments when this field rolls from 0x0FFF to 0x0000.
        </td>
    </tr>
</tbody>
</table>

*Table 18-7: RTC Time-of-Day Alarm Register*
<a name="rtc-time-of-day-alarm-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">RTC Time-of-Day Alarm</th>
        <th colspan="1">RTC_TODA</th>
        <th>[0x0008]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:20</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>19:0</td>
        <td>tod_alarm</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Time-of-Day Alarm</strong><br> This field sets the time-of-day alarm from 1 second up to 12-days. When this field matches <em>RTC_SEC[19:0]</em>, an RTC system interrupt is generated.
        </td>
    </tr>
</tbody>
</table>

*Table 18-8: RTC Sub-Second Alarm Register*
<a name="rtc-sub-second-alarm-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <thead>
    <tr>
        <th colspan="3">RTC Sub-Second Alarm</th>
        <th colspan="1">RTC_SSECA</th>
        <th>[0x000C]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    </thead>
<tbody>
    <tr>
        <td>31:0</td>
        <td>ssec_alarm</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Sub-second Alarm (4KHz)</strong><br> Sets the starting and reload value of the internal sub-second alarm counter. The internal counter increments and generates an alarm when the internal counter rolls from 0xFFFF FFFF to 0x0000 0000.
        </td>
    </tr>
</tbody>
</table>

*Table 18-9: RTC Control Register*
<a name="rtc-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">RTC Control Register</th>
        <th colspan="1">RTC_CTRL</th>
        <th>[0x0010]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:16</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
<tr>
    <td>15</td>
    <td>wr_en</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Write Enable</strong><br> This field controls access to the <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a> register and the RTC enable (<a href="#rtc-control-register">RTC_CTRL</a>.<em>en</em>) fields.
    <div style="margin-left: 20px;">
    1: Writes to the <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a> register and the <a href="#rtc-control-register">RTC_CTRL</a>.<em>en</em> field are allowed.<br>
    0: Writes to the <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a> register and the
    <a href="#rtc-control-register">RTC_CTRL</a>.<em>en</em> field are ignored.</div>
    <p><em>Note: Reset on System Reset, Soft Reset, and GCR_RST0.rtc assertion.</em></p></td>
</tr>
<tr>
<td>14</td>
<td>rd_en</td>
<td>R/W</td>
<td>0</td>
<td><strong>Asynchronous Counter Read Enable</strong><br> Set this field to 1 to allow direct read access of the <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> registers without waiting for <a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy.</em> Multiple consecutive reads of <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> must be executed until two consecutive reads are identical to ensure data accuracy.
    <div style="margin-left: 20px;">
    0: <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> registers are synchronized and should only be accessed while <a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy</em>= 1.<br>
    1: <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> registers are asynchronous and require software interaction to ensure data accuracy.</div>
    </td>
</tr>
    <tr>
    <td>13:11</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
</tr>
<tr>
    <td>10:9</td>
    <td>sqw_sel</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Frequency Output Select</strong><br> This field selects the RTC-derived frequency to output on the square wave output pin. See <a href="#table18-3">Table 18‑3</a> for configuration details.
    <div style="margin-left: 20px;">
    0: 1Hz (Compensated)<br>
    1: 512Hz (Compensated)<br>
    2: 4kHz
    </div>
    <p><em>Note: Reset on POR only.</em></p>
    </td>
</tr>
<tr>
    <td>8</td>
    <td>sqw_en</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Square Wave Output Enable</strong><br> This field enables the square wave output. See <a href="#table18-3">Table 18‑3</a> for configuration details.
    <div style="margin-left: 20px;">
    0: Disabled.<br>
    1: Enabled.</div>
    <p><em>Note: Reset on POR only.</em></p>
    </td>
</tr>
<tr>
    <td>7</td>
    <td>ssec_alarm</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Sub-second Alarm Interrupt Flag</strong><br> This interrupt flag is set when a sub-second alarm condition occurs. This flag is a wake-up source for the device.
    <div style="margin-left: 20px;">
    0: No sub-second alarm pending.<br>
    1: Sub-second interrupt pending.</div>
    <p><em>Note: Reset on POR only.</em></p>
    </td>
</tr>
<tr>
    <td>6</td>
    <td>tod_alarm</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Time-of-Day Alarm Interrupt Flag</strong><br> This interrupt flag is set by the hardware when a time-of-day alarm occurs.</p>
    <div style="margin-left: 20px;">
    0: No time-of-day alarm interrupt pending.<br>
    1: Time-of-day interrupt pending.</div>
    <p><em>Note: Reset on POR only.</em></p></td>
</tr>
<tr>
    <td>5</td>
    <td>rdy_ie</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>RTC Ready Interrupt Enable</strong>
    <div style="margin-left: 20px;">
    0: Disabled.<br>
    1: Enabled.</div>
    <p><em>Note: Reset on System Reset, Soft Reset, and <em>GCR_RST0</em>.<em>rtc</em> assertion.</em></p>
    </td>
</tr>
<tr>
    <td>4</td>
    <td>rdy</td>
    <td>R/W0</td>
    <td>0*</td>
    <td><strong>RTC Ready</strong><br> This bit is set to 1 for 120µs by the hardware once a hardware update of the <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> registers has occurred. The software should read <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a>> while this hardware bit is set to 1. The software can clear this bit at any
    time. An RTC interrupt is generated if <a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy_ie</em> = 1.
    <div style="margin-left: 20px;">
    0: Software reads of <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> are invalid.<br>
    1: Software reads of <a href="#rtc-seconds-counter-register">RTC_SEC</a> and <a href="#rtc-sub-second-counter-register">RTC_SSEC</a> are valid.</div>
    <p><em>Note: Reset on System Reset, Soft Reset, and <em>GCR_RST0</em>.<em>rtc</em> assertion.</em></p></td>
</tr>
<tr>
    <td>3</td>
    <td>busy</td>
    <td>RO</td>
    <td>0*</td>
    <td><strong>RTC Busy Flag</strong><br> This field is set to 1 by the hardware while a register update is in progress when the software writes to the following registers:
    <div style="margin-left: 20px;">
    <ul>
    <li><p><a href="#rtc-seconds-counter-register">RTC_SEC</a></p></li>
    <li><p><a href="#rtc-sub-second-counter-register">RTC_SSEC</a></p></li>
    <li><p><a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a></p></li>
    </ul>
    <p>The following fields cannot be written when this field is set to 1:</p>
    <ul>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>en</em></p></li>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>tod_alarm_ie</em></p></li>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>ssec_alarm_ie</em></p></li>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>rdy_ie</em></p></li>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>tod_alarm</em></p></li>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>ssec_alarm</em></p></li>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>sqw_en</em></p></li>
    <li><p><a href="#rtc-control-register">RTC_CTRL</a>.<em>rd_en</em></p></li>
    </ul>
    </div>
    This field is automatically cleared by the hardware when the update is complete. The software should poll this field until it reads 0 after changing the <a href="#rtc-seconds-counter-register">RTC_SEC</a>, <a href="#rtc-sub-second-counter-register">RTC_SSEC</a>, or <a href="#rtc-oscillator-digital-trim-register">RTC_TRIM</a> register, prior to making any other RTC register modifications.<br>
    <div style="margin-left: 20px;">
    0: RTC not busy<br>
    1: RTC busy</div>
    <p><em>Note: Reset on POR only.</em></p></td>
</tr>
<tr>
    <td>2</td>
    <td>ssec_alarm_ie</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Sub-Second Alarm Interrupt Enable</strong><br> Check the <a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> flag after writing to this field to
    determine when the RTC synchronization is complete.
    <div style="margin-left: 20px;">
    0: Disable.<br>
    1: Enable.</div>
    <p><em>Note: Reset on POR only.</em></p></td>
</tr>
<tr>
    <td>1</td>
    <td>tod_alarm_ie</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Time-of-Day Alarm Interrupt Enable</strong><br> Check the <a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> flag after writing to this field to
    determine when the RTC synchronization is complete.
    <div style="margin-left: 20px;">
    0: Disable.<br>
    1: Enable.</div>
    <p><em>Note: Reset on POR only.</em></p></td>
</tr>
<tr>
    <td>0</td>
    <td>en</td>
    <td>R/W</td>
    <td>0*</td>
    <td><strong>Real-Time Clock Enable</strong><br> This field enables the RTC. The RTC write enable
    (<a href="#rtc-control-register">RTC_CTRL</a>.<em>wr_en</em>) bit must be set, and the RTC busy
    (<a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em>) field must read 0 before writing to this field. After writing to this bit, check the <a href="#rtc-control-register">RTC_CTRL</a>.<em>busy</em> flag for 0 to determine when the RTC synchronization is complete.
    <div style="margin-left: 20px;">
    0: Disabled.<br>
    1: Enabled.</div>
    <p><em>Note: Reset on POR only.</em></p></td>
</tr>
</tbody>
</table>

*Table 18-10: RTC 32KHz Oscillator Digital Trim Register*
<a name="rtc-oscillator-digital-trim-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">RTC 32KHz Oscillator Digital Trim</th>
        <th colspan="1">RTC_TRIM</th>
        <th>[0x0014]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:8</td>
        <td>vrtc_tmr</td>
        <td>R/W</td>
        <td>0*</td>
        <td><strong>VRTC Time Counter</strong><br> The hardware increments this field every 32 seconds while the RTC is enabled.
        <p><em>Note: Reset on POR only.</em></p></td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>trim</td>
        <td>R/W</td>
        <td>0*</td>
        <td><strong>RTC Trim</strong><br> This field specifies the 2s complement value of the trim resolution. Each increment or decrement of the field adds or subtracts 1ppm at each 4kHz clock value with a maximum correction of ± 127ppm.
        <p><em>Note: Reset on POR only.</em></p></td>
    </tr>
</tbody>
</table>

*Table 18-11: RTC 32KHz Oscillator Control Register*
<a name="rtc-oscillator-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">RTC Oscillator Control</th>
        <th colspan="1">RTC_OSCCTRL</th>
        <th>[0x0018]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:6</td>
        <td>-</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>5</td>
        <td>sqw_32k</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>RTC Square Wave Output</strong>
        <div style="margin-left: 20px;">
        0: Disabled.<br>
        1: Enables the 32kHz oscillator output or the external clock source
        is output on square wave output pin. See <a href="table18-3">Table 18‑3</a> for
        configuration details.</div>
        <p><em>Note: Reset on POR only.</em></p></td>
    </tr>
    <tr>
        <td>4</td>
        <td>bypass</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>RTC Crystal Bypass</strong><br> This field disables the RTC oscillator and allows an external clock source to drive the 32KIN pin.
        <div style="margin-left: 20px;">
        0: Disable the bypass. RTC time base uses the external 32kHz crystal.<br>
        1: Enable the bypass. RTC time base is external square wave driven on 32KIN.</div>
        <p><em>Note: Reset on POR only.</em></p></td>
    </tr>
    <tr>
        <td>3:0</td>
        <td>-</td>
        <td>DNM</td>
        <td>0b1001</td>
        <td><strong>Reserved Do Not Modify</strong></td>
    </tr>
</tbody>
</table>