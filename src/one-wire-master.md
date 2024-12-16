# 1-Wire Master (OWM)

The device provides a 1‑Wire master (OWM) that the software can use to
communicate with one or more external 1‑Wire slave devices using a
single-signal, combined clock, data protocol. The OWM is contained in
the OWM module. The OWM module handles the lower-level details
(including timing and drive modes) required by the 1‑Wire protocol,
allowing the CPU to communicate over the 1‑Wire bus at a logical data
level.

## 1-Wire Master Features

The OWM provides the following features:

- Flexible 1‑Wire timing generation (required 1MHz timing base) using the
OWM module clock frequency derived from the current system clock source

- The OWM module clock can be pre-scaled to allow proper 1‑Wire timing
generation using a range of base frequencies.

- Automatic generation of proper 1‑Wire time slots for both standard and
overdrive timing modes

- Flexible configuration for 1‑Wire line pullup modes: options for
internal pullup, external fixed pullup, and optional external strong
pullup are available.

- Long-line compensation and bit-banging (direct software drive) modes

- 1‑Wire reset generation and presence-pulse detection.

- Generation of 1‑Wire read and write time slots for single-bit and
eight-bit byte transmissions.

- Search ROM Accelerator (SRA) mode simplifies the generation of
multiple-bit time slots and discrepancy resolution required when
completing the Search ROM function to determine the IDs of multiple,
unknown 1‑Wire slaves on the bus.

- Transmit data completion, received data available, presence pulse
detection, and 1‑Wire line-error condition interrupts.

For more information about the 1‑Wire protocol and supporting devices,
refer to the following resources:

- [*AN937: Book of iButton®
Standards*](https://www.analog.com/en/resources/technical-articles/book-of-ibuttonreg-standards.html)

- [*AN187: 1‑Wire Search
Algorithm*](https://www.analog.com/en/resources/app-notes/1wire-search-algorithm.html)

> iButton is a registered trademark of Maxim Integrated Products, Inc.

## 1-Wire Pins and Configuration

The one instance of the peripheral shown in [Table 17‑1](#table17-1) lists the location of the OWM_IO and OWM_PE signals.

*Table 17-1: MAX78000 1-Wire Master Peripheral Pins*
<a name="table17-1"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>OWM Instance</th>
        <th>Alternate Function Name</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td rowspan="2">OWM</td>
        <td>OWM_IO</td>
    </tr>
    <tr>
        <td>OWM_PE</td>
    </tr>
</tbody>
</table>


### 1‑Wire I/O (OWM_IO)

The OWM_IO pin is a bidirectional I/O that is used to drive the external 1‑Wire bus directly. As described in the [*Book of iButton Standards*](http://www.maximintegrated.com/AN937), this I/O is generally driven as an open-drain output. The 1‑Wire bus requires a common pullup
to return the 1‑Wire bus line to an idle high state when no master or
slave device is actively driving the line low. This pullup can consist
of a fixed resistor pullup (connected to the 1‑Wire bus outside the
microcontroller), an internal pullup enabled by setting [OWM_CFG](#owm-configuration-register).*int_pullup_enable* to 1, or an OWM module controlled external pullup enabled by setting [OWM_CFG](#owm-configuration-register).*ext_pullup_mode* to 1.

### Pullup Enable (OWM_PE)

The 1‑Wire pullup enable (PE) signal is an active high output used to
enable an optional external pullup on the 1‑Wire bus. This pullup is
intended to provide a stronger (lower impedance) pullup on the 1‑Wire
bus under certain circumstances, such as during overdrive mode.

### Clock Configuration

To correctly generate the timing required by the 1‑Wire protocol in
Standard or Overdrive timing modes, the OWM clock must be set to achieve
$f_{owmclk} = 1MHz$. This clock generates both the Standard and
Overdrive timing, so it does not need adjustment when transitioning from
Standard to Overdrive mode or vice versa.

The OWM peripheral uses the system peripheral clock, PCLK, divided by
the value in the *OWM_CLK_DIV_1US.divisor* field as shown in [Equation 17‑1](#equation17-1) 
where $f_{PCLK}\  = \ \frac{f_{SYSCLK}}{2}$.

*Equation 17-1: OWM 1MHz Clock Frequency*
<a name="equation17-1"></a>

$$f_{owmclk} = \ 1MHz = \frac{f_{PCLK}}{OWM\text{_}CLK\text{_}DIV\text{_}1US.divisor}$$

## 1-Wire Protocol
The general timing and communication protocols used by the OWM interface
are those standardized for the 1‑Wire network.

Because the 1‑Wire interface is a master interface, it initiates and
times all communication on the 1‑Wire bus. Except for the presence pulse
generation when a device first connects to the 1‑Wire bus, 1‑Wire slave
devices complete 1‑Wire bus communication only as directed by the 1‑Wire
bus master. From a software perspective, the lowest-level timing and
electrical details of how the 1‑Wire network operates are unimportant.
The application can configure the OWM module properly and direct it to
complete low-level operations such as reset, read, and write bit/byte
operations. Thus, the OWM module on the microcontroller is designed to
interface to the 1‑Wire bus at a low level.

### Networking Layers
In the [*Book of iButton Standards*](http://www.maximintegrated.com/AN937), the 1‑Wire communication protocol is described in terms of the ISO-OSI model
(International Organization of Standardization (ISO) Open System
Interconnection (OSI) Network Layer model). Network layers that apply to
this description are the Physical, Link, Network, and Transport layers.
The Transport layer consists of the software that transfers memory data
other than ROM ID contents to and from the individual 1‑Wire network
nodes. The Presentation layer corresponds to higher-level application
software functions (such as library layers) that implement communication
protocols using the 1‑Wire layers as a foundation. This document
describes the details of the physical, link, and network layers
regarding the OSI Network Layer model. The Transport and Presentation
layers are beyond the scope of this document.

##### Physical Layer
The 1‑Wire communication bus consists of a single data/power line plus
ground. Devices (either master or slave) interface to the 1‑Wire
communication bus using an open-drain (active low) connection, meaning
the 1‑Wire bus normally idles in a high state.

An external pullup resistor is used to pull the 1‑Wire line high when no
master or slave device is driving the line. This means that 1‑Wire
devices do not actively drive the 1‑Wire line high. Instead, they either
drive the line low or release it (set their output to high impedance) to
allow the external resistor to pull the line high. This allows the
1‑Wire bus to operate in a wired-AND manner, as shown in [Figure 17‑1](#figure17-1),
and avoids bus contention if more than one device attempts to drive the
1‑Wire bus at the same time.

*Figure 17-1: 1-Wire Signal Interface*
<a name="figure17-1"></a>

![Figure 17-1](assets/images/figure17-1.svg)

##### Link Layer

The 1‑Wire Bus supports a single master and one or more slave devices
(multidrop). Slave devices can connect to and disconnect from the 1‑Wire
Bus dynamically (as is typically the case with iButton devices that
operate using an intermittent touch contact interface), which means that
it is the master's responsibility to poll the bus as needed to
determine the number and types of 1‑Wire devices that are connected to
the bus.

The OWM initiates all communication sequences on the 1‑Wire Bus. The OWM
determines when 1‑Wire data transmissions begin and the overall
communication speed that is used. There are three different
communication speeds supported by the 1‑Wire specification: standard
speed, overdrive speed, and hyperdrive speed. However, only standard
speed and overdrive speed are supported by the OWM peripheral in the
devices.

###### OWM Reset and Presence Detect
The OWM begins each communication sequence by sending a reset pulse, as
shown in [Figure 17‑2](#figure17-2). This pulse resets all 1‑Wire slave devices on
the line to their initial states and causes them all to begin monitoring
the line for a command from the OWM. Each 1‑Wire slave device on the
line responds to the reset pulse by sending out a presence pulse. These
pulses from multiple 1‑Wire slave devices are combined in wired-AND
fashion, resulting in a pulse whose length is determined by the slowest
1‑Wire slave device on the bus.

*Figure 17-2: 1-Wire Reset Pulse*
<a name="figure17-2"></a>

![Figure 17-2](assets/images/figure17-2.svg)

In general, the 1‑Wire line must idle in a high state when communication
is not taking place. The master can pause communication in between time
slots. There is not an overall "timeout" period that causes a slave to
revert to the reset state if the master takes too long between one time
slot and the next time slot.

The 1‑Wire communication protocol relies on the fact that the maximum
allowable length for a bit transfer (write 0/1 or read bit) time slot is
less than the minimum length for a 1‑Wire reset. At any time, if the
1‑Wire line is held low (by the master or by any slave device) for more
than the minimum reset pulse time, all slave devices on the line
interpret this as a 1‑Wire reset pulse.

###### OWM Write Time Slot
All 1‑Wire bit time slots are initiated by the 1‑Wire bus master and
begin with a single falling edge. There is no indication given by the
beginning of a time slot if a read bit or write bit operation is
intended, as the time slots all begin in the same manner. Rather, the
1‑Wire command protocol enforces agreement between the OWM and slave as
to which time slots are used for bit writes and which time slots are
used for bit reads.

When multiple bits of a value are transmitted (or read) in sequence, the
least significant bit of the value is always sent or received first. The
1‑Wire bus is a half-duplex bus, so data is transmitted in only one
direction (from master to slave or from slave to master) at any given
time.

As shown in [Figure 17‑3](#figure17-3), the time slots for writing a 0 bit and
writing a 1 bit begin identically, with the falling edge and a
minimum-width low pulse sent by the master. To write a one bit, the
master releases the line after the minimum low pulse, allowing it to be
pulled high. To write a zero bit, the master continues to hold the line
low until the end of the time slot.

*Figure 17-3: 1-Wire Write Time Slot*
<a name="figure17-3"></a>

![Figure 17-3](assets/images/figure17-3.svg)

From the slave's perspective, the initial falling edge of the time slot
triggers the start of an internal timer, and when the proper amount of
time has passed, the slave samples the 1‑Wire line that is driven by the
master. This sampling point is in between the end of the minimum-width
low pulse and the end of the time slot.

###### OWM Read Time Slot
As with all 1‑Wire transactions, the master initiates all bit read time
slots. Like the bit write time slots, the bit read time slot begins with
a falling edge. From the master's perspective, this time slot is
transmitted identically to the "Write 1 Bit" time slot shown in [Figure 17‑3](#figure17-3). The master begins by transmitting a falling edge, holds the line low for a minimum-width period, and then releases the line.

The difference here is that instead of the slave sampling the line, the
slave begins transmitting either a 0 (by holding the line low) or a 1
(by leaving the line to float high) after the initial falling edge. The
master then samples the line to read the bit value that is transmitted
by the slave device.

For example, [Figure 17‑4](#figure17-4) shows a sequence in which the slave device
transmits data back to the 1‑Wire bus master upon request. The slave
device does not need to do anything to transmit a 1 bit. It simply
leaves the line alone (to float high) and waits for the next time slot.
The slave device holds the line low until the end of the time slot to
transmit a 0 bit.

*Figure 17-4: 1-Wire Read Time Slot*
<a name="figure17-4"></a>

![Figure 17-4](assets/images/figure17-4.svg)

###### Standard Speed and Overdrive Speed
By default, all 1‑Wire communications following reset begin at the
lowest rate of speed (that is, standard speed). For 1‑Wire devices that
support it, it is possible for the OWM to increase the rate of
communication from standard speed to overdrive speed by sending the
appropriate command.

The protocols and time slots operate identically for standard and
overdrive speeds. The difference comes in the widths of the time slots
and pulses. The OWM automatically adjusts the timings based on the
setting of the [OWM_CFG](#owm-configuration-register).*overdrive* field.

If a 1‑Wire slave device receives a standard speed reset pulse, it
resets and reverts to standard speed communication. If the device is
already communicating in overdrive mode, and it receives a reset pulse
at the overdrive speed, it resets but remains in overdrive mode.

##### Network Layer
###### ROM Commands
Following the initial 1‑Wire reset pulse on the bus, all slave 1‑Wire
devices are active, which means they are monitoring the bus for
commands. Because the 1‑Wire bus can have multiple slave devices present
on the bus at any time, the OWM must go through a process (defined by
the 1‑Wire command protocol) to activate only the 1‑Wire slave device it
intends to communicate with and deactivate all others. This is the
purpose of the ROM commands (network layer) shown in [Table 17‑2](#table17-2).

*Table 17-2: 1-Wire ROM Commands*
<a name="table17-2"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>ROM Command</th>
        <th>Hex Value</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>Read ROM</td>
        <td>0x33</td>
    </tr>
    <tr>
        <td>Match ROM</td>
        <td>0x55</td>
    </tr>
    <tr>
        <td>Search ROM</td>
        <td>0xF0</td>
    </tr>
    <tr>
        <td>Skip ROM</td>
        <td>0xCC</td>
    </tr>
    <tr>
        <td>Overdrive Skip ROM</td>
        <td>0x3C</td>
    </tr>
    <tr>
        <td>Overdrive Match ROM</td>
        <td>0x69</td>
    </tr>
    <tr>
        <td>Resume Communication</td>
        <td>0xA5</td>
    </tr>
</tbody>
</table>

The ROM command layer relies on the fact that all 1‑Wire slave devices are assigned a globally unique, 64-bit ROM ID. This ROM ID value is factory programmed to ensure that no two 1‑Wire slave devices have the same value.

###### ROM ID
[Figure 17‑5](#figure17-5) is a visual representation of the 1‑Wire ROM ID fields and shows the organization of the fields within the 64-bit ROM ID for a device.

*Figure 17-5: 1-Wire ROM ID Fields*
<a name="figure17-5"></a>

![Figure 17-5](assets/images/figure17-5.svg)

[Table 17‑3](#table17-3) provides a detailed description of each of the ROM ID
fields.

*Table 17-3: 1-Wire Slave Device ROM ID Field*
<a name="table17-3"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Field</th>
        <th>Bit Number</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>Family code</td>
        <td>0-7</td>
        <td>This 8-bit value is used to identify the type of a 1‑Wire slave device.</td>
    </tr>
    <tr>
        <td>Unique ID</td>
        <td>8-55</td>
        <td>This 48-bit value is factory-programmed to give each 1‑Wire slave device (within a given family code group) a globally unique identifier.</td>
    </tr>
    <tr>
        <td>CRC</td>
        <td>56-63</td>
        <td>This is the 8-bit, 1‑Wire CRC as defined in the <a href="http://www.maximintegrated.com/AN937"><em>Book of iButton Standards</em></a>. The CRC is generated using the polynomial (x<sup>8</sup> + x<sup>5</sup> + x<sup>4</sup> + 1 ).</td>
    </tr>
</tbody>
</table>

*Note: For certain operations that consist only of writing from the OWM
to the slave, it is technically possible for the master to communicate
with more than one slave at a time on the same 1‑Wire bus. For this to
work, the exact same data must be transmitted to all slave devices, and
any values read back from the slaves must either be identical as well or
must be disregarded by the master device (because different slaves can
attempt to transmit different values). The following descriptions
assume, however, that the master is communicating with only one slave
device at a time because this is the method normally used.*

As explained above, the ROM ID contents play a key role in addressing
and selecting devices on the 1‑Wire bus. All devices except one are in
an idle/inactive state after the Match ROM command or the Search ROM
command is executed. They return to the active state only after
receiving a 1‑Wire reset pulse.

Devices with overdrive capability are distinguished from others by their
family code and two additional ROM commands (Overdrive Skip ROM and
Overdrive Match ROM). The first transmission of the ROM command itself
takes place at the normal speed understood by all 1‑Wire devices. After
a device with overdrive capability is addressed and set into overdrive
mode (that is, after the appropriate ROM command is received), further
communication to that device must occur at overdrive speed. Because all
deselected devices remain in the idle state if no reset pulse of regular
duration is detected, even multiple overdrive components can reside on
the same 1‑Wire bus. A reset pulse of regular duration resets all 1‑Wire
devices on the bus and simultaneously sets all overdrive-capable devices
back to the default standard speed.

### Read ROM Command
The Read ROM command allows the OWM to obtain the 8-byte ROM ID of any
slave device connected to the 1‑Wire bus. Each slave device on the bus
responds to this command by transmitting all eight bytes of its ROM ID
value, starting with the least significant byte (Family Code) and ending
with the most significant byte (CRC).

Because this command is addressed to all 1‑Wire devices on the bus, if
more than one slave is present on the bus, there is a data collision as
multiple slaves attempt to transmit their ROM IDs at once. This
condition is detectable by the OWM because the CRC value does not match
the ROM ID value received. In this case, the OWM should reset the 1‑Wire
bus and select a single slave device on the bus to continue either by
using the Match ROM command (if the ROM ID values are already known) or
the Search ROM command (if the master has not yet identified some or all
devices on the bus).

After the Read ROM command is complete, all slave devices on the 1‑Wire
bus are selected or active, and communication proceeds to the Transport
layer.

### Skip ROM and Overdrive Skip ROM Commands
The Skip ROM command is used to activate all slave devices present on
the 1‑Wire bus regardless of their ROM ID. Normally, this command is
used when only a single 1‑Wire slave device is connected to the bus.
After the Skip ROM command is complete, all slave devices on the 1‑Wire
bus are selected or active, and communication proceeds to the Transport
layer.

The Overdrive Skip ROM command operates in an identical manner except
that running it also causes the receiving slave devices to shift
communication speed from standard speed to overdrive speed. The
Overdrive Skip ROM command byte itself (0x3C) is transmitted at standard
speed. All subsequent communication is sent at overdrive speed.

### Match ROM and Overdrive Match ROM Commands
The Match ROM command is used by the OWM to select one and only one
slave 1‑Wire device when the ROM ID of the device is already determined.
When transmitting this command, the master sends the command byte (that
is, 0x55 for standard speed and 0x69 for overdrive speed) and then sends
the entire 64-bit ROM ID for the device selected, least significant bit
first.

During the transmission of the ROM ID by the master, all slave devices
monitor the bus. As each bit is transmitted, each of the slave devices
compares it against the corresponding bit of their ROM ID. If the bits
match, the slave device continues to monitor the bus. If the bits do not
match, the slave device transitions to the inactive state (waiting for a
1‑Wire reset) and stops monitoring the bus.

At the end of the transmission, at most one slave device is active,
which is the slave device whose ROM ID matched the ROM ID that was
transmitted. All other slave devices are inactive. Communication then
proceeds to the Transport layer for the device that was selected.

The Overdrive Match ROM command operates in an identical manner except
that it also causes the slave device selected by the command to shift
communication speed from standard speed to overdrive speed. The
Overdrive Match ROM command byte (0x69) and the 64-bit ROM ID bits are
transmitted at standard speed. All subsequent communication is sent at
overdrive speed.

### Search ROM Command
The Search ROM command allows the OWM to determine the ROM ID values of
all 1‑Wire slave devices connected to the bus using an iterative search
process. Each execution of the Search ROM command reveals the ROM ID of
one slave device on the bus.

The operation of the Search ROM command resembles a combination of the
Read ROM and Match ROM commands. First, all slaves on the bus transmit
the least significant bit (bit 0) of their ROM IDs. Next, all slaves on
the bus transmit a complement of the same bit. By analyzing the two bits
received, the master can determine if the bit 0 values were 0 for all
slaves, 1 for all slaves, or a combination of the two. Next, the master
selects which slaves remain activated for the next step in the Search
ROM process by transmitting the bit 0 value for the slaves it selects.
All slaves whose bit 0 matches the value transmitted by the master
remain active, while slaves with a different bit 0 value go to the
inactive state and do not participate in the remainder of the Search ROM
command.

Next, the same process is followed for bit 1, then bit 2, and so on
until the 63rd bit (most significant bit) of the ROM ID is transmitted.
At this point, only one slave device remains active, and the master can
either continue with communication at the Transport layer or issue a
1‑Wire reset pulse to go back for another pass at the Search ROM
command.

The [*Book of iButton Standards*](http://www.maximintegrated.com/AN937)
goes into more detail about the process used by the master to obtain ROM
IDs of all devices on the 1‑Wire bus using multiple executions of the
Search ROM command. The algorithm resembles a binary tree search and is
used regardless of how many devices are on the bus.

There is no overdrive equivalent version of the Search ROM command.

### Search ROM Accelerator Operation
The OWM module provides a special accelerator mode for use with the Search ROM command to allow the Search ROM command to process more quickly. This mode is activated by setting [OWM_CTRL_STAT](#owm-control-status-register).*sra_mode* to 1.

When this mode is active, ROM IDs being processed by the Search ROM
command are broken into 4-bit nibbles where the current 64-bit ROM ID
varies with each pass through the search algorithm. Each 4-bit
processing step is initiated by writing the 4-bit value to [OWM_DATA](#owm-data-buffer-register).*tx_rx*. This causes the generation of twelve 1‑Wire time slots
by the OWM as each bit in the 4-bit value (starting with the LSB)
results in a read of two bits (all active slaves transmitting bit N of
their ROM IDs, then all active slaves transmitting the complement of bit
N of their ROM ID), and then a write of a single bit by the OWM.

After the 4-bit processing stage is complete, the return value is loaded
into [OWM_DATA](#owm-data-buffer-register).*tx_rx* consists of 8 bits. The low nibble (bits 0 through 3) contains the four discrepancy flags: one for each ID bit processed.
If the discrepancy bit is set to 1, it means that either two slaves with
differing ID bits in that position both responded (the 2 bits read were
both zero), or no slaves responded (the 2 bits read were both 1). If the
discrepancy bit is set to 0, then the 2 bits read were complementary
(either 0, 1 or 1, 0), meaning there was no bus conflict.

In this way, at each step in the Search ROM command, the master either
follows the ID of the responding slaves or deselects some of the slaves
on the bus in case of a conflict. By the time the end of the 64-bit ROM
ID is reached (the sixteenth 4-bit group processing step), the
combination of all bits from the high nibbles of the received data are
equal to the ROM ID of one of the slaves remaining on the bus.
Subsequent passes through the Search ROM algorithm are used to determine
additional slave ROM ID values until all slaves are identified. Refer to
the *[Book of iButton Standards](http://www.maximintegrated.com/AN937)*
for a detailed explanation of the search function and possible variants
of the search algorithm applicable to specific circumstances.

### Resume Communication Command
If more than one 1‑Wire slave device is on the bus, then the master must
specify which one it wishes to communicate with each time a new 1‑Wire
command (starting with a reset pulse) begins. Using the commands
discussed previously, this would normally involve sending the Match ROM
command each time, which means the master must explicitly specify the
full 64-bit ROM ID of the part it communicates with for each command.

The Resume Communication command provides a shortcut for this process by
allowing the master to repeatedly select the same device for multiple
commands without having to transmit the full ROM ID each time.

When the OWM selects a single device (using the Match ROM or Search ROM
commands), an internal flag called the RC (for Resume Communication)
flag is set in the slave device. (Only one device on the bus has this
flag set at any one time; the Skip ROM command selects multiple devices,
but the RC flag is not set by the Skip ROM command.)

When the master resets the 1‑Wire bus, the RC flag remains set. At this
point, it is possible for the master to send the Resume Communication
command. This command does not have a ROM ID attached to it, but the
device that has the RC flag set responds to this command by going to the
active state while all other devices deactivate and drop off the 1‑Wire
bus.

Issuing any other ROM command clears the RC flag on all devices. So, for
example, if a Match ROM command is issued for device A, its RC flag is
set. The Resume Communication command can then be used repeatedly to
send commands to device A. If a Match ROM command is then sent with the
ROM ID of device B, the RC flag on device A will clear to 0, and the RC
flag on device B is set.

## 1-Wire Operation
Once the OWM peripheral is correctly configured, then using the OWM
peripheral to communicate with the 1‑Wire network involves directing the
OWM to generate the proper reset, read, and write operations to
communicate with the 1‑Wire slave devices used in a specific
application.

The OWM manages the following 1‑Wire protocol primitives directly in either Standard or Overdrive mode:

- 1‑Wire bus reset (including detection of presence pulse from responding
slave devices).
- Write single bit (a single write time slot).
- Write 8-bit byte, least significant bit first (eight write time slots).
- Read single bit (a single write-1 time slot).
- Read 8-bit byte, least significant bit first (eight write-1 time slots).
- Search ROM Acceleration Mode allowing the generation of four groups of
three time slots (read, read, and write) from a single 4-bit register
write to support the Search ROM command.

### Resetting the OWM
The first step in any 1‑Wire communication sequence is to reset the
1‑Wire bus. To direct the OWM module to complete a 1‑Wire reset, write [OWM_CTRL_STAT](#owm-control-status-register).*start_ow_reset* to 1. This generates a reset pulse and
checks for a replying presence pulse from any connected slave devices.

Once the reset time slot is complete, the [OWM_CTRL_STAT](#owm-control-status-register).*start_ow_reset* field is automatically cleared to zero. Then, the interrupt flag [OWM_INTFL](#owm-interrupt-flag-register).*ow_reset_done* is set to 1 by the hardware. This flag must be cleared by writing a 1 bit to the flag.

If a presence pulse is detected on the 1‑Wire bus during the reset sequence (that should normally be the case unless no 1‑Wire slave devices are present on the bus), the [OWM_CTRL_STAT](#owm-control-status-register).*presence_detect* flag is also set to 1. This flag does not result in the generation of an interrupt.

## 1-Wire Data Reads
### Reading a Single Bit Value from the 1‑Wire Bus
The procedure for reading a single bit is like the procedure for writing
a single bit because the operation is completed by writing a 1 bit that
the slave device either leaves unchanged (to transmit a 1 bit) or
overrides by forcing the line low (to transmit a 0 bit).

To read a single bit value from the 1‑Wire Bus, complete the following
steps:

1.  Set [OWM_CFG](#owm-configuration-register).*single_bit_mode* to 1. This setting causes the OWM
    to transmit/receive a single bit of data at a time instead of the default 8 bits.

2.  Write [OWM_DATA](#owm-data-buffer-register).*tx_rx* to 1. Only bit 0 of this field is used in
    this instance; the other bits in the field are ignored. Writing to
    the [OWM_DATA](#owm-data-buffer-register) register initiates the read of the bit on the 1‑Wire
    bus.

3.  Once the single-bit transmission is complete, the hardware sets
    the interrupt flag [OWM_INTFL](#owm-interrupt-flag-register).*tx_data_empty* to 1. This flag (that
    triggers an OWM module interrupt if [OWM_INTEN](#owm-interrupt-enable-register).*tx_data_empty* is
    also set to 1) is cleared by writing a 1 to the flag.

4.  As the hardware shifts the bit value out, it also samples the
    value returned from the slave device. Once this value is ready to
    read, the interrupt flag [OWM_INTFL](#owm-interrupt-flag-register).*rx_data_ready* is set to 1. If
    [OWM_INTEN](#owm-interrupt-enable-register).*rx_data_ready* is set to 1, an OWM module interrupt
    occurs.

5.  Read [OWM_DATA](#owm-data-buffer-register).*tx_rx* (only bit 0 is used) to determine the
    value returned by the slave device. Note that if no slave devices
    are present or the slaves are not communicating with the master, bit
    0 remains set to 1.

### Reading an 8-Bit Value from the 1‑Wire Bus
The procedure for reading an 8-bit byte is like the procedure for
writing an 8-bit byte because the operation is completed by writing
eight 1 bits that the slave device either leaves unchanged (to transmit
1 bits) or overrides by forcing the line low (to transmit 0 bits).

6.  Set [OWM_CFG](#owm-configuration-register).*single_bit_mode* to 0. This setting causes the OWM
    to transmit/receive in the default 8-bit mode.

7.  Write [OWM_DATA](#owm-data-buffer-register).*tx_rx* to 0x0FFh.

8.  Once the 8-bit transmission completes, the hardware sets the
    interrupt flag [OWM_INTFL](#owm-interrupt-flag-register).*tx_data_empty* to 1. This flag (that
    triggers an OWM module interrupt if [OWM_INTEN](#owm-interrupt-enable-register).*tx_data_empty* is
    also set to 1) is cleared by writing a 1 to the flag.

9.  As the hardware shifts the bit values out, it also samples the
    values returned from the slave device. Once the full 8-bit value is
    ready to be read, the interrupt flag [OWM_INTFL](#owm-interrupt-flag-register).*rx_data_ready* is
    set to 1. If [OWM_INTEN](#owm-interrupt-enable-register).*rx_data_ready* is set to 1, an OWM module
    interrupt occurs.

10. Read [OWM_DATA](#owm-data-buffer-register).*tx_rx* to determine the 8-bit value returned by
    the slave device. *Note that if no slave devices are present or the
    slave devices are not communicating with the master, the return
    value 0x0FF is the same as the transmitted value.*

## Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 17-4: OWM Register Summary*
<a name="table17-4"></a>

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
        <td><a href="#owm-configuration-register">OWM_CFG</a></td>
        <td>OWM Configuration Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#owm-clock-divisor-register">OWM_CLK_DIV_1US</a></td>
        <td>OWM Clock Divisor Register</td>
    </tr>
    <tr>
        <td>[0x0008]</td>
        <td><a href="#owm-control-status-register">OWM_CTRL_STAT</a></td>
        <td>OWM Control/Status Register</td>
    </tr>
    <tr>
        <td>[0x000C]</td>
        <td><a href="#owm-data-buffer-register">OWM_DATA</a></td>
        <td>OWM Data Buffer Register</td>
    </tr>
    <tr>
        <td>[0x0010]</td>
        <td><a href="#owm-interrupt-flag-register">OWM_INTFL</a></td>
        <td>OWM Interrupt Flag Register</td>
    </tr>
    <tr>
        <td>[0x0014]</td>
        <td><a href="#owm-interrupt-enable-register">OWM_INTEN</a></td>
        <td>OWM Interrupt Enable Register</td>
    </tr>
</tbody>
</table>

### Register Details

*Table 17-5: OWM Configuration Register*
<a name="owm-configuration-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th colspan="3">OWM Configuration Register</th>
    <th colspan="1">OWM_CFG</th>
    <th>[0x0000]</th>
</tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:8</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>7</td>
        <td>int_pullup_enable</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Internal Pullup Enable</strong><br> Set this field to enable the internal pullup resistor.
        <div style="margin-left: 20px;">
        0: Internal pullup disabled.<br>
        1: Internal pullup enabled.</div>
        </td>
    </tr>
    <tr>
        <td>6</td>
        <td>overdrive</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Overdrive Enable</strong><br> Set this field to 1 to enable overdrive mode for 1‑Wire communications. Clearing this field sets 1‑Wire communications to standard speed.
        <div style="margin-left: 20px;">
        0: Overdrive mode disabled, standard speed mode. <br>
        1: Overdrive mode enabled.</div>
        </td>
    </tr>
    <tr>
        <td>5</td>
        <td>single_bit_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Bit Mode Enable</strong><br>When set to 1, only a single bit at a time is transmitted and received (LSB of <a href="#owm-data-buffer-register">OWM_DATA</a>) rather than the whole byte.
        <div style="margin-left: 20px;">
        0: Byte mode enabled, single bit mode disabled. <br>
        1: Single bit mode enabled, byte mode disabled.</div>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>ext_pullup_enable</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>External Pullup Enable</strong><br> Enables external FET pullup when the 1‑Wire master is idle. FET is designed to pull the wire high regardless of its enable state (that is, high or low). Idle means the 1‑Wire master is idle, and there are no 1‑Wire accesses in progress.
        <div style="margin-left: 20px;">
        0: External pullup pin is not driven to high. <br>
        1: External pullup pin is driven high when the 1‑Wire bus is idle, actively pulling the 1‑Wire IO high.</div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>ext_pullup_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>External Pullup Mode</strong><br> Provides an extra output to control an external pullup. For long wires, a pullup resistor strong enough to pull the wire high in a
        reasonable amount of time might need to be so strong that it would be difficult to drive the line low. In this case, implement an external FET to actively drive the wire high for a brief amount of time. Then, let the resistor keep the line high.</td>
    </tr>
    <tr>
        <td>2</td>
        <td>bit_bang_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Bit-Bang Mode Enable</strong><br> Enable bit-bang control of the I/O pin. If this bit is set to 1, <a href="#owm-control-status-register">OWM_CTRL_STAT</a>.<em>bit_bang_oe</em> controls the state of the I/O pin.
        <div style="margin-left: 20px;">
        0: Bit-bang mode disabled. <br>
        1: Bit-bang mode enabled.</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>force_pres_det</td>
        <td>R/W</td>
        <td>1</td>
        <td><strong>Presence Detect Force</strong><br>Setting this bit to 1 drives the OWM_IO pin low during presence detection. Use this bit field to prevent a large number of 1‑Wire slaves on the bus from all responding at different times, which might cause ringing. When this bit is set to 1, the <a href="#owm-control-status-register">OWM_CTRL_STAT</a>.<em>presence_detect</em> bit is always set as the result of a 1‑Wire reset even if no slave devices are present on the bus.
        <div style="margin-left: 20px;">
        0: OWM_IO pin floats during presence detection portion of 1‑Wire reset. <br>
        1: OWM_IO pin is driven low during presence detection portion of 1‑Wire reset.</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>long_line_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Long Line Mode Enable</strong> <br> Selects alternate timings for 1‑Wire communication. The recommended setting depends on the length of the wire. For lines less than 40 meters, 0 should be used.
        <p>Setting this bit to 0 leaves the write one release, the data
        sampling, and the time-slot recovery times at approximately 5μs, 15μs,
        and 7μs, respectively.</p>
        <p>Setting this bit to 1 enables long line mode timings during standard
        mode communications. This mode moves the write one release, the data
        sampling, and the time-slot recovery times out to approximately 8μs,
        22μs, and 14μs, respectively.</p>
        <div style="margin-left: 20px;">
        0: Standard operation for lines less than 40 meters. <br>
        1: Long Line mode enabled.</div>
        </td>
    </tr>
</tbody>
</table>

*Table 17-6: OWM Clock Divisor Register*
<a name="owm-clock-divisor-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">OWM Clock Divisor</th>
        <th colspan="1">OWM_CLK_DIV_1US</th>
        <th>[0x0004]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>31:8</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>divisor</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>OWM Clock Divisor</strong>
        <p>Divisor for the OWM peripheral clock. The target is to achieve a 1MHz
        clock. See the <em>Clock Configuration</em> section for details.</p>
        <p>0x00: OWM clock disabled.</p>
        <p>0x01: <span class="math display">f<sub>owmclk</sub> = f<sub>PCLK</sub> / 1</span></p>
        <p>0x02: <span class="math display">f<sub>owmclk</sub> = f<sub>PCLK</sub> / 2</span></p>
        <p>…</p>
        <p>0xFF: <span class="math display">f<sub>owmclk</sub> = f<sub>PCLK</sub> / 255</span></p></td>
    </tr>
</tbody>
</table>

*Table 17-7: OWM Control Status Register*
<a name="owm-control-status-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
<th colspan="3">OWM Control Status</th>
<th colspan="1">OWM_CTRL_STAT</th>
<th>[0x0008]</th>
</tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
<tr>
<td>31:6</td>
<td>-</td>
<td>RO</td>
<td>0</td>
<td><strong>Reserved</strong></td>
</tr>
<tr>
<td>5</td>
<td>presence_detect</td>
<td>R</td>
<td>0</td>
<td><strong>Presence Detect Flag</strong> <br> Set to 1 when a presence pulse is detected from one or more slaves during the 1‑Wire reset sequence.</p>
<div style="margin-left: 20px;">
0: No presence detect pulse during previous 1‑Wire reset sequence.
1: Presence detect pulse on bus during previous 1‑Wire reset sequence.</div>
</td>
</tr>
<tr>
<td>4</td>
<td>od_spec_mode</td>
<td>R</td>
<td>0</td>
<td><strong>Overdrive Spec Mode</strong><br> Returns the version of the overdrive spec.
</td>
</tr>
<tr>
<td>3</td>
<td>ow_input</td>
<td>R</td>
<td>-</td>
<td><strong>OWM_IN State</strong><br> Returns the current logic level on the OWM_IO pin.
<div style="margin-left: 20px;">
0: OWM_IO pin is low. <br>
1: OWM_IO pin is high.</div>
</td>
</tr>
<tr>
<td>2</td>
<td>bit_bang_oe</td>
<td>R/W</td>
<td>0</td>
<td><strong>OWM Bit-Bang Output</strong> <br> When bit-bang mode is enabled (<a href="#owm-configuration-register">OWM_CFG</a>.<em>bit_bang_en</em> = 1), this bit sets the state of the OWM_IO pin. Setting this bit to 1 drives the OWM_IO pin low. Setting this bit to 0 releases the line, allowing the OWM_IO pin to be pulled high by the pullup resistor or held low by a slave device.
<div style="margin-left: 20px;">
0: OWM_IO pin floating.<br>
1: Drive OWM_IO pin to low state.</div>
</td>
</tr>
<tr>
<td>1</td>
<td>sra_mode</td>
<td>R/W</td>
<td>0</td>
<td><strong>Search ROM Accelerator Enable</strong> <br> Enable Search ROM Accelerator mode. This mode is used to identify slaves and their addresses that are attached to the 1‑Wire bus.
<div style="margin-left: 20px;">
0: Search ROM accelerator mode disabled.<br>
1: Search ROM accelerator mode enabled.</div>
</td>
</tr>
<tr>
<td>0</td>
<td>start_ow_reset</td>
<td>R/W</td>
<td>0</td>
<td><strong>Start 1‑Wire Reset Pulse</strong><br> Write 1 to start a 1‑Wire reset sequence. Automatically cleared by the OWM hardware when the reset sequence is complete.
<div style="margin-left: 20px;">
0: 1‑Wire reset sequence complete or inactive.<br>
1: Start a 1‑Wire reset sequence.</div>
</td>
</tr>
</tbody>
</table>

*Table 17-8: OWM Data Buffer Register*
<a name="owm-data-buffer-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">OWM Data</th>
        <th colspan="1">OWM_DATA</th>
        <th>[0x000C]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
<tr>
<td>31:8</td>
<td>-</td>
<td>RO</td>
<td>0</td>
<td><strong>Reserved</strong></td>
</tr>
<tr>
<td>7:0</td>
<td>tx_rx</td>
<td>R/W</td>
<td>0</td>
<td><strong>OWM Data Field</strong><br> Writing to this field sets the transmit data and initiates a 1‑Wire data transmit cycle. Reading from this field returns the data received by the master during the last 1‑Wire data transmit cycle.</td>
</tr>
</tbody>
</table>

*Table 17-9: OWM Interrupt Flag Register*
<a name="owm-interrupt-flag-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">OWM Interrupt Flag</th>
        <th colspan="1">OWM_INTFL</th>
        <th>[0x0010]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
<tr>
<td>31:5</td>
<td>-</td>
<td>RO</td>
<td>0</td>
<td><strong>Reserved</strong></td>
</tr>
<tr>
<td>4</td>
<td>line_low</td>
<td>R/W1C</td>
<td>0</td>
<td><strong>Line Low Flag</strong><br> If this flag is set, the OWM_IO pin was in a low state. Write 1 to clear this flag.
</td>
</tr>
<tr>
<td>3</td>
<td>line_short</td>
<td>R/W1C</td>
<td>0</td>
<td><strong>Line Short Flag</strong><br> The OWM hardware detected a short on the OWM_IO pin. Write 1 to clear this flag.
</td>
</tr>
<tr>
<td>2</td>
<td>rx_data_ready</td>
<td>R/W1C</td>
<td>0</td>
<td><strong>RX Data Ready</strong><br> Data received from the 1‑Wire bus and is available in the
<a href="#owm-data-buffer-register">OWM_DATA</a>.<em>tx_rx</em> field. Write 1 to clear this flag.
<div style="margin-left: 20px;">
0: Receive data not available.<br>
1: Data received and is available in the <a href="#owm-data-buffer-register">OWM_DATA</a>.<em>tx_rx</em> field.</div>
</td>
</tr>
<tr>
<td>1</td>
<td>tx_data_empty</td>
<td>R/W1C</td>
<td>0</td>
<td><strong>TX Empty</strong><br> The OWM hardware automatically sets this interrupt flag when the data
transmit is complete. Write 1 to clear this flag.
<div style="margin-left: 20px;">
0: Either no data was sent, or the data in the
<a href="#owm-data-buffer-register">OWM_DATA</a>.<em>tx_rx</em> field has not completed transmission.<br>
1: Data in the <a href="#owm-data-buffer-register">OWM_DATA</a>.<em>tx_rx</em> field was
transmitted.</div>
</td>
</tr>
<tr>
<td>0</td>
<td>ow_reset_done</td>
<td>R/W1C</td>
<td>0</td>
<td><strong>Reset Complete</strong><br> This flag is set when a 1‑Wire reset sequence completes. To start a 1‑Wire reset sequence, see <a href="#owm-control-status-register">OWM_CTRL_STAT</a>.<em>start_ow_reset</em>. Write 1 to clear this flag.
<div style="margin-left: 20px;">
0: 1‑Wire reset sequence not complete or bus idle.<br>
1: 1‑Wire reset sequence complete.</div>
</td>
</tr>
</tbody>
</table>

*Table 17-10: OWM Interrupt Enable Register*
<a name="owm-interrupt-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="3">OWM Interrupt Enable</th>
        <th colspan="1">OWM_INTEN</th>
        <th>[0x0014]</th>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
<tr>
<td>31:5</td>
<td>-</td>
<td>RO</td>
<td>0</td>
<td><strong>Reserved</strong></td>
</tr>
<tr>
<td>4</td>
<td>line_low</td>
<td>R/W</td>
<td>0</td>
<td><strong>Line Low Interrupt Enable</strong><br> Set this field to 1 to enable the I/O pin low detected interrupt.
<div style="margin-left: 20px;">
0: Interrupt disabled.<br>
1: Interrupt enabled.<br>
</td>
</tr>
<tr>
<td>3</td>
<td>line_short</td>
<td>R/W</td>
<td>0</td>
<td><strong>Line Short Interrupt Enable</strong><br> Set this field to 1 to enable the I/O pin short detected interrupt.
<div style="margin-left: 20px;">
0: Interrupt disabled. <br>
1: Interrupt enabled.</div>
</td>
</tr>
<tr>
<td>2</td>
<td>rx_data_ready</td>
<td>R/W</td>
<td>0</td>
<td><strong>Receive Data Ready Interrupt Enable</strong> <br> Set this field to 1 to enable the receive data ready interrupt.
<div style="margin-left: 20px;">
0: Interrupt disabled.<br>
1: Interrupt enabled.</div>
</td>
</tr>
<tr>
<td>1</td>
<td>tx_data_empty</td>
<td>R/W</td>
<td>0</td>
<td><strong>Transmit Data Empty Interrupt Enable</strong><br> Set this field to 1 to enable the transmit data empty interrupt.
<div style="margin-left: 20px;">
0: Interrupt disabled.<br>
1: Interrupt enabled.</div>
</td>
</tr>
<tr>
<td>0</td>
<td>ow_reset_done</td>
<td>R/W</td>
<td>0</td>
<td><strong>1‑Wire Reset Sequence Complete Interrupt Enable</strong><br>Set this field to 1 to enable the 1‑Wire reset sequence completed interrupt.
<div style="margin-left: 20px;">
0: Interrupt disabled.<br>
1: Interrupt enabled.</div>
</td>
</tr>
</tbody>
</table>