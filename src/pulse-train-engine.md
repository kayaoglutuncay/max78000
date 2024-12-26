Each independent pulse train engine operates either in square wave mode,
which generates a continuous 50% duty-cycle square wave, or pulse train
mode, which generates a continuous programmed bit pattern from 2-bits to
32-bits in length. Pulse train engines are used independently or may be
synchronized together to generate signals in unison. The frequency of
each generated output can be set separately based on a divisor of the
peripheral clock.

## Instances
The device provides four instances of the pulse train engine peripheral.

-   PT0 to PT3

All peripheral registers share a common register set.

## Features
The pulse train outputs with individually programmable modes, patterns,
and output enables. The pulse train engine uses the PCLK, ensuring all
pulse train outputs use the same clock source. The pulse trains support
the following features:

-   Independent or synchronous pulse train output operation
-   Atomic enable and atomic disable.
-   Synchronous enable or disable of pulse train output(s) without modification to non-intended pulse train outputs.
-   Multiple output modes:

    -   Square wave output mode generates a repeating square wave (50% duty cycle).

    -   Pattern output mode for generating a customizable output wave based on a programmable bit pattern from 2 to 32 output cycles.

-   Global clock for all generated outputs
-   Individual rate configuration for each pulse train output
-   Configuration registers are modifiable while the pulse train engine is running.
-   Pulse train outputs can be halted and resumed at the same point.

## Engine
The pulse train engine uses the PCLK as the peripheral input clock. Each pulse train output is individually configurable and independently controlled.

The following sections describe the available configuration options for each individual pulse train output.

### Pulse Train Output Modes

Each pulse train output supports the following modes:

-   Pulse train mode
-   Bit pattern length
-   Square wave mode

#### Pulse Train Mode

When pulse train *n* (*PTn*) is configured in pulse train mode, the
configuration also includes the bit length (up to 32-bits) of the custom
pulse train. This is configured using the 5-bit field
*PTn_RATE_LENGTH.mode* as follows:

*PTn_RATE_LENGTH.mode* = 1:
    
*PTn* configured in square wave mode.

*PTn_RATE_LENGTH.mode* \> 1:

*PTn* is configured in pulse train mode. The value of the *mode* field is the pattern bit length.

*PTn_RATE_LENGTH.mode* = 0:

*PTn* configured for pulse train mode (32-bit pattern).

#### In Pulse Train Mode, Set the Bit Pattern

If an output is set to pulse train mode, configure a custom bit pattern from 2- to 32-bits in length in the 32-bit register *PTn_TRAIN*. The pattern is shifted out LSB first. If the output is configured in square wave mode, then the *PTn_TRAIN* register is ignored.

*Equation 22‑1: Pulse Train Mode Output Function*
<a name="equation22-1"></a>

$$PTn\text{_}TRAIN = \lbrack Bit\ pattern\ for\ PTn\rbrack$$

#### Synchronize Two or More Outputs, if Needed
The write-only register *PTG_RESYNC* "PT Global Resync" allows two or
more outputs to be reset and synchronized. Write to any bit in
*PTG_RESYNC* to simultaneously reset any outputs in pulse train mode to
the beginning of the pattern (the LSB) set in the *PTn_TRAIN*
bit-pattern register, and reset the output to 0 for outputs in square
wave mode.

#### Pulse Train Loop Mode
By default, a pulse train engine runs indefinitely until the software disables it.

A pulse train engine can be configured to repeat its pattern a specified number of times, referred to as loop mode. To select loop mode, write a non-zero value to the 16-bit field *PTn_LOOP.count*. When the pulse train engine is enabled, this field decrements by 1 each time a complete pattern is shifted through the output pin. When the count reaches 0, the output is halted, and the corresponding flag in the *PTG_INTFL* register is set.

#### Pulse Train Loop Delay
If the pulse train is configured in loop mode, a delay can be inserted
after each repeated output pattern. Write the 12-bit field
*PTn_LOOP.delay* with the number of PCLK cycles to delay between the MSB
of the last pattern to the LSB of the next pattern to enable a delay.
During this delay, the output is held at the MSB of the last pattern. If
the loop counter has not reached 0, then it is decremented when the next
pattern starts.

#### Pulse Train Automatic Restart Mode
When an engine in pulse train mode is in loop mode and stops when the
loop count reaches 0, this is called a stop event. A stop event can
optionally trigger one or more pulse trains to restart from the
beginning. This is called automatic restart mode. While only pulse train
engines operating in pulse train mode can operate in loop mode and can
optionally restart a pulse train engine, automatic restart mode can
trigger pulse train engines operating in pulse train mode or square wave
mode.

If another pulse train's stop event triggers a running pulse train
engine, automatic restart restarts the running pulse train engine from
the beginning of its pattern. If another pulse train's stop event
triggers a pulse train engine, and it is not running, automatic restart
sets the enable bit to 1 and starts the pulse train engine.

The settings for this mode are contained in the *PTn_RESTART* register
for each pulse train engine.

*Note: The configuration for automatic restart is set using the pulse
train engine(s) triggered by the automatic restart, not the pulse train
engine(s) that trigger the automatic restart. For example, the
PT8_RESTART register configures which pulse train engine triggers PT8 to
restart.*

Each pulse train engine can be configured to perform an automatic
restart when it detects a stop event from one or two pulse trains.

If *PTn_RESTART.on_pt_x_loop_exit* = 1, then pulse train engine *n*
automatically restarts when it detects a stop event from pulse train
*x*, where *x* is the value in the 5-bit field
*PTn_RESTART.pt_x*\_*select*.

If *PTn_RESTART.on_pt*\_y\_*loop*\_*exit* = 1, then pulse train engine
*n* automatically restarts when it detects a stop event from pulse train
*y*, where *y* is the value in 5-bit field *PTn_RESTART.pt_y_select*.

A pulse train engine can be configured to restart on its stop event,
allowing the pulse train to run indefinitely.

Each individual pulse train can be configured for:

-   No automatic restart.
-   Automatic restart triggered by a stop event from pulse train *x* only.
-   Automatic restart triggered by a stop event from pulse train *y* only.
-   Automatic restart triggered by a stop event from both pulse train *x* and pulse train *y*

## Enabling and Disabling a Pulse Train Output
The <a href="#table22-2">PTG_ENABLE</a> register is used to enable and disable each of the
individual pulse train outputs. Enable a given pulse train output by
setting the respective bit in the <a href="#table22-2">PTG_ENABLE</a> register. Halt a pulse
train output by clearing the respective bit in the <a href="#table22-2">PTG_ENABLE</a>
register.

*Note: Before changing a pulse train output's configuration, the
corresponding pulse train output should be halted to prevent unexpected
behavior.*

## Atomic Pulse Train Output Enable and Disable
Deterministic enable and disable operations are critical for pulse train
outputs that must be synchronized in an application. The <a href="#table22-2">PTG_ENABLE</a>
register does not perform atomic access directly. Atomic operations are
supported using the registers *PTG_SAFE_EN*, *PTG_SAFE_DIS*.

For most pulse train peripherals, enabling and disabling individual
pulse trains is performed by setting and clearing bits in the global
enable/disable register, which for this peripheral is <a href="#table22-2">PTG_ENABLE</a>. For
most Arm Cortex-M microcontrollers, this is usually done by bit banding.
Because bit banding performs a read, modify, write (RMW), some pulse
trains could start and end during the RMW operation, often with
unpredictable results.

Two additional registers are used to enable and disable the outputs to
ensure safe and predictable operation.

### Pulse Train Atomic Enable
*PTG_SAFE_EN* "Global Safe Enable" is a write-only register. To safely
enable outputs without a read/modify/write, write a 32-bit value to this
register with a 1 in the bit positions corresponding to the pulse train
engines to be enabled. This immediately sets to 1 the corresponding bits
in the <a href="#table22-2">PTG_ENABLE</a> register to 1, enabling the corresponding pulse
train engine. Writing a 0 to any bit position in the *PTG_SAFE_EN*
register does not affect the state of the corresponding pulse train
enable bit. If the corresponding pulse train engine is already enabled
and running, writing a 1 to that bit position in the *PTG_SAFE_EN*
register has no effect.

### Pulse Train Atomic Disable
*PTG_SAFE_DIS* "Global Safe Disable" is a write-only register for
disabling a pulse train engine without performing a read/modify/write.
To safely disable pulse train engines, write a 32-bit value to this
register with a 1 in the bit positions corresponding to the pulse train
engines to be disabled. This immediately clears to 0 the corresponding
bits in <a href="#table22-2">PTG_ENABLE</a>, which disables the corresponding pulse train
engines. Writing a 0 to any bit position in the *PTG_SAFE_DIS* register
does not affect the state of the corresponding pulse train enable bit.

Bit banding is not supported for the <a href="#table22-2">PTG_ENABLE</a>, *PTG_SAFE_EN*, and
*PTG_SAFE_DIS* registers and can have unpredictable results.

## Halt and Disable

Once a pulse train engine is enabled and running, it continues to run
until one of the following events stops the output:

-   The corresponding enable bit in the <a href="#table22-2">PTG_ENABLE</a> register is cleared to 0 to halt the output.
-   A 1 is written to the corresponding disable bit in the *PTG_SAFE_DIS* register to halt the output.
-   The corresponding resync bit in the *PTG_RESYNC* register is cleared to 0 to halt and reset the output.
-   *PTn_LOOP* was initialized to a non-zero value, and the loop count has reached 0 (this does not affect square wave mode; it only applies to pulse train mode).

When a pulse train is halted, the corresponding enable bit in <a href="#table22-2">PTG_ENABLE</a> is automatically cleared to 0.

## Interrupts
Each pulse train can generate an interrupt only if it is configured in
pulse train mode, and the loop counter *PTG_SAFE_DIS* was initialized to
a non-zero number. When *PTG_SAFE_DIS* counts down to 0, the
corresponding status flag in the *PTG_INTFL* register is set. If the
corresponding interrupt enable bit in the *PTG_INTEN* register is set,
the event also generates an interrupt.

## Registers
See *Table 3‑3* for the base address of this peripheral/module. If
multiple instances of the peripheral are provided, each instance has its
own independent set of the registers shown in *Table 22‑1*. Register
names for a specific instance are defined by replacing "n" with the
instance number. As an example, a register PERIPHERALn_CTRL resolves to
PERIPHERAL0_CTRL and PERIPHERAL1_CTRL for instances 0 and 1,
respectively.

*Table 22-1: Pulse Train Engine Register Summary*
<a name="table22-1"></a>

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
        <td><a href="#table22-2">PTG_ENABLE</a></td>
        <td>PT Global Enable/Disable Control</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#table22-3">PTG_RESYNC</a></td>
        <td>PT Global Resync</td>
    </tr>
    <tr>
        <td>[0x0008]</td>
        <td><a href="#table22-4">PTG_INTFL</a></td>
        <td>PT Stopped Global Status Flags</td>
    </tr>
    <tr>
        <td>[0x000C]</td>
        <td><a href="#table22-5">PTG_INTEN</a></td>
        <td>PT Global Interrupt Enable</td>
    </tr>
    <tr>
        <td>[0x0010]</td>
        <td><a href="#table22-6">PTG_SAFE_EN</a></td>
        <td>PT Global Safe Enable</td>
    </tr>
    <tr>
        <td>[0x0014]</td>
        <td><a href="#table22-7">PTG_SAFE_DIS</a></td>
        <td>PT Global Safe Disable</td>
    </tr>
    <tr>
        <td>[0x0020]</td>
        <td><a href="#table22-8">PTn_RATE_LENGTH</a></td>
        <td>PTn Configuration</td>
    </tr>
    <tr>
        <td>[0x0024]</td>
        <td><a href="#table22-9">PTn_TRAIN</a></td>
        <td>PTn Pulse Train Mode Bit Pattern</td>
    </tr>
    <tr>
        <td>[0x0028]</td>
        <td><a href="#table22-10">PTn_LOOP</a></td>
        <td>PTn Loop Control</td>
    </tr>
    <tr>
        <td>[0x002C]</td>
        <td><a href="#table22-11">PTn_RESTART</a></td>
        <td>PTn Automatic Restart</td>
    </tr>
    <tr>
        <td>[0x0030]</td>
        <td><a href="#table22-12">PTn_RATE_LENGTH</a></td>
        <td>PTn Configuration</td>
    </tr>
    <tr>
        <td>[0x0034]</td>
        <td><a href="#table22-13">PTn_TRAIN</a></td>
        <td>PT1 Pulse Train Mode Bit Pattern</td>
    </tr>
    <tr>
        <td>[0x0038]</td>
        <td><a href="#table22-14">PTn_LOOP</a></td>
        <td>PT1 Loop Control</td>
    </tr>
    <tr>
        <td>[0x003C]</td>
        <td><a href="#table22-15">PTn_RESTART</a></td>
        <td>PT1 Automatic Restart</td>
    </tr>
    <tr>
        <td>[0x0040]</td>
        <td><a href="#table22-16">PTn_RATE_LENGTH</a></td>
        <td>PT2 Configuration</td>
    </tr>
    <tr>
        <td>[0x0044]</td>
        <td><a href="#table22-17">PTn_TRAIN</a></td>
        <td>PT2 Pulse Train Mode Bit Pattern</td>
    </tr>
    <tr>
        <td>[0x0048]</td>
        <td><a href="#table22-18">PTn_LOOP</a></td>
        <td>PT2 Loop Control</td>
    </tr>
    <tr>
        <td>[0x004C]</td>
        <td><a href="#table22-19">PTn_RESTART</a></td>
        <td>PT2 Automatic Restart</td>
    </tr>
    <tr>
        <td>[0x0050]</td>
        <td><a href="#table22-20">PTn_RATE_LENGTH</a></td>
        <td>PT3 Configuration</td>
    </tr>
    <tr>
        <td>[0x0054]</td>
        <td><a href="#table22-21">PTn_TRAIN</a></td>
        <td>PT3 Pulse Train Mode Bit Pattern</td>
    </tr>
    <tr>
        <td>[0x0058]</td>
        <td><a href="#table22-22">PTn_LOOP</a></td>
        <td>PT3 Loop Control</td>
    </tr>
    <tr>
        <td>[0x005C]</td>
        <td><a href="#table22-23">PTn_RESTART</a></td>
        <td>PT3 Automatic Restart</td>
    </tr>
</tbody>
</table>

### Register Details

##### Pulse Train Engine Global Enable/Disable Register

This register enables each of the individual pulse trains. Write a 1 to
the individual pulse train enable bits to enable the corresponding pulse
train. When, for a given pulse train, the *PTn_LOOP.count* loop counter
is set to a non-zero number, when the loop counter reaches zero, then
the given pulse train engine stops, and the corresponding enable bit in
this register is cleared by hardware.

*Table 22-2: Pulse Train Engine Global Enable/Disable Register*
<a name="table22-2"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">PT Global Enable/Disable Control</th>
        <th>PTG_ENABLE</th>
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
        <td>31:4</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>pt3</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Enable PT3</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        <p><em>Note: Disabling an active pulse train halts the output and does not
        generate a stop event.</em></p></td>
    </tr>
    <tr>
        <td>2</td>
        <td>pt2</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Enable PT2</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        <p><em>Note: Disabling an active pulse train halts the output and does not
        generate a stop event.</em></p></td>
    </tr>
    <tr>
        <td>1</td>
        <td>pt1</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Enable PT1</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        <p><em>Note: Disabling an active pulse train halts the output and does not
        generate a stop event.</em></p></td>
    </tr>
    <tr>
        <td>0</td>
        <td>pt0</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Enable PT0</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        <p><em>Note: Disabling an active pulse train halts the output and does not
        generate a stop event.</em></p></td>
    </tr>
</tbody>
</table>

*Table 22-3: Pulse Train Engine Resync Register*
<a name="table22-3"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">PT Resync Register</th>
        <th>PTG_RESYNC</th>
        <th>[0x0004]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
    <td>31:4</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>pt3</td>
        <td>WO</td>
        <td>-</td>
        <td><strong>Resync Control for PT3</strong><br> Write 1 to reset the output of the pulse train. For pulse train mode, the output is restarted to the beginning of the output pattern. For square wave mode, the output is reset to 0.
        <p>Setting multiple bits simultaneously in this register synchronizes the set outputs.
        </p>
        <div style="margin-left: 20px;">
        1: Reset/restart the pulse train<br>
        0: No effect</div>
        <p><em>Note: Writing 1 has no effect if the corresponding pulse train is
        disabled.</em></p></td>
    </tr>
    <tr>
        <td>2</td>
        <td>pt2</td>
        <td>WO</td>
        <td>-</td>
        <td><strong>Resync Control for PT2</strong><br> Write 1 to reset the output of the pulse train. For pulse train mode, the output is restarted to the beginning of the output pattern. For square wave mode, the output is reset to 0.
        <p>Setting multiple bits simultaneously in this register synchronizes the set outputs.
        </p>
        <div style="margin-left: 20px;">
        1: Reset/restart the pulse train<br>
        0: No effect</div>
        <p><em>Note: Writing 1 has no effect if the corresponding pulse train is
        disabled.</em></p></td>
    </tr>
    <tr>
        <td>1</td>
        <td>pt1</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Resync Control for PT1</strong><br> Write 1 to reset the output of the pulse train. For pulse train mode, the output is restarted to the beginning of the output pattern. For square wave mode, the output is reset to 0.</p>
        <p>Setting multiple bits simultaneously in this register synchronizes the set outputs.</p>
        <div style="margin-left: 20px;">
        1: Reset/restart the pulse train<br>
        0: No effect</div>
        <p><em>Note: Writing 1 has no effect if the corresponding pulse train is
        disabled.</em></p></td>
    </tr>
    <tr>
        <td>0</td>
        <td>pt0</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Resync Control for PT0</strong><br> Write 1 to reset the output of the pulse train. For pulse train mode, the output is restarted to the beginning of the output pattern. For square wave mode, the output is reset to 0.
        <p>Setting multiple bits simultaneously in this register synchronizes the set outputs.</p>
        <div style="margin-left: 20px;">
        1: Reset/restart the pulse train<br>
        0: No effect</div>
        <p><em>Note: Writing 1 has no effect if the corresponding pulse train is
        disabled.</em></p></td>
    </tr>
</tbody>
</table>

*Table 22-4: Pulse Train Engine Stopped Interrupt Flag Register*
<a name="table22-4"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">PT Stopped Interrupt Flag Register</th>
        <th >PTG_INTFL</th>
        <th>[0x0008]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
    <td>31:4</td>
        <td>-</td>
        <td>RO</td>
        <td >0</td>
        <td ><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>pt3</td>
        <td>R/W1C</td>
        <td >0</td>
        <td ><strong>PT3 Stopped Status Flag</strong><br> This bit is set to 1 by hardware when the corresponding pulse train is in pulse train mode and the loop counter reaches 0. In square wave mode, this field is not used. Write 1 to clear.
        <p>1: Pulse Train is stopped.</p>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>pt2</td>
        <td>R/W1C</td>
        <td >0</td>
        <td ><strong>PT2 Stopped Status Flag</strong><br> This bit is set to 1 by hardware when the corresponding pulse train is in pulse train mode and the loop counter reaches 0. In square wave mode, this field is not used. Write 1 to clear.
        <p>1: Pulse Train is stopped.</p></td>
    </tr>
    <tr>
        <td>1</td>
        <td>pt1</td>
        <td>R/W1C</td>
        <td >0</td>
        <td ><strong>PT1 Stopped Status Flag</strong><br> This bit is set to 1 by hardware when the corresponding pulse train is in pulse train mode and the loop counter reaches 0. In square wave mode, this field is not used. Write 1 to clear.
        <p>1: Pulse Train is stopped.</p>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>pt0</td>
        <td>R/W1C</td>
        <td >0</td>
        <td ><strong>PT0 Stopped Status Flag</strong><br> This bit is set to 1 by hardware when the corresponding pulse train is in pulse train mode and the loop counter reaches 0. In square wave mode, this field is not used. Write 1 to clear.
        <p>1: Pulse Train is stopped.</p></td>
    </tr>
</tbody>
</table>

*Table 22-5: Pulse Train Engine Interrupt Enable Register*
<a name="table22-5"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">PT Interrupt Enable Register</th>
        <th >PTG_INTEN</th>
        <th>[0x000C]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:4</td>
        <td>-</td>
        <td>RO</td>
        <td >0</td>
        <td ><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>pt3</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>PT3 Interrupt Enable</strong><br> Write 1 to enable the interrupt for the corresponding PT when the flag is set in the <em>PTG_INTFL</em> register.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>pt2</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>PT2 Interrupt Enable</strong><br> Write 1 to enable the interrupt for the corresponding PT when the flag is set in the <em>PTG_INTFL</em> register.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>pt1</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>PT1 Interrupt Enable</strong><br> Write 1 to enable the interrupt for the corresponding PT when the flag is set in the <em>PTG_INTFL</em> register.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>pt0</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>PT0 Interrupt Enable</strong><br> Write 1 to enable the interrupt for the corresponding PT when the flag is set in the <em>PTG_INTFL</em> register.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
</tbody>
</table>

##### Pulse Train Engine Safe Enable Register

A 32-bit value written to this register performs an immediate binary OR with the contents of <a href="#table22-2">PTG_ENABLE</a>. The result is immediately stored in the <a href="#table22-2">PTG_ENABLE</a>.

*Table 22-6: Pulse Train Engine Safe Enable Register*
<a name="table22-6"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">Pulse Train Engine Safe Enable Register</th>
        <th >PTG_SAFE_EN</th>
        <th>[0x0010]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:4</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>pt3</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Enable Control for PT3</strong><br> Writing a 1 sets <a href="#table22-2">PTG_ENABLE</a>.<em>enable_pt3</em>.
        <div style="margin-left: 20px;">
        1: Enable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>pt2</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Enable Control for PT2</strong><br> Writing a 1 sets <a href="#table22-2">PTG_ENABLE</a>.<em>enable_pt2</em>.
        <div style="margin-left: 20px;">
        1: Enable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>pt1</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Enable Control for PT1</strong><br> Writing a 1 sets <a href="#table22-2">PTG_ENABLE</a>.<em>enable_pt1</em>.
        <div style="margin-left: 20px;">
        1: Enable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>pt0</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Enable Control for PT0</strong><br> Writing a 1 sets <a href="#table22-2">PTG_ENABLE</a>.<em>enable_pt0</em>.
        <div style="margin-left: 20px;">
        1: Enable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
</tbody>
</table>

##### Pulse Train Engine Safe Disable Register

A 32-bit value written to this register performs an immediate binary OR with the contents of <a href="#table22-2">PTG_ENABLE</a>. The result is immediately stored in the <a href="#table22-2">PTG_ENABLE</a>.

*Table 22-7: Pulse Train Engine Safe Disable Register*
<a name="table22-7"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">Pulse Train Engine Safe Disable Register</th>
        <th >PTG_SAFE_DIS</th>
        <th>[0x0014]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:4</td>
        <td>-</td>
        <td>RO</td>
        <td >0</td>
        <td ><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>pt3</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Disable Control for PT3</strong><br> Writing a 1 clears <em>PTG_ENABLE.enable_pt3</em>.
        <div style="margin-left: 20px;">
        1: Disable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>pt2</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Disable Control for PT2</strong><br> Writing a 1 clears <a href="#table22-2">PTG_ENABLE</a>.<em>enable_pt2</em>.
        <div style="margin-left: 20px;">
        1: Disable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>pt1</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Disable Control for PT1</strong><br> Writing a 1 clears <a href="#table22-2">PTG_ENABLE</a>.<em>enable_pt1</em>.
        <div style="margin-left: 20px;">
        1: Disable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>pt0</td>
        <td>WO</td>
        <td >-</td>
        <td ><strong>Safe Disable Control for PT0</strong><br> Writing a 1 clears <a href="#table22-2">PTG_ENABLE</a>.<em>enable_pt0</em>.
        <div style="margin-left: 20px;">
        1: Disable corresponding pulse train<br>
        0: No effect</div>
        </td>
    </tr>
</tbody>
</table>

##### Pulse Train Registers

*Table 22-8: Pulse Train Engine Configuration Register*
<a name="table22-8"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">Pulse Train <em>n</em> Configuration Register</th>
        <th>PTn_RATE_LENGTH</th>
        <th>[0x0020]</th>
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
        <td>31:27</td>
        <td>mode</td>
        <td>R/W</td>
        <td>1</td>
        <td><strong>Square Wave or Pulse Train Output Mode</strong><br> This field selects either pulse train mode with length or square wave mode.
        <div style="margin-left: 20px;">
        0: Pulse train mode, 32-bits long<br>
        1: Square wave mode<br>
        2: Pulse train mode, 2-bits long<br>
        3: Pulse train mode, 3-bits long<br>
        ...<br>
        31: Pulse train mode, 31-bits long</div>
        <p><em>Note: If this field is set to 1 square wave mode, the PTn_TRAIN register is not used.</em></p>
        </td>
    </tr>
    <tr>
        <td>26:0</td>
        <td>rate_control</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Pulse Train Enable and Rate Control</strong><br> Defines the rate at which the output for PT<em>n</em> changes state by setting the divisor of the PT clock. Setting this field to 0 disables the PT<em>n</em>. For all other values, the following equation is used to calculate the rate.:</p>
        <p><span class="math display">$$f_{PTn} =
        \frac{f_{PTE\text{_}CLK}}{rate\text{_}control}$$</span></p>
        <div style="margin-left: 20px;">
        0: Output halted<br>
        1: <em>f</em><sub><em>PTn</em></sub>=<em>f</em><sub><em>PTE</em>_<em>CLK</em></sub><br>
        2: <em>f</em><sub><em>PTn</em></sub>=<em>f</em><sub><em>PTE</em>_<em>CLK</em> /2</sub><br>
        3: <em>f</em><sub><em>PTn</em></sub>=<em>f</em><sub><em>PTE</em>_<em>CLK</em> /3</sub><br>
        ...</div></td>
    </tr>
</tbody>
</table>

*Table 22-9: Pulse Train Mode Bit Pattern Register*
<a name="table22-9"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">Pulse Train Mode Bit Pattern</th>
        <th >PTn_TRAIN</th>
        <th>[0x0024]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:0</td>
        <td>-</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>Pulse Train Mode Bit Pattern</strong><br> Write the repeating bit pattern that is shifted out, LSB first, when configured in pulse train mode. Set the bit pattern length with the <em>PTn_RATE_LENGTH.mode</em> field.
        <p><em>Note: This register is ignored in square wave mode.</em></p>
        <p><em>Note: 0x0000_0000 and 0x0001 0000  are invalid values for this register.</em></p></td>
    </tr>
</tbody>
</table>

*Table 22-10: Pulse Train n Loop Configuration Register*
<a name="table22-10"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">Pulse Train Loop Configuration</th>
        <th >PTn_LOOP</th>
        <th>[0x0028]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:28</td>
        <td>-</td>
        <td>RO</td>
        <td >0</td>
        <td ><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>27:16</td>
        <td>delay</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>Pulse Train Delay Between Loops</strong><br> Sets the delay in the number of PCLK cycles, that the output pauses between loops. The <em>PTn_LOOP</em>.<em>count</em> is decremented after the delay.</p>
        <p><em>Note: This field is ignored if software writes 0 to the PTn_LOOP.count field.
        </em></p></td>
    </tr>
    <tr>
        <td>15:0</td>
        <td>count</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>Pulse Train Loop Countdown</strong><br> Sets the number of times a pulse train pattern is repeated until it automatically stops.
        <p>Reading this field returns the number of loops remaining.</p>
        <p>When this field counts down to zero, the corresponding <em>PTG_INTFL</em> flag is set.</p>
        <p>Writing this field to 0 to repeat the pulse train pattern indefinitely.</p>
        <p><em>Note: This field is ignored in square wave mode.</em></p></td>
    </tr>
</tbody>
</table>

*Table 22-11: Pulse Train n Automatic Restart Configuration Register*
<a name="table22-11"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">Pulse Train Automatic Restart Configuration</th>
        <th >PTn_RESTART</th>
        <th>[0x002C]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th >Reset</th>
        <th >Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:16</td>
        <td>-</td>
        <td>RO</td>
        <td >0</td>
        <td ><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>15</td>
        <td>on_pt_y_loop_exit</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>Enable Automatic Restart for This Pulse Train on PTy
        Stop Event</strong>
        <div style="margin-left: 20px;">
        0: Disable automatic restart<br>
        1: When PTy has a stop event, automatically restart this pulse train
        from the beginning of its pattern.</div>
        </td>
    </tr>
    <tr>
        <td>14:11</td>
        <td>-</td>
        <td>RO</td>
        <td >0</td>
        <td ><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>12:8</td>
        <td>pt_y_select</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>Select PTy</strong><br> Select the pulse train number to be associated with PTy. This engine must be in pulse train mode.
        <div style="margin-left: 20px;">
        0b00000: PT0<br>
        0b00001: PT1<br>
        0b00010: PT2<br>
        0b00011: PT3<br>
        0b00100 - 0b11111: Reserved</div>
        </td>
    </tr>
    <tr>
        <td>7</td>
        <td>on_pt_x_loop_exit</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>Enable Automatic Restart for this Pulse Train on a <em>PTn</em> Stop Event
        </strong>
        <div style="margin-left: 20px;">
        0: Disable automatic restart<br>
        1: When <em>PTn</em> has a stop event, automatically restart the
        pulse train from the beginning of its pattern.</div>
        </td>
    </tr>
    <tr>
        <td>6:5</td>
        <td>-</td>
        <td>RO</td>
        <td >0</td>
        <td ><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>4:0</td>
        <td>pt_x_select</td>
        <td>R/W</td>
        <td >0</td>
        <td ><strong>Select <em>PTn</em></strong><br> Select the pulse train number to be associated with <em>PTn</em>. This engine must be in pulse train mode.
        <div style="margin-left: 20px;">
        0b00000: PT0<br>
        0b00001: PT1<br>
        0b00010: PT2<br>
        0b00011: PT3<br>
        0b00100-0b1xxxx: Reserved</div>
        </td>
    </tr>
</tbody>
</table>