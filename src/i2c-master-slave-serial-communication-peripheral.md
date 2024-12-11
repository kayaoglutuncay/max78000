The I²C peripherals can be configured as either an I2C master or an I2C slave at standard data rates.

For detailed information on I²C bus operation, refer to Analog Devices Note 4024 "SPI/I²C Bus Lines Control Multiple Peripherals" at [https://www.analog.com/en/resources/app-notes/spii2c-bus-lines-control-multiple-peripherals.html](https://www.analog.com/en/resources/app-notes/spii2c-bus-lines-control-multiple-peripherals.html).

## I²C Master/Slave Features
Each I²C master/slave is compliant with the I²C Bus Specification and includes the following features:

- Communicates through a serial data bus (SDA) and a serial clock line (SCL)
- Operates as either a master or slave device as a transmitter or receiver
- Supports I²C Standard Mode, Fast Mode, Fast Mode Plus, and High Speed (Hs) mode
- Transfers data at rates up to:

    - 100kbps in Standard Mode
    - 400kbps in Fast Mode
    - 1Mbps in Fast Mode Plus
    - 3.4Mbps in Hs Mode

- Supports multi-master systems, including support for arbitration and clock synchronization for Standard, Fast, and Fast Plus modes
- Supports 7- and 10-bit addressing
- Supports RESTART condition
- Supports clock stretching
- Provides transfer status interrupts and flags
- Provides DMA data transfer support
- Supports I²C timing parameters fully controllable through software
- Provides glitch filter and Schmitt trigger hysteresis on SDA and SCL
- Provides control, status, and interrupt events for maximum flexibility
- Provides independent 8-byte receive FIFO and 8-byte transmit FIFO
- Provides transmit FIFO preloading
- Provides programmable interrupt threshold levels for the transmit and receive FIFO

## Instances
The three instances of the peripheral are shown in [Table 14-1](#table14-1-max78000-i2c-peripheral-pins), which lists the locations of the SDA and SCL signals for each of the I2C peripherals per package.

*Table 14-1: MAX78000 I2C Peripheral Pins*
<a name="table14-1-max78000-i2c-peripheral-pins"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>I²C Instance</th>
        <th>Alternate Function</th>
        <th>Alternate Function #</th>
        <th>Package 81-CTBGA</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td rowspan="2">I2C0</td>
        <td>I2C0_SCL</td>
        <td>AF1</td>
        <td>P0.10</td>
    </tr>
    <tr>
        <td>I2C0_SDA</td>
        <td>AF1</td>
        <td>P0.11</td>
    </tr>
    <tr>
        <td rowspan="2">I2C1</td>
        <td>I2C1_SCL</td>
        <td>AF1</td>
        <td>P0.16</td>
    </tr>
    <tr>
        <td>I2C1_SDA</td>
        <td>AF1</td>
        <td>P0.17</td>
    </tr>
    <tr>
        <td rowspan="2">I2C2</td>
        <td>I2C2_SCL</td>
        <td>AF1</td>
        <td>P0.30</td>
    </tr>
    <tr>
        <td>I2C2_SDA</td>
        <td>AF1</td>
        <td>P0.31</td>
    </tr>
</tbody>
</table>

## I²C Overview
### I²C Bus Terminology
[Table 14-2](#table14-2-i2c-bus-terminology) contains terms and definitions used in this chapter for the I²C bus terminology.

*Table 14-2: I²C Bus Terminology*
<a name="table14-2-i2c-bus-terminology"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Term</th>
        <th>Definition</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>Transmitter</td>
        <td>The device that sends data to the bus.</td>
    </tr>
    <tr>
        <td>Receiver</td>
        <td>The device that receives data from the bus.</td>
    </tr>
    <tr>
        <td>Master</td>
        <td>The device that initiates a transfer, generates clock signals, and terminates a transfer.</td>
    </tr>
    <tr>
        <td>Slave</td>
        <td>The device that a master addresses.</td>
    </tr>
    <tr>
        <td>Multi-master</td>
        <td>More than one master can attempt to control the bus at the same time without corrupting the message.</td>
    </tr>
    <tr>
        <td>Arbitration</td>
        <td>Procedure to ensure that, if more than one master simultaneously tries to control the bus, only one can do so, and the resulting message is not corrupted.</td>
    </tr>
    <tr>
        <td>Synchronization</td>
        <td>Procedure to synchronize the clock signals of two or more devices.</td>
    </tr>
    <tr>
    <td>Clock Stretching</td>
    <td>When a slave device holds SCL low to pause a transfer until it is ready. This is an optional feature according to the I²C specification; thus, a master does not have to support slave clock stretching if none of the slaves in the system are capable of clock stretching.</td>
    </tr>
</tbody>
</table>

### I²C Transfer Protocol Operation
The I²C protocol operates over a two-wire bus: a clock circuit (SCL) and a data circuit (SDA). I²C is a half-duplex protocol: only one device is allowed to transmit on the bus at a time.

Each transfer is initiated when the bus master sends a START or repeated START condition. It is followed by the I²C slave address of the targeted slave device plus a read/write bit. The master can transmit data to the slave (a 'write' operation) or receive data from the slave (a 'read' operation). Information is sent most significant bit (MSB) first. 

Following the slave address, the master indicates a read or write operation and then exchanges data with the addressed slave. An acknowledge bit is sent by the receiving device after each byte is transferred. 

When all necessary data bytes have been transferred, a STOP or RESTART condition is sent by the bus master to indicate the end of the transaction. After the STOP condition has been sent, the bus is idle and ready for the next transaction. After a RESTART condition is sent, the same master begins a new transmission. The number of bytes that can be transmitted per transfer is unrestricted.

### START and STOP Conditions
A START condition occurs when a bus master pulls SDA from high to low while SCL is high, and a STOP condition occurs when a bus master allows SDA to be pulled from low to high while SCL is high. Because these are unique conditions that cannot occur during normal data transfer, they are used to denote the beginning and end of the data transfer.

### Master Operation
I²C transmit and receive data transfer operations occur through the [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register) register. Writes to the register load the transmit FIFO and reads from the register return data from the receive FIFO. If a slave sends a NACK in response to a write operation, the I²C master generates an interrupt. The I²C controller can be configured to issue a STOP condition to free the bus.

The receive FIFO contains the received data. If the receive FIFO is full or the transmit FIFO is empty, the I²C master stops the clock to allow time to read bytes from the receive FIFO or load bytes into the transmit FIFO.

### Acknowledge and Not Acknowledge
An acknowledge bit (ACK) is generated by the receiver, whether I²C master or slave, after every byte received by pulling SDA low. The ACK bit is how the receiver tells the transmitter that the byte was successfully received and another byte might be sent.

A Not Acknowledge (NACK) occurs if the receiver does not generate an ACK when the transmitter releases SDA. A NACK is generated by allowing SDA to float high during the acknowledge time slot. The I²C master can then either generate a STOP condition to abort the transfer or generate a repeated START condition (send a START condition without an intervening STOP condition) to start a new transfer.

A receiver can generate a NACK after a byte transfer if any of the following conditions occur:

- No receiver is present on the bus with the transmitted address. In that case, no device responds with an acknowledge signal.
- The receiver cannot receive or transmit because it is busy and is not ready to start communication with the master.
- During the transfer, the receiver receives data or commands it does not understand.
- During the transfer, the receiver is unable to receive any more data.
- If an I²C master has requested data from a slave, it signals the slave to stop transmitting by sending a NACK following the last byte it requires.

### Bit Transfer Process
Both SDA and SCL circuits are open-drain, bidirectional circuits. Each requires an external pullup resistor that ensures each circuit is high when idle. The I²C specification states that during data transfer, the SDA line can change state only when SCL is low and that SDA is stable and can be read when SCL is high, as shown in [Figure 14-1](#figure14-1).

*Figure 14-1: I²C Write Data Transfer*
<a name="figure14-1"></a>

![Figure 14-1](assets/images/figure14-1.svg)

An example of an I²C data transfer is as follows:

1. A bus master indicates a data transfer to a slave with a START condition.
2. The master then transmits one byte with a 7-bit slave address and a single read-write bit: a zero for a write or a one for a read.
3. During the next SCL clock following the read-write bit, the master releases SDA. During this clock period, the addressed slave responds with an ACK by pulling SDA low.
4. The master senses the ACK condition and begins transferring data. If reading from the slave, it floats SDA and allows the slave to drive SDA to send data. After each byte, the master drives SDA low to acknowledge the byte. If writing to the slave, the master drives data on the SDA circuit for each of the eight bits of the byte and then floats SDA during the ninth bit to allow the slave to reply with the ACK indication.
5. After the last byte is transferred, the master indicates the transfer is complete by generating a STOP condition. A STOP condition is generated when the master pulls SDA from a low to high while SCL is high.

## Configuration and Usage
### SCL and SDA Bus Drivers
SCL and SDA are open-drain signals. In this device, once the I²C peripheral is enabled and the proper GPIO alternate function is selected, the corresponding pad circuits are automatically configured as open-drain outputs. However, SCL can also be optionally configured as a push-pull driver to conserve power and avoid the need for any pullup resistor. This should only be used in systems where no I²C slave device can hold SCL low, such as for clock stretching. Push-pull operation is enabled by setting [I2Cn_CTRL](#i2c-control-register).*sclppm* to 1. SDA, on the other hand, always operates in open-drain mode.

### SCL Clock Configurations
The SCL frequency depends on the values of the I²C's peripheral clock and the values of the external pullup resistor and trace capacitance on the SCL clock line.

*Note: An external RC load on the SCL line affects the target SCL frequency calculation.*

### SCL Clock Generation for Standard, Fast, and Fast-Plus Modes
The master generates the I²C clock on the SCL line. When operating as a master, the software must configure the [I2Cn_CLKHI](#i2c-clock-high-time-register) and [I2Cn_CLKLO](#i2c-clock-low-time-register) registers for the desired I²C operating frequency.

The SCL high time is configured in the I²C Clock High Time register field [I2Cn_CLKHI](#i2c-clock-high-time-register).*hi* using [Equation 14-2](#equation14-2). The SCL low time is configured in the I²C Clock Low Time register field [I2Cn_CLKLO](#i2c-clock-low-time-register).*lo* using [Equation 14-3](#equation14-3). Each of these fields is 8-bits. The I²C frequency value is shown in [Equation 14-1](#equation14-1).

Equation 14-1: I²C Clock Frequency
<a name="equation14-1"></a>

$$
f_{I2C\text{_}CLK} = \frac{1}{t_{I2C\text{_}CLK}}\ is\ either\ f_{PCLK}\ or\ f_{IBRO}
$$

Equation 14-2: I²C Clock High Time Calculation
<a name="equation14-2"></a>

$$
t_{SCL\text{_}HI} = t_{I2C\text{_}CLK} \times (I2Cn_CLKHI.hi + 1)
$$

Equation 14-3: I²C Clock Low Time Calculation
<a name="equation14-3"></a>

$$
t_{SCL\text{_}LO} = t_{I2C\text{_}CLK} \times (I2Cn_CLKLO.lo + 1)
$$

[Figure 14-2](#figure14-2) shows the association between the SCL clock low and high times for Standard, Fast, and Fast Plus I²C frequencies.

*Figure 14-2: I²C SCL Timing for Standard, Fast and Fast-Plus Modes*
<a name="figure14-2"></a>

![Figure 14-2](assets/images/figure14-2.svg)

External masters or external slaves may be driving SCL simultaneously, affecting the SCL duty cycle during synchronization. By monitoring SCL, the controller can determine whether an external master or slave is holding SCL low. In either case, the controller waits until SCL is high before starting to count the number of SCL high cycles. Similarly, suppose an external master pulls SCL low before the controller has finished counting SCL high cycles. In that case, the controller starts counting SCL low cycles and releases SCL once the time period, [I2Cn_CLKLO](#i2c-clock-low-time-register).*lo*, has expired.

Because the controller does not start counting the high and low time until the input buffer detects the new value, the actual clock behavior is based on many factors, including bus loading, other devices on the bus holding SCL low, and the filter delay time of this device.

### SCL Clock Generation for Hs-Mode
Values to be programmed into the [I2Cn_HSCLK](#i2c-hs-mode-clock-control-register).*hsclk_lo* register and [I2Cn_HSCLK](#i2c-hs-mode-clock-control-register).*hsclk_hi* register must be determined to operate the I²C interface in Hs-Mode at its maximum speed (~3.4MHz). Since the Hs-Mode operation is entered by first using one of the lower speed modes for pre-amble, a relevant lower speed mode must also be configured. See SCL Clock Generation for Standard, Fast and Fast-Plus Modes for information regarding the configuration of lower speed modes.

#### Hs-Mode Timing
With I²C bus capacitances less than 100pf, the following specifications are extracted from the I²C-bus Specification and User Manual Rev. 6 April 2014 [https://www.nxp.com/docs/en/user-guide/UM10204.pdf](https://www.nxp.com/docs/en/user-guide/UM10204.pdf).

$t_{LOW\text{_MIN}}$ = 160ns, the minimum low time for the I²C bus clock.

$t_{HIGH\text{_MIN}}$ = 60ns, the minimum high time for the I²C bus clock.

$t_{rCL\text{_MAX}}$ = 40ns, the maximum rise time of the I2C bus clock.

$t_{fCL\text{_MAX}}$ = 40ns, the maximum fall time of the I²C bus clock.

#### Hs-Mode Clock Configuration
The maximum Hs-Mode bus clock frequency can now be determined. Calculate the required settings for Hs-Mode using the following equations.

Equation 14-4: I²C Target SCL Frequency
<a name="equation14-4"></a>

Desired Target Maximum I2C Frequency: $f_{\text{SCL}} = \frac{1}{t_{\text{SCL}}}$ 

In Hs-Mode, the analog glitch filter (AF_MIN) within the device adds a minimum delay of $t_{\text{AF_MIN}}$ = 10ns.

Equation 14-5: Determining the [I2Cn_HSCLK](#i2c-hs-mode-clock-control-register).*hsclk_lo* Register Value
<a name="equation14-5"></a>

$$I2Cn\text{_}HSCLK.hsclk\_ lo =
MAX\left\{ \left\lfloor \left( \ \frac{t_{LOW\_ MIN} + \ t_{FCL\_ MAX} +
\ t_{I2C\_ CLK} - t_{AF\_ MIN}}{t_{I2C\_ CLK}} \right) \right\rfloor -
1,\ \ \frac{t_{SCL}}{t_{I2C\_ CLK}} - 1 \right\}$$

Equation 14-6: Determining the I2Cn_HSCLK.hsclk_hi Register Value
<a name="equation14-6"></a>

$$I2Cn\text{_}HSCLK.hsclk\text{\_}hi =
\left\lfloor \left( \ \frac{t_{HIGH\text{\_}MIN} + \ t_{rCL\text{\_}MAX}
+ \ t_{I2C\text{\_}CLK} - t_{AF\text{\_}MIN}}{t_{I2C\text{\_}CLK}}
\right) \right\rfloor - 1$$

Equation 14-7: The Calculated Frequency of the I²C Bus Clock Using the Results of [Equation 14-5](#equation14-5) and [Equation 14-6](#equation14-6)
<a name="equation14-7"></a>

Calculated Frequency = $((I2Cn\text{_HS}\text{_CLK}.hsclk\text{_hi}+1)+(I2Cn\text{_HS}\text{_CLK}.hsclk\text{_lo}+1) \times t_{I2C\text{_CLK}})$

[Table 14-3](#table14-3-calculated-i2c-bus-clock-frequencies) shows the I²C bus clock calculated frequencies given different ${f_{SYS\_ CLK}}$ frequencies.

*Table 14-3: Calculated I²C Bus Clock Frequencies*
<a name="table14-3-calculated-i2c-bus-clock-frequencies"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th><em>f<sub>SYS_CLK</sub></em> (MHz)</th>
        <th><a href="#i2c-hs-mode-clock-control-register">I2Cn_HSCLK</a>.<em>hsclk_hi</em></th>
        <th><a href="#i2c-hs-mode-clock-control-register">I2Cn_HSCLK</a>.<em>hsclk_lo</em></th>
        <th>Calculated Frequency (MHz)</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>100</td>
        <td>4</td>
        <td>9</td>
        <td>3.3</td>
    </tr>
    <tr>
        <td>50</td>
        <td>2</td>
        <td>4</td>
        <td>3.125</td>
    </tr>
    <tr>
        <td>25</td>
        <td>1</td>
        <td>2</td>
        <td>2.5</td>
    </tr>
</tbody>
</table>


### Master Mode Addressing
After a START condition, the I²C slave address byte is transmitted by the hardware. The I²C slave address is composed of a slave address followed by a read/write bit.

*Table 14-4: I²C Slave Address Format*
<a name="table14-4-i2c-slave-address-format"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th colspan="2">Slave Address Bits</th>
        <th>R/W Bit</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>0000</td>
        <td>000</td>
        <td>0</td>
        <td>General Call Address</td>
    </tr>
    <tr>
        <td>0000</td>
        <td>000</td>
        <td>1</td>
        <td>START Condition</td>
    </tr>
    <tr>
        <td>0000</td>
        <td>001</td>
        <td><em>x</em></td>
        <td>CBUS Address</td>
    </tr>
    <tr>
        <td>0000</td>
        <td>010</td>
        <td><em>x</em></td>
        <td>Reserved for different bus format</td>
    </tr>
    <tr>
        <td>0000</td>
        <td>011</td>
        <td><em>x</em></td>
        <td>Reserved for future purposes</td>
    </tr>
    <tr>
        <td>0000</td>
        <td>1<em>xx</em></td>
        <td><em>x</em></td>
        <td>HS-mode master code</td>
    </tr>
    <tr>
        <td>1111</td>
        <td>1<em>xx</em></td>
        <td><em>x</em></td>
        <td>Reserved for future purposes</td>
    </tr>
    <tr>
        <td>1111</td>
        <td>0<em>xx</em></td>
        <td><em>x</em></td>
        <td>10-bit slave addressing</td>
    </tr>
</tbody>
</table>


In 7-bit addressing mode, the master sends one address byte. Address a 7-bit address slave as follows. First, clear the [I2Cn_MSTCTRL](#i2c-master-control-register).*ex_addr_en* field to 0, then write the address to the transmit FIFO formatted as follows:

Master writing to slave: 7-bit address : [A6 A5 A4 A3 A2 A1 A0 0]

Master reading from slave: 7-bit address : [A6 A5 A4 A3 A2 A1 A0 1]

In 10-bit addressing mode ([I2Cn_MSTCTRL](#i2c-master-control-register).*ex_addr_en* = 1), the first byte the master sends is the 10-bit slave address byte, which includes the first two bits of the 10-bit address, followed by a 0 for the R/W bit. That is followed by a second byte representing the remainder of the 10-bit address. If the operation is a write, this is followed by data bytes to be written to the slave. If the operation is a read, it is followed by a repeated START. The software then writes the 10-bit address again with a 1 for the R/W bit. This I²C then starts receiving data from the slave device.

### Master Mode Operation
The peripheral operates in master mode when master mode Enable [I2Cn_CTRL](#i2c-control-register).*mst_mode* = 1. To initiate a transfer, the master generates a START condition by setting [I2Cn_MSTCTRL](#i2c-control-register).*start* = 1. If the bus is busy, it does not generate a START condition until the bus is available.

A master can communicate with multiple slave devices without relinquishing the bus. Instead of generating a STOP condition after communicating with the first slave, the master generates a Repeated START condition, or RESTART, by setting [I2Cn_MSTCTRL](#i2c-master-control-register).*restart* = 1. If a transaction is in progress, the peripheral completes the transaction before generating a RESTART. The peripheral then transmits the slave address stored in the transmit FIFO. The [I2Cn_MSTCTRL](#i2c-master-control-register).*restart* bit is automatically cleared to 0 as soon as the master begins a RESTART condition.

[I2Cn_MSTCTRL](#i2c-master-control-register).*start* is automatically cleared to 0 after the master has completed a transaction and sent a STOP condition.

The master can also generate a STOP condition by setting [I2Cn_MSTCTRL](#i2c-master-control-register).*stop* = 1.

If both START and RESTART conditions are enabled simultaneously, a START condition is generated first. Then, at the end of the first transaction, a RESTART condition is generated.

If both RESTART and STOP conditions are enabled simultaneously, a STOP condition is not generated. Instead, a RESTART condition is generated. After the RESTART condition is generated, both bits are cleared.

If START, RESTART, and STOP are all enabled simultaneously, a START condition is first generated. At the end of the first transaction, a RESTART condition is generated. The [I2Cn_MSTCTRL](#i2c-master-control-register).*stop* bit is cleared and ignored.

A slave cannot generate START, RESTART, or STOP conditions. Therefore, when master mode is disabled, the [I2Cn_MSTCTRL](#i2c-master-control-register).*start*, [I2Cn_MSTCTRL](#i2c-master-control-register).*restart*, and [I2Cn_MSTCTRL](#i2c-master-control-register).*stop* bits are all cleared to 0.

For master mode operation, the following registers should only be configured when either:

1. The I²C peripheral is disabled,
or
2. The I²C bus is guaranteed to be idle/free.

If this peripheral is the only master on the bus, then changing the registers outside of a transaction ([I2Cn_MSTCTRL](#i2c-master-control-register).*start* = 0) satisfies this requirement:

- [I2Cn_CTRL](#i2c-control-register).*mst_mode*
- [I2Cn_CTRL](#i2c-control-register).*irxm_en*
- [I2Cn_CTRL](#i2c-control-register).*one_mst_mode*
- [I2Cn_CTRL](#i2c-control-register).*hs_en*
- [I2Cn_RXCTRL1](#i2c-receive-control-1-register).*cnt*
- [I2Cn_MSTCTRL](#i2c-master-control-register).*ex_addr_en*
- [I2Cn_MSTCTRL](#i2c-master-control-register).*mcode*
- [I2Cn_CLKLO](#i2c-clock-low-time-register).*lo*
- [I2Cn_CLKHI](#i2c-clock-high-time-register).*hi*
- [I2Cn_HSCLK](#i2c-hs-mode-clock-control-register).*hsclk_lo*
- [I2Cn_HSCLK](#i2c-hs-mode-clock-control-register).*hsclk_hi*

#### I²C Master Mode Receiver Operation
In contrast to the above set of registers, these registers below can be safely (re)programmed at any time:

- All interrupt flags and interrupt enable bits
- [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val*
- [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl*
- [I2Cn_TIMEOUT](#i2c-timeout-register).*scl_to_val*
- [I2Cn_DMA](#i2c-dma-enable-register).*rx_en*
- [I2Cn_DMA](#i2c-dma-enable-register).*tx_en*
- [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register).*data*
- [I2Cn_MSTCTRL](#i2c-master-control-register).*start*
- [I2Cn_MSTCTRL](#i2c-master-control-register).*restart*
- [I2Cn_MSTCTRL](#i2c-master-control-register).*stop*

#### I²C Master Mode Transmitter Operation
When in master mode, initiating a master receiver operation begins with the following sequence:

1. Write the number of data bytes to receive to the I²C receive count field ([I2Cn_RXCTRL1](#i2c-transmit-control-1-register).*cnt*).
2. Write the I²C slave address byte to the [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register) register with the R/W bit set to 1
3. Send a START condition by setting [I2Cn_MSTCTRL](#i2c-master-control-register).*start* = 1
4. The slave address is transmitted by the controller from the [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register) register.
5. The I²C controller receives an ACK from the slave, and the controller sets the address ACK interrupt flag ([I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_ack* = 1).
6. The I²C controller receives data from the slave and automatically ACKs each byte. The software must retrieve this data by reading the [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register) register.
7. Once [I2Cn_RXCTRL1](#i2c-receive-control-1-register).*cnt* data bytes have been received, the I²C controller sends a NACK to the slave and sets the Transfer Done Interrupt Status Flag ([I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* = 1).
8. If [I2Cn_MSTCTRL](#i2c-master-control-register).*restart* or [I2Cn_M](#i2c-master-control-register).*stop* is set, then the I²C controller sends a repeated START or STOP, respectively.

#### I²C Multi-Master Operation
The I²C protocol supports multiple masters on the same bus. When the bus is free, two (or more) masters might try to initiate communication simultaneously. This is a valid bus condition. If this occurs, and the two masters want to transmit different data or address different slaves, only one master can remain in master mode and complete its transaction. The other master must stop transmission and wait until the bus is idle. This process by which the winning master is determined is called bus arbitration.

For each address or data bit, the master compares the data being transmitted on SDA to the value observed on SDA to determine which master wins the arbitration. If a master attempts to transmit a 1 on SDA (that is, the master lets SDA float) but senses a 0 instead, then that master loses arbitration, and the other master that sent a zero continues with the transaction. The losing master cedes the bus by switching off its SDA and SCL drivers.

*Note: This arbitration scheme works with any number of bus masters: if more than two masters begin transmitting simultaneously, the arbitration continues as each master cedes the bus until only one master remains transmitting. Data is not corrupted because as soon as each master realizes it has lost the arbitration, it stops transmitting on SDA, leaving the following data bits sent on SDA intact.*

*If the I²C master peripheral detects it has lost the arbitration, it stops generating SCL; sets [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*areri*; sets [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout*, flushing any remaining data in the transmit FIFO; and clears [I2Cn_MSTCTRL](#i2c-master-control-register).*start*, [I2Cn_MSTCTRL](#i2c-master-control-register).*restart*, and [I2Cn_MSTCTRL](#i2c-master-control-register).*stop* to 0. So long as the peripheral is not addressed by the winning master, the I²C peripheral stays in master mode ([I2Cn_CTRL](#i2c-control-register).*mst_mode* = 1). If, at any time, another master addresses this peripheral using the address programmed in I2Cn_SLAVE.addr, then the I²C peripheral clears [I2Cn_CTRL](#i2c-control-register).*mst_mode* to 0 and begins responding as a slave. This can even occur during the same address transmission during which the peripheral lost arbitration.*

*Note: Arbitration loss is considered an error condition, and like the other error conditions, it sets [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout* to 1. Therefore, after an arbitration loss, the software needs to clear [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout* and reload the transmit FIFO.*

Also, in a multi-master environment, the software does not need to wait for the bus to become free before attempting to start a transaction (writing 1 to [I2Cn_MSTCTRL](#i2c-master-control-register).*start*). If the bus is free when [I2Cn_MSTCTRL](#i2c-master-control-register).*start* is set to 1, the transaction begins immediately. If, instead, the bus is busy, then the peripheral will:

1. Wait for the other master to complete the transaction(s) by sending a STOP,
2. Count out the bus free time using $t_{BUF} = t_{SCL\text{_LO}}$ (see [Equation 14-3](#equation14-3)), and then
3. Send a START condition and begin transmitting the slave address byte(s) in the transmit FIFO, followed by the rest of the transfer.

The I²C master peripheral is compliant with all bus arbitration and clock synchronization requirements of the I²C specification; this operation is automatic, and no additional programming is required.

### Slave Mode Operation
When in slave mode, the I²C peripheral operates as a slave device on the I²C bus and responds to an external master's requests to transmit or receive data. To configure the I²C peripheral as a slave, write the [I2Cn_CTRL](#i2c-control-register).*mst_mode* bit to zero. The I²C clock, SCL, is driven by the external master on the bus, and [I2Cn_STATUS](#i2c-status-register).*mst_busy* remains a zero. The desired slave address must be set by writing to the [I2Cn_SLAVE](#i2c-slave-address-register).*addr* register.

For slave mode operation, the following register fields should be configured with the I²C peripheral disabled:

- [I2Cn_CTRL](#i2c-control-register).*mst_mode* = 0 for slave operation.
- I2C slave address

    - [I2Cn_SLAVE](#i2c-slave-address-register).*addr must* be set to the desired address for the device on the bus
    - [I2Cn_SLAVE](#i2c-slave-address-register).*ext_addr_en* should be set to 1 for 10-bit addressing or 0 for 7-bit addressing

- [I2Cn_CTRL](#i2c-control-register).*gc_addr_en*
- [I2Cn_CTRL](#i2c-control-register).*irxm_en*

    - The recommended value for this field is 0. Note that a setting of 1 is incompatible with slave mode operation with clock stretching disabled ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 1).

- [I2Cn_CTRL](#i2c-control-register).*clkstr_dis*
- [I2Cn_CTRL](#i2c-control-register).*hs_en*
- [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*dnr*

    - SMBus/PMBus applications should set this to 0, while other applications should set this to 1.

- [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*nack_flush_dis*
- [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*rd_addr_flush_dis*
- [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*wr_addr_flush_dis*
- [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*gc_addr_flush_dis*
- [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*preload_mode*

    - The recommended value is 0 for applications that can tolerate slave clock stretching ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 0).
    - The recommended value is 1 for applications that do not allow slave clock stretching ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 1).

- [I2Cn_CLKHI](#i2c-clock-high-time-register).*hi*

    - Applies to slave mode when clock stretching is enabled ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 0)

        - This is used to satisfy $t_{SU\text{_DAT}}$ after clock stretching; program it so that the value defined by [Equation 14-2](#equation14-2) is >= $t_{SU\text{_DAT(min)}}$

- [I2Cn_HSCLK](#i2c-hs-mode-clock-control-register).*hsclk_hi*

    - Applies to slave mode in Hs Mode when clock stretching is enabled ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 0)

        - This is used to satisfy $t_{SU\text{_DAT}}$ after clock stretching during Hs-Mode operation; program it so that the value defined by [Equation 14-6](#equation14-6) is >= $t_{SU\text{_DAT(min)}}$

In contrast to the above register fields, the following register fields can be safely (re)programmed at any time:

- All interrupt flags and interrupt enables
- [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val* and [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl*

    - Transmit and receive FIFO threshold levels

- [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*tx_rdy*

    - Transmit ready (Can only be cleared by the hardware)

- [I2Cn_TIMEOUT](#i2c-timeout-register).*scl_to_val*

    - Time out control

- [I2Cn_DMA](#i2c-dma-enable-register).*rx_en* and [I2Cn_DMA](#i2c-dma-enable-register).*tx_en*

    - Transmit and receive DMA Enables

- [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register).*data*

    - FIFO access register

#### Slave Transmitter
The device operates as a slave transmitter when the received address matches the device slave address with the R/W bit set to 1. The master is then reading from the device slave. There two main modes of slave transmitter operation: just-in-time mode and preload mode.

##### Just-In-Time Mode
In just-in-time mode, the software waits to write the transmit data to the transmit FIFO until after the master addresses it for a READ transaction, just in time, to send the data to the master. This allows the software to defer the determination of what data should be sent until the time of the address match. For example, the transmit data could be based on an immediately preceding I2C WRITE transaction that requests a certain block of data to be sent. The data could represent the latest, most up-to-date value of a sensor reading. Clock stretching must be enabled ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 0) for just-in-time mode operation.

Program flow for transmit operation in just-in-time mode is as follows:

1. With [I2Cn_CTRL](#i2c-control-register).*en* = 0, initialize all relevant registers, including:

    a. Set the [I2Cn_SLAVE](#i2c-slave-address-register).*addr* field with the desired I2C slave address.

    b. Set the [I2Cn_SLAVE](#i2c-slave-address-register).*ext_addr_en* for either 7-bit or 10-bit addressing.
    
    c. Just-in-time mode specific settings:

        i) I2Cn_CTRL.clkstr_dis = 0
        ii) I2Cn_TXCTRL0[5:2] = 0x8
        iii) I2Cn_TXCTRL0.preload_mode = 0.

    d. Program [I2Cn_CLKHI](#i2c-clock-high-time-register).*hi* and [I2Cn_HSCLK](#i2c-clock-high-time-register).*hsclk_hi* with appropriate values satisfying 
    $t_{SU\text{_DAT}}$ (and HS $t_{SU\text{_DAT}}$).

2. The software sets [I2Cn_CTRL](#i2c-control-register).*en* = 1.

    a. The controller is now listening for its address. The peripheral responds to its address with an ACK for either a transmit (R/W = 1) or receive (R/W = 0) operation.

    b. When the address match occurs, the hardware sets [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* and [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout*.

3. The software waits for [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* = 1, either through polling the interrupt flag or setting [I2Cn_INTEN0](#i2c-interrupt-enable-0-register).*addr_match* to interrupt the CPU.
4. After reading [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* = 1, the software reads I2Cn_CTRL.read to determine whether the transaction is a transmit (read = 1) or receive (read = 0) operation. In this case, assume read = 1, indicating transmit.

    a. At this point, the hardware holds SCL low until the software clears [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout* and loads data into the FIFO.

5. The software clears [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* and [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout*. Now that [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout* is 0, the software can begin loading the transmit data into I2Cn_FIFO.
6. As soon as there is data in the FIFO, the hardware releases SCL (after counting out [I2Cn_CLKHI](#i2c-clock-high-time-register).*hi*) and sends out the data on the bus.
7. While the master keeps requesting data and sending ACKs, [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* remains 0, and the software should continue to monitor the transmit FIFO and refill it as needed.

    a. The FIFO level can be monitored synchronously through the transmit FIFO status/interrupt flags or asynchronously by setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val* and setting the [I2Cn_INTEN0](#i2c-interrupt-enable-0-register).*tx_thd* interrupt.
    b. If the transmit FIFO ever empties during the transaction, the hardware starts clock stretching and wait for it to be refilled.

8. The master ends the transaction by sending a NACK. Once this happens, the [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* interrupt flag is set, and the software can stop monitoring the transmit FIFO.
9. The transaction is complete. The software should clean up, including clearing [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* and clearing [I2Cn_INTEN0](#i2c-interrupt-enable-0-register).*tx_thd* interrupt. Return to step 3, waiting on an address match.
10. If the software needs to know how many data bytes were transmitted to the master, it should check the transmit FIFO level as soon as the software sees [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* = 1 and use that to determine how many data bytes were successfully sent.

    a. Note that any data remaining in the transmit FIFO is discarded before the next transmit operation; it is NOT necessary for the software to manually flush the transmit FIFO for this to occur.

##### Preload Mode
The other mode of operation for slave transmit is preload mode. In this mode, it is assumed that the software knows before the transmit operation what data it should send to the master. This data is then preloaded into the transmit FIFO. Once the address match occurs, this data can be sent out without any software intervention. Preload mode can be used with clock stretching either enabled or disabled, but it is the only option if clock stretching must be disabled.

To use slave transmit preload mode:

1. With [I2Cn_CTRL](#i2c-control-register).*en* = 0, initialize all relevant registers, including:

    a. Set the [I2Cn_SLAVE](#i2c-slave-address-register).*addr* field with the desired I2C slave address.

    b. Set the [I2Cn_SLAVE](#i2c-slave-address-register).*ext_addr_en* for either 7-bit or 10-bit addressing.

    c. Preload mode specific settings:

        i) I2Cn_CTRL.cl_clk_stretch_dis = 1
        ii) I2Cn_TXCTRL0[5:2] = 0xF
        iii) I2Cn_TXCTRL0.preload_mode = 1.

2. The software sets I2Cn_CTRL.en = 1.

    a. Even though the controller is enabled, at this point, it will not ACK an address match with R/W = 1 until the software sets [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* = 1.

3. The software prepares for the transmit operation by loading data into the transmit FIFO, enabling DMA, setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val*, and setting the [I2Cn_INTEN0](#i2c-interrupt-enable-0-register).*tx_thd* interrupt, as well as any other required settings.

    a. If clock stretching is disabled, an empty transmit FIFO during the transmit operation causes a transmit underrun error. Therefore, the software should take any necessary steps to avoid an underrun before setting [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* = 1.

    b. If clock stretching is enabled, then an empty transmit FIFO does not cause a transmit underrun error. However, it is recommended to follow the same preparation steps to minimize the amount of time spent clock stretching, letting the transaction complete as quickly as possible.

4. Once the software has prepared for the transmit operation, the software should set [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* = 1.

    a. The controller is now fully enabled and responds with an ACK to an address match.

    b. The hardware sets I2Cn_INTFL0.addr_match once an address match has occurred. [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout* is NOT set and remains 0.

5. The software waits for [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* = 1, either through polling the interrupt flag or setting [I2Cn_INTEN0](#i2c-interrupt-enable-0-register).*amie* to interrupt the CPU.

6. After seeing [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* = 1, the software reads [I2Cn_CTRL](#i2c-control-register).read to determine whether the transaction is a transmit (read = 1) or receive (read = 0) operation. In this case, assume [I2Cn_CTRL](#i2c-control-register).*read*, indicating transmit:

    a. At this point, the hardware begins sending out the preloaded data into the transmit FIFO.

    b. Once the first data byte is sent, the hardware also automatically clears [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* to 0.

7. While the master keeps requesting data and sending ACKs, [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* remains 0, and the software should continue to monitor the transmit FIFO and refill it as needed.

    a. The FIFO level can be monitored synchronously through the transmit FIFO interrupt flags or asynchronously by setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val* and setting [I2Cn_INTEN0](#i2c-interrupt-enable-0-register).*tx_thd* interrupt.

    b. If clock stretching is disabled and the transmit FIFO ever empties during the transaction, the hardware sets [I2Cn_INTFL1](#i2c-interrupt-flags-1-register).*tx_un* = 1 and sends 0xFF for all following data bytes requested by the master.

8. The master ends the transaction by sending a NACK. Once this happens, the [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* interrupt flag is set.

    a. If the transmit FIFO goes empty at the same time that the master indicates the transaction is complete by sending a NACK, this is not considered an underrun event, [I2Cn_INTFL1](#i2c-interrupt-flags-1-register).*tx_un* flag remains 0.

9. The transaction is complete. The software should clean up, which should include clearing [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done*.

    a. If the software needs to know how many data bytes were transmitted to the master, it should check the transmit FIFO level as soon as the software sees [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* = 1 and use that to determine how many data bytes were successfully sent.

    b. By default, any data remaining in the transmit FIFO is NOT discarded and instead is reused for the next transmit operation.

    c. If this is not desired, the software can flush the transmit FIFO. The safest way to do this is by clearing and then resetting [I2Cn_CTRL](#i2c-control-register).*en*. This flushes both the transmit and receive FIFOs.

    d. Return to step 3 and prepare for the next transaction.

Once a slave starts transmitting out of the I2Cn_FIFO, detection of an out of sequence STOP, START, or RESTART condition terminates the current transaction. When a transaction is terminated in such a manner, [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*start_err* or [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*stop_err* is set to 1.

If the transmit FIFO is not ready ([I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* = 0) and the I2C controller receives a data read request from the master, the hardware automatically sends a NACK at the end of the first address byte. In this case, the setting of the do not respond field is ignored by the hardware because the only opportunity to send a NACK for an I2C read transaction is after the address byte.

#### Slave Receivers
The device operates as a slave receiver when the received address matches the device slave address with the R/W bit set to 0. The external master is writing to the slave.

Program flow for a receive operation is as follows:

1. With [I2Cn_CTRL](#i2c-control-register).*en* = 0, initialize all relevant registers, including:

    a. Set the [I2Cn_SLAVE](#i2c-slave-address-register).*addr* field with the desired I2C slave address.

    b. Set the [I2Cn_SLAVE](#i2c-slave-address-register).*ext_addr_en* for either 7-bit or 10-bit addressing.

2. Set [I2Cn_CTRL](#i2c-control-register).*en* = 1.

    a. If an address match with R/W = 0 occurs, and the receive FIFO is empty, the peripheral responds with an ACK, and the [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* flag is set.

    b. If the receive FIFO is not empty, then depending on the value of [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*dnr*, the peripheral NACKs either the address byte ([I2Cn_RXCTRL0](#i2c-receive-control-0-register).*dnr* = 1) or the first data byte ([I2Cn_RXCTRL0](#i2c-receive-control-0-register).*dnr* = 0).

3. Wait for [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* = 1, either by polling or by enabling the wr_addr_match interrupt to the CPU. Once a successful address match occurs, the hardware sets [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match* = 1.
4. Read [I2Cn_CTRL](#i2c-control-register).*read* to determine whether the transaction is a transmit ([I2Cn_CTRL](#i2c-control-register).*read* = 1) or receive ([I2Cn_CTRL](#i2c-control-register).*read* = 0) operation. In this case, assume [I2Cn_CTRL](#i2c-control-register).*read* = 0, indicating receive. At this point, the device begins receiving data into the receive FIFO.
5. Clear [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*addr_match*, and while the master keeps sending data, [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* remains 0, and the software should continue to monitor the receive FIFO and empty it as needed.

    a. The FIFO level can be monitored synchronously through the receive FIFO status/interrupt flags or asynchronously by setting [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl* and enabling the [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*rx_thd* interrupt.

    b. If the receive FIFO ever fills up during the transaction, then the hardware sets [I2Cn_INTFL1](#i2c-interrupt-flags-1-register).*rx_ov* and then either:

        i. If I2Cn_CTRL.clkstr_dis = 0, start clock stretching and wait for the software to read from the receive FIFO, OR
        ii. If I2Cn_CTRL.clkstr_dis = 1, respond to the master with a NACK, and the last byte is discarded.

6. The master ends the transaction by sending a RESTART or STOP. Once this happens, the [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*done* interrupt flag is set, and software can stop monitoring the receive FIFO.

7. Once a slave starts receiving into its receive FIFO, detection of an out of sequence STOP, START, or RESTART condition releases the I2C bus to the Idle state, and the hardware sets the [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*start_err* field or [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*stop_err* field to 1 based on the specific condition.

Suppose the software has not emptied the data in the receive FIFO from the previous transaction by the time a master addresses it for another write transaction (i.e., a slave receive). In that case, the controller does not participate in the transaction, and no additional data is written into the FIFO. Although a NACK is sent to the master, the software can control whether the NACK is sent with the initial address match or at the end of the first data byte. Setting [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*dnr* to 1 sends the NACK with the initial address match. Setting [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*dnr* to 0 chooses to send the NACK at the end of the first data byte.

### Interrupt Sources
The I²C controller has a very flexible interrupt generator that generates an interrupt signal to the interrupt controller on any of several events. On recognizing the I²C interrupt, the software determines the cause of the interrupt by reading the I²C interrupt flags registers [I2Cn_INTFL0](#i2c-interrupt-flags-0-register) and [I2Cn_INTFL1](#i2c-interrupt-flags-1-register). Interrupts can be generated for the following events:

- In either master or slave mode:

    - Transaction complete
    - Transaction timeout
    - FIFO is empty, not empty, and full to a configurable threshold level
    - Transmit FIFO lockout during a FIFO flush
    - Out of sequence START and STOP conditions

- In master mode only:

    - Address ACK or NACK received from the slave
    - Data NACK received from the slave
    - Lost arbitration

- In slave mode only:

    - Sent a NACK to an external master because the transmit or receive FIFO was not ready
    - Incoming address match
    - Transmit FIFO underflow or receive FIFO overflow

Interrupts for each event can be enabled or disabled by setting or clearing the corresponding bit in the [I2Cn_INTEN0](#i2c-interrupt-enable-0-register) or [I2Cn_INTEN1](#i2c-interrupt-enable-1-register) interrupt enable registers.

*Note: Disabling the interrupt does not prevent the corresponding flag from being set by the hardware but does prevent an interrupt when the interrupt flag is set.*

*Note: Before enabling an interrupt, the status of the corresponding interrupt flag should be checked and, if necessary, serviced or cleared. This prevents a previous interrupt event from interfering with a new I2C communications session.*

### Transmit FIFO and Receive FIFO
There are separate transmit and receive FIFOs. Both are accessed using the FIFO data register [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register). Writes to this register enqueue data into the transmit FIFO. Writing to a full transmit FIFO has no effect. Reads from [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register) dequeue data from the receive FIFO. Writing to a full transmit FIFO has no effect, and reading from an empty receive FIFO returns 0xFF.

The transmit and receive FIFO only reads or writes one byte at a time. Transactions larger than 8 bits can still be performed, however. A 16- or 32-bit write to the transmit FIFO stores just the lowest 8 bits of the write data. A 16- or 32-bit read from the receive FIFO has the valid data in the lowest 8 bits and 0's in the upper bits. In any case, the transmit and receive FIFOs only accepts 8 bits at a time for either read or write.

To offload work from the CPU, the DMA can read and write to each FIFO. See section DMA Control for more information on configuring the DMA.

During a receive transaction (which during master operation is a READ, and during slave operation is a WRITE), received bytes are automatically written to the receive FIFO. The software should monitor the receive FIFO level and unload data from it as needed by reading [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register). If the receive FIFO becomes full during a master mode transaction, then the controller sets the [I2Cn_INTFL1](#i2c-interrupt-flags-1-register).*rx_ov* the [I2Cn_INTFL1](#i2c-interrupt-flags-1-register).*rx_ov* bit, and one of two things happen, depending on the value of [I2Cn_CTRL](#i2c-control-register).*clkstr_dis*:

- If clock stretching is enabled ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 0), then the controller stretches the clock until the software makes space available in the receive FIFO by reading from [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register). Once space is available, the peripheral moves the data byte from the shift register into the receive FIFO, the SCL device pin is released, and the master is free to continue the transaction.

- If clock stretching is disabled ([I2Cn_CTRL](#i2c-control-register).*clkstr_dis* = 1), the controller responds to the master with a NACK, and the data byte is lost. The master can return the bus to idle with a STOP condition or start a new transaction with a RESTART condition.

During a transmit transaction (which during master operation is a WRITE, and during slave operation is a READ), either the software or the DMA can provide data to be transmitted by writing to the transmit FIFO. Once the peripheral finishes transmitting each byte, it removes it from the transmit FIFO and, if available, begins transmitting the next byte.

Interrupts can be generated for the following FIFO status:

- Transmit FIFO level less than or equal to the threshold
- Receive FIFO level greater than or equal to the threshold
- Transmit FIFO underflow
- Receive FIFO overflow
- Transmit FIFO locked for writing

Both the receive and transmit FIFOs are flushed when the I2C port is disabled by clearing [I2Cn_CTRL](#i2c-control-register).en = 0. While the peripheral is disabled, writes to the transmit FIFO have no effect and reads from the receive FIFO return 0xFF.

The transmit FIFO and receive FIFO can be flushed by setting the transmit FIFO flush bit ([I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*flush* = 1) or the receive FIFO flush bit ([I2Cn_RXCTRL0](#i2c-receive-control-0-register).*flush* = 1), respectively. In addition, under certain conditions, the transmit FIFO is automatically locked by the hardware and flushed so stale data is not unintentionally transmitted. The transmit FIFO is automatically flushed and writes locked out from software under the following conditions:

- General call address match 

    - Automatic flushing and lockout can be disabled by setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*gc_addr_flush_dis*.

- Slave address match write 

    - Automatic flushing and lockout can be disabled by setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*wr_addr_flush_dis*.

- Slave address match read 

    - Automatic flushing and lockout can be disabled by setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*rd_addr_flush_dis*.

- During operation as a slave transmitter, a NACK is received. 

    - Automatic flushing and lockout can be disabled by setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*nack_flush_dis*.

- Any of the following interrupts (Automatic flushing cannot be disabled for these conditions):

    - Arbitration error
    - Timeout error
    - Master mode address NACK error
    - Master mode data NACK error
    - Start error
    - Stop error

When the above conditions occur, the transmit FIFO is flushed so that data intended for a previous transaction is not transmitted unintentionally for a new transaction. In addition to flushing the transmit FIFO, the transmit lockout flag is set ([I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout* = 1), and writes to the transmit FIFO are ignored until the software acknowledges the external event by clearing [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*tx_lockout*.

### Transmit FIFO Preloading
There may be situations during slave mode operation where software wants to preload the transmit FIFO before transmission, such as when clock stretching is disabled. In this scenario, rather than responding to an external master requesting data with an ACK and clock stretching while software writes the data to the transmit FIFO, the controller instead responds with a NACK until the software has preloaded the requested data into the transmit FIFO.

When transmit FIFO preloading is enabled, the software controls ACKs to the external master using the transmit ready ([I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy*) bit. When [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* is set to 0, the hardware automatically NACKs all read transactions from the master. Setting [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* to 1 sends an ACK to the master on the next read transaction and transmits the data in the transmit FIFO. Preloading the transmit FIFO should be complete before setting the [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* field to 1.

The required steps for implementing transmit FIFO Preloading in an application are as follow:

1. Enable transmit FIFO preloading by setting [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*preload_mode* to 1. This automatically clears [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* to 0.
2. If the transmit FIFO lockout flag ([I2Cn_INTFL0](#i2c-transmit-control-0-register).*tx_lockout*) is set to 1, write 1 to clear the flag and enable writes to the transmit FIFO.
3. Enable DMA or Interrupts if required.
4. Load the transmit FIFO with the data to send when the master sends the next read request.
5. Set [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* to 1 to automatically let the hardware send the preloaded FIFO on the next read from a master.
6. [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* is cleared by the hardware once it finishes transmitting the first byte, and data is transmitted from the transmit FIFO. Once cleared, the software may repeat the preloading process or disable transmit FIFO preloading.

*Note: To prevent the preloaded data from being cleared when the master tries to read it, the software must at least set [I2Cn_INTFL0](#i2c-transmit-control-0-register).*rd_addr_flush_dis* to 1, disabling auto flush on READ address match. The software determines whether the other auto flush disable bits should be set. For example, if a master uses I2C WRITE transactions to determine what data the slave should send in the following READ transactions, then the software can clear [I2Cn_INTFL0](#i2c-transmit-control-0-register).*wr_addr_flush_dis* to 0. Then when a WRITE occurs, the transmit FIFO is flushed, giving the software time to load the new data. For the READ transaction, the external master can poll the slave address until the new data has been loaded and [I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*preload_rdy* is set, at which point the peripheral responds with an ACK.*

### Interactive Receive Mode (IRXM)
In some situations, the I2C peripheral might want to inspect and respond to each byte of received data. In this case, IRXM can be used. IRXM is enabled by setting [I2Cn_CTRL](#i2c-control-register).*irxm_en* = 1. If IRXM is enabled, it must occur before any I2C transfer is initiated.

When IRXM is enabled, after every data byte received, the I2C peripheral automatically holds SCL low before the ACK bit. Additionally, after the 8th SCL falling edge, the I2C peripheral sets the IRXM interrupt status flag ([I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*irxm* = 1). The software must read the data and generate a response (ACK or NACK) by setting the IRXM acknowledge ([I2Cn_CTRL](#i2c-control-register).*irxm_ack*) bit accordingly. Send an ACK by clearing the [I2Cn_CTRL](#i2c-control-register).*irxm_ack* bit to 0. Send a NACK by setting the [I2Cn_CTRL](#i2c-control-register).*irxm_ack* bit to 1.

After setting the [I2Cn_CTRL](#i2c-control-register).*irxm_ack* bit, clear the IRXM interrupt flag. Write 1 to [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*irxm* to clear the interrupt flag. When the IRXM interrupt flag is cleared, the I2C peripheral hardware releases the SCL line and sends the [I2Cn_CTRL](#i2c-control-register).*irxm_ack* on the SDA line. 

While the I2C peripheral is waiting for the software to clear the [I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*irxm* flag, the software can disable IRXM and, if operating as a master, load the remaining number of bytes to be received for the transaction. This allows the software to examine the initial bytes of a transaction, which might be a command, and then disable IRXM to receive the remaining bytes in normal operation.

During IRXM, received data is not placed in the receive FIFO. Instead, the [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register) address is repurposed to directly read the receive shift register, bypassing the receive FIFO. Therefore, before disabling IRXM, the software must first read the data byte from [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register).*data*. If the IRXM byte is not read, the byte is lost, and the next read from the receive FIFO returns 0xFF.

*Note: IRXM does not apply to address bytes, only to data bytes.*

*Note: IRXM does not apply to general call address responses or START byte responses.*

*Note: When enabling IRXM and operating as a slave, clock stretching must remain enabled ([I2Cn_CTRL](#i2c-control-register).clkstr_dis = 0).*

### Clock Stretching
When the I2C peripheral requires some response or intervention from the software to continue with a transaction, it holds SCL low, preventing the transfer from continuing. This is called "clock stretching" or "stretching the clock." While the I2C Bus Specification defines the term "clock stretching" to only apply to a slave device holding the SCL line low, this section describes situations where the I2C peripheral holds the SCL line low in either slave or master mode and refers to both as clock stretching.

When the I2C peripheral stretches the clock, it typically does so in response to either a full receive FIFO during a receive operation or an empty transmit FIFO during a transmit operation. Necessarily, this occurs before the next data byte begins, either between the ACK bit and the first data bit or, if at the beginning of a transaction, immediately after a START or RESTART condition. However, when operating in IRXM ([I2Cn_CTRL](#i2c-control-register).*irxm_en* = 1), the peripheral can also clock stretch before the ACK bit, allowing the software to decide whether to send an ACK or NACK.

For a transmit operation (as either master or slave), when the transmit FIFO is empty, SCL is automatically held low after the ACK bit and before the next data byte begins. The software must write data to [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register).*data* to stop clock stretching and continue the transaction. However, if operating in master mode, instead of sending more data, the software may also set either [I2Cn_MSTCTRL](#i2c-master-control-register).*stop* or [I2Cn_MSTCTRL](#i2c-master-control-register).*restart* to send a STOP or RESTART condition, respectively.

For a receive operation (as either master or slave), when both the receive FIFO and the receive shift register are full, SCL is automatically held low until at least one data byte is read from the receive FIFO. The software must read data from [I2Cn_FIFO](#i2c-transmit-and-receive-fifo-register).*data* to stop clock stretching and continue the transaction. If operating in master mode and this is the final byte of the transaction, as determined by [I2Cn_RXCTRL1](#i2c-receive-control-1-register).*cnt*, the software must also set either [I2Cn_MSTCTRL](#i2c-master-control-register).*stop* or [I2Cn_MSTCTRL](#i2c-master-control-register).*restart* to send a STOP or RESTART condition, respectively. This must be done in addition to reading from the receive FIFO since the peripheral cannot start sending the STOP or RESTART until the last data byte has been moved from the receive shift register into the receive FIFO. (This automatically occurs once there is space in the receive FIFO.)

*Note: Since some masters do not support other devices stretching the clock, it is possible to completely disable all clock stretching during slave mode by setting I2Cn_CTRL.clkstr_dis to 1 and clearing [I2Cn_CTRL](#i2c-control-register).irxm_en to 0. In this case, instead of clock stretching, the peripheral automatically sends a NACK if receiving data or sends 0xFF if transmitting data.*

*Note: The clock synchronization required to support other I2C master or slave devices stretching the clock is built into the peripheral and requires no intervention from software to operate correctly.*

### Bus Timeout
The timeout register, [I2Cn_TIMEOUT](#i2c-timeout-register).*scl_to_val*, is used to detect bus errors. [Equation 14-8](#equation14-8) and [Equation 14-9](#equation14-9) show equations for calculating the maximum and minimum timeout values based on the value loaded into the [I2Cn_TIMEOUT](#i2c-timeout-register).*scl_to_val* field.

Equation 14-8: I2C Timeout Maximum
<a name="equation14-8"></a>

$$t_{TIMEOUT} \leq \left(
\frac{1}{f_{I2C\text{_}CLK}} \right) \times \left( \left(
I2Cn\text{_}TIMEOUT.scl\text{_}to\text{_}val \times 32 \right) + 3
\right)$$

Due to clock synchronization, the timeout is guaranteed to meet the following minimum time calculation shown in [Equation 14-9](#equation14-9).

Equation 14-9: I2C Timeout Minimum
<a name="equation14-9"></a>

$$t_{TIMEOUT} \leq \left(
\frac{1}{f_{I2C\text{_}CLK}} \right) \times \left( \left(
I2Cn\text{_}TIMEOUT.scl\text{_}to\text{_}val \times 32 \right) + 2
\right)$$

The timeout feature is disabled when [I2Cn_TIMEOUT](#i2c-timeout-register).*scl_to_val* = 0 and is enabled for any non-zero value. When the timeout is enabled, the timeout timer starts counting when the I2C peripheral hardware drives SCL low and is reset by the I2C peripheral hardware when the SCL line is released.

The timeout counter only monitors if the I2C peripheral hardware is driving the SCL line low. It does not monitor if an external I2C device is actively holding the SCL line low. The timeout counter also does not monitor the status of the SDA line.

If the timeout timer expires, a bus error condition has occurred. When a timeout error occurs, the I2C peripheral hardware releases the SCL and SDA lines and sets the timeout error interrupt flag to 1 ([I2Cn_INTFL0](#i2c-interrupt-flags-0-register).*to_err* = 1).

For applications where the device may hold the SCL line low longer than the maximum timeout supported, the timeout can be disabled by setting the timeout field to 0 ([I2Cn_TIMEOUT](#i2c-timeout-register).*scl_to_val* = 0).

### DMA Control
There are independent DMA channels for each transmit FIFO and receive FIFO. DMA activity is triggered by the transmit FIFO ([I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val*) and receive FIFO ([I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl*) threshold levels.

When the transmit FIFO byte count ([I2Cn_TXCTRL1](#i2c-transmit-control-1-register).*lvl*) is less than or equal to the transmit FIFO Threshold Level [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val*, then the DMA transfers data into the transmit FIFO according to the DMA configuration.

The DMA burst size should be set as shown in [Equation 14-10](#equation14-10) to ensure the DMA does not overflow the transmit FIFO:

*Equation 14-10: DMA Burst Size Calculation for I2C Transmit*
<a name="equation14-10"></a>

$$
\text{DMA Burst Size} \leq \text{TX FIFO Depth} - \text{I2C}_n\text{TXCTRL0.tx_thresh} = 8 - \text{I2C}_n\text{TXCTRL0.tx_thresh}, \\
$$

$$
\text{where } 0 \leq \text{I2C}_n\text{TXCTRL0.tx_thresh} \leq 7
$$

Applications trying to avoid transmit underflow or clock stretching should use a smaller burst size and higher [I2Cn_TXCTRL0](#i2c-transmit-control-0-register).*thd_val* setting. This fills up the FIFO more frequently but increases internal bus traffic.

When the receive FIFO count ([I2Cn_RXCTRL1](#i2c-receive-control-1-register).*lvl*) is greater than or equal to the receive FIFO Threshold Level [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl*, the DMA transfers data out of the receive FIFO according to the DMA configuration. The DMA burst size should be set as shown in [Equation 14-11](#equation14-11) to ensure the DMA does not underflow the receive FIFO:

*Equation 14-11: DMA Burst Size Calculation for I2C Receive*
<a name="equation14-11"></a>

$$
\text{DMA Burst Size} \leq \text{I2C}_n\text{RXCTRL0.rx_thresh} 
$$

$$
\text{where } 1 \leq \text{I2C}_n\text{RXCTRL0.rx_thresh} \leq 8
$$

Applications trying to avoid receive overflow or clock stretching should use a smaller burst size and lower [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl*. This results in reading from the receive FIFO more frequently but increases internal bus traffic.

Note: For receive operations, the length of the DMA transaction (in bytes) must be an integer multiple of [I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl*. Otherwise, the receive transaction ends with some data still in the receive FIFO, but not enough to trigger an interrupt to the DMA, leaving the DMA transaction incomplete. One easy way to ensure this for all transaction lengths is to set burst size to 1 ([I2Cn_RXCTRL0](#i2c-receive-control-0-register).*thd_lvl* = 1).

To enable DMA transfers, enable the transmit DMA channel ([I2Cn_DMA](#i2c-dma-enable-register).*tx_en*) and the receive DMA channel ([I2Cn_DMA](#i2c-dma-enable-register).*rx_en*) if receiving data.

## Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. If multiple instances of the peripheral are provided, each instance has its own independent set of the registers shown in [Table 14-5](#table14-5-i2c-register-summary). Register names for a specific instance are defined by replacing "n" with the instance number. For example, a register PERIPHERALn_CTRL resolves to PERIPHERAL0_CTRL and PERIPHERAL1_CTRL for instances 0 and 1, respectively.

See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 14-5: I²C Register Summary*
<a name="table14-5-i2c-register-summary"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th>Offset</th>
    <th>Register</th>
    <th>Description</th>
  </tr>
</thead>
<tr>
    <td>[0x0000]</td>
    <td><a href="#i2c-control-register">I2Cn_CTRL</a></td>
    <td>I2C Control Register</td>
</tr>
<tr>
    <td>[0x0004]</td>
    <td><a href="#i2c-status-register">I2Cn_STATUS</a></td>
    <td>I2C Status Register</td>
</tr>
<tr>
    <td>[0x0008]</td>
    <td><a href="#i2c-interrupt-flags-0-register">I2Cn_INTFL0</a></td>
    <td>I2C Interrupt Flags 0 Register</td>
</tr>
<tr>
    <td>[0x000C]</td>
    <td><a href="#i2c-interrupt-enable-0-register">I2Cn_INTEN0</a></td>
    <td>I2C Interrupt Enable 0 Register</td>
</tr>
<tr>
    <td>[0x0010]</td>
    <td><a href="#i2c-interrupt-flags-1-register">I2Cn_INTFL1</a></td>
    <td>I2C Interrupt Flags 1 Register</td>
</tr>
<tr>
    <td>[0x0014]</td>
    <td><a href="#i2c-interrupt-enable-1-register">I2Cn_INTEN1</a></td>
    <td>I2C Interrupt Enable 1 Register</td>
</tr>
<tr>
    <td>[0x0018]</td>
    <td><a href="#i2c-fifo-length-register">I2Cn_FIFOLEN</a></td>
    <td>I2C FIFO Length Register</td>
</tr>
<tr>
    <td>[0x001C]</td>
    <td><a href="#i2c-receive-control-0-register">I2Cn_RXCTRL0</a></td>
    <td>I2C Receive Control 0 Register</td>
</tr>
<tr>
    <td>[0x0020]</td>
    <td><a href="#i2c-receive-control-1-register">I2Cn_RXCTRL1</a></td>
    <td>I2C Receive Control 1 Register</td>
</tr>
<tr>
    <td>[0x0024]</td>
    <td><a href="#i2c-transmit-control-0-register">I2Cn_TXCTRL0</a></td>
    <td>I2C Transmit Control 0 Register</td>
</tr>
<tr>
    <td>[0x0028]</td>
    <td><a href="#i2c-transmit-control-1-register">I2Cn_TXCTRL1</a></td>
    <td>I2C Transmit Control 1 Register</td>
</tr>
<tr>
    <td>[0x002C]</td>
    <td><a href="#i2c-transmit-and-receive-fifo-register">I2Cn_FIFO</a></td>
    <td>I2C Transmit and Receive FIFO Register</td>
</tr>
<tr>
    <td>[0x0030]</td>
    <td><a href="#i2c-master-control-register">I2Cn_MSTCTRL</a></td>
    <td>I2C Master Control Register</td>
</tr>
<tr>
    <td>[0x0034]</td>
    <td><a href="#i2c-clock-low-time-register">I2Cn_CLKLO</a></td>
    <td>I2C Clock Low Time Register</td>
</tr>
<tr>
    <td>[0x0038]</td>
    <td><a href="#i2c-clock-high-time-register">I2Cn_CLKHI</a></td>
    <td>I2C Clock High Time Register</td>
</tr>
<tr>
    <td>[0x003C]</td>
    <td><a href="#i2c-hs-mode-clock-control-register">I2Cn_HSCLK</a></td>
    <td>I2C Hs-Mode Clock Control Register</td>
</tr>
<tr>
    <td>[0x0040]</td>
    <td><a href="#i2c-timeout-register">I2Cn_TIMEOUT</a></td>
    <td>I2C Timeout Register</td>
</tr>
<tr>
    <td>[0x0048]</td>
    <td><a href="#i2c-dma-enable-register">I2Cn_DMA</a></td>
    <td>I2C DMA Enable Register</td>
</tr>
<tr>
    <td>[0x004C]</td>
    <td><a href="#i2c-slave-address-register">I2Cn_SLAVE</a></td>
    <td>I2C Slave Address Register</td>
</tr>
</table>


### Register Details

*Table 14-6: I²C Control Register*
<a name="i2c-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I²C Control</td>
        <td colspan="1">I2Cn_CTRL</td>
        <td>[0x0000]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
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
        <td>hs_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Hs-Mode Enable</strong><br>
        Set this field to 1 for I²C Hs-Mode operation.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>14</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>13</td>
        <td>one_mst_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Single Master Only</strong><br> When set to 1, the device MUST ONLY be used in a single master application with slave devices that are NOT going to hold SCL low (i.e., the slave devices never clock stretch)</td>
    </tr>
    <tr>
        <td>12</td>
        <td>clkstr_dis</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Clock Stretching</strong>
        <div style="margin-left: 20px;">
        0: Enabled<br>
        1: Disabled
        </td>
    </tr>
    <tr>
        <td>11</td>
        <td>read</td>
        <td>R</td>
        <td>0</td>
        <td><strong>Slave Read/Write Bit Status</strong><br>Returns the logic level of the R/W bit on a received address match (<em>I2Cn_INTFL0</em>.<em>addr_match</em> = 1) or general call match
        (<em>I2Cn_INTFL0</em>.<em>gc_addr_match = </em>1). This bit is valid for three system clock cycles after the address match status flag is set.</td>
    </tr>
    <tr>
        <td>10</td>
        <td>bb_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Software Output Control Enabled</strong><br> Setting this field to 1 enables software bit-bang control of the I<sup>2</sup>C Bus.
        <div style="margin-left: 20px;">
        0: The I<sup>2</sup>C controller manages the SDA and SCL pins in the hardware.<br>
        1: SDA and SCL are controlled by the software using the <em>I2Cn_CTRL</em>.<em>sda_out</em> and <em>I2Cn_CTRL</em>.<em>scl_out</em> fields.</div>
        </td>
    </tr>
    <tr>
        <td>9</td>
        <td>sda</td>
        <td>R</td>
        <td>-</td>
        <td><strong>SDA Status</strong>
        <div style="margin-left: 20px;">
        0: SDA pin is logic low.<br>
        1: SDA pin is logic high.</div>
        </td>
    </tr>
    <tr>
        <td>8</td>
        <td>scl</td>
        <td>R</td>
        <td>-</td>
        <td><strong>SCL Status</strong>
        <div style="margin-left: 20px;">
        0: SCL pin is logic low.<br>
        1: SCL pin is logic high.</div></td>
    </tr>
    <tr>
        <td>7</td>
        <td>sda_out</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>SDA Pin Output Control</strong><br> Set the state of the SDA hardware pin (actively pull low or float).
        <div style="margin-left: 20px;">
        0: Pull SDA Low<br>
        1: Release SDA</div>
        <em>Note: Only valid when I2Cn_CTRL.bb_mode = 1</em>
        </td>
    </tr>
    <tr>
        <td>6</td>
        <td>scl_out</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>SCL Pin Output Control</strong><br>
        Set the state of the SCL hardware pin (actively pull low or float).
        <div style="margin-left: 20px;">
        0: Pull SCL low<br>
        1: Release SCL</div>
        <em>Note: Only valid when I2Cn_CTRL.bb_mode = 1</em>
        </td>
    </tr>
    <tr>
        <td>5</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>4</td>
        <td>irxm_ack</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>IRXM Acknowledge</strong><br>If IRXM is enabled (<em>I2Cn_CTRL</em>.<em>rx_mode</em> = 1), this field determines if the hardware sends an ACK or a NACK to an IRXM transaction.
        <div style="margin-left: 20px;">
        0: Respond to IRXM with an ACK.<br>
        1: Respond to IRXM with a NACK.</div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>irxm_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>IRXM Enable</strong><br> When receiving data, this field allows for an interactive receive mode (IRXM) interrupt event after each received byte of data. The I<sup>2</sup>C peripheral hardware can be enabled to send either an ACK or NACK for IRXM. See the <em>Interactive Receive Mode</em> section for detailed information.
        <div style="margin-left: 20px;">
        0: Disable<br>
        1: Enable</div>
        <em>Note: Only set this field when the I<sup>2</sup>C bus is inactive.</em>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>gc_addr_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>General Call Address Enable</strong>
        <div style="margin-left: 20px;">
        0: Ignore General Call Address<br>
        1: Acknowledge General Call Address</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>mst_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Master Mode Enable</strong>
        <div style="margin-left: 20px;">
        0: Slave mode enabled.<br>
        1: Master mode enabled.</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>I<sup>2</sup>C Peripheral Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-7: I2C Status Register*
<a name="i2c-status-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Status</td>
        <td colspan="1">I2Cn_STATUS</td>
        <td>[0x0004]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
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
        <td>mst_busy</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Master Mode I<sup>2</sup>C Bus Transaction Active</strong><br>
        The peripheral is operating in master mode, and a valid transaction beginning with a START command is in progress on the I<sup>2</sup>C bus. This bit reads 1 until the master ends the transaction with a STOP command. This bit continues to read 1 while a slave performs clock stretching.
        <div style="margin-left: 20px;">
        0: Device not actively driving SCL clock cycles.<br>
        1: Device operating as master and actively driving SCL clock cycles.</div>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>tx_full</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Transmit FIFO Full</strong>
        <div style="margin-left: 20px;">
        0: Not full<br>
        1: Full</div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>tx_em</td>
        <td>RO</td>
        <td>1</td>
        <td><strong>Transmit FIFO Empty</strong>
        <div style="margin-left: 20px;">
        0: Not empty<br>
        1: Empty</div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>rx_full</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Receive FIFO Full</strong>
        <div style="margin-left: 20px;">
        0: Not full<br>
        1: Full</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>rx_em</td>
        <td>RO</td>
        <td>1</td>
        <td><strong>Receive FIFO Empty</strong>
        <div style="margin-left: 20px;">
        0: Not empty<br>
        1: Empty</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>busy</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Master or Slave Mode I<sup>2</sup>C Busy Transaction Active</strong>
        The peripheral is operating in master or slave mode, and a valid transaction beginning with a START command is in progress on the I<sup>2</sup>C bus. This bit reads 1 until the peripheral acting as a master or an external master ends the transaction with a STOP command. This bit continues to read 1 while a slave performs clock stretching.
        <div style="margin-left: 20px;">
        0: I<sup>2</sup>C bus is idle.<br>
        1: I<sup>2</sup>C bus transaction in progress.</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-8: I2C Interrupt Flag 0 Register*
<a name="i2c-interrupt-flags-0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Interrupt Flag 0</td>
        <td colspan="1">I2Cn_INTFL0</td>
        <td>[0x0008]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:24</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>23</td>
        <td>wr_addr_match</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Slave Write Address Match Interrupt Flag</strong><br> If set, the device has been accessed for a write (i.e., receive) transaction in slave mode, and the address received matches the device slave address.
        <div style="margin-left: 20px;">
        0: No address match.<br>
        1: Address match.</div>
        </td>
    </tr>
    <tr>
        <td>22</td>
        <td>rd_addr_match</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Slave Read Address Match Interrupt Flag</strong><br>
        If set, the device has been accessed for a read (i.e., transmit) transaction in slave mode, and the address received matches the device slave address.
        <div style="margin-left: 20px;">
        0: No address match.<br>
        1: Address match.</div>
        </td>
    </tr>
    <tr>
        <td>21:17</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>16</td>
        <td>-</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>MAMI Interrupt Flag</strong></td>
    </tr>
    <tr>
        <td>15</td>
        <td>tx_lockout</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Transmit FIFO Locked Interrupt Flag</strong><br> If set, the transmit FIFO is locked and writes to the transmit FIFO are ignored. When set, the transmit FIFO is automatically flushed. Writes to the transmit FIFO are ignored until this flag is cleared. Write 1 to clear.
        <div style="margin-left: 20px;">
        0: Transmit FIFO not locked.<br>
        1: Transmit FIFO is locked, and all writes to the transmit FIFO are ignored.</div>
        </td>
    </tr>
    <tr>
        <td>14</td>
        <td>stop_err</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Out of Sequence STOP Interrupt Flag</strong><br> This flag is set if a STOP condition occurs out of sequence. Write 1 to clear this field. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: Out of sequence STOP condition occurred.</div>
        </td>
    </tr>
    <tr>
        <td>13</td>
        <td>start_err</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Out of Sequence START Interrupt Flag</strong><br> This flag is set if a START condition occurs out of sequence. Write 1 to clear this field. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Error condition has not occurred.<br>
        1: Out of sequence START condition occurred.</div>
        </td>
    </tr>
    <tr>
        <td>12</td>
        <td>dnr_err</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Slave Mode Do Not Respond Interrupt Flag</strong><br> This flag is set if an address match is made, but the transmit FIFO or receive FIFO is not ready. Write 1 to clear this field. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: I<sup>2</sup>C address match has occurred, and either the transmit or receive FIFO is not configured.</div>
        </td>
    </tr>
    <tr>
        <td>11</td>
        <td>data_err</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Master Mode Data NACK from External Slave Interrupt Flag</strong><br> 
        This flag is set by hardware if a NACK is received from a slave. This flag is only valid if the I<sup>2</sup>C peripheral is configured for master mode operation. Write 1 to clear. Write 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: Data NACK received from a slave.</div>
        </td>
    </tr>
    <tr>
        <td>10</td>
        <td>addr_nack_err</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Master Mode Address NACK from Slave Error Flag</strong><br> The hardware sets this flag if an address NACK is received from a slave bus. This flag is only valid if the I<sup>2</sup>C peripheral is configured for master mode operation. Write 1 to clear. Write 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: Address NACK received from a slave.</div>
        </td>
    </tr>
    <tr>
        <td>9</td>
        <td>to_err</td>
        <td>R/ W1C</td>
        <td>0</td>
        <td><strong>Timeout Error Interrupt Flag</strong><br> This field is set to 1 when this device holds the SCL low longer than the programmed timeout value, in either master or slave mode. Write 1 to clear. Write 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: Timeout error occurred.
        </td>
    </tr>
    <tr>
        <td>8</td>
        <td>arb_err</td>
        <td>R/ W1C</td>
        <td>0</td>
        <td><strong>Master Mode Arbitration Lost Interrupt Flag</strong><br> Write 1 to clear. Write 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: Condition occurred.</div>
        </td>
    </tr>
    <tr>
        <td>7</td>
        <td>addr_ack</td>
        <td>R/ W1C</td>
        <td>0</td>
        <td><strong>Master Mode Address ACK from External Slave Interrupt Flag</strong><br> This field is set when a slave address ACK is received. Write 1 to clear. Write 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: The slave device ACK for the address was received.</div>
        </td>
    </tr>
    <tr>
        <td>6</td>
        <td>stop</td>
        <td>R/ W1C</td>
        <td>0</td>
        <td><strong>Slave Mode STOP Condition Interrupt Flag</strong><br>
        This flag is set by the hardware when a STOP condition is detected. Write 1 to clear. Write 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: Condition occurred.</div>
        </td>
    </tr>
    <tr>
        <td>5</td>
        <td>tx_thd</td>
        <td>RO</td>
        <td>1</td>
        <td><strong>Transmit FIFO Threshold Level Interrupt Flag</strong><br> The hardware sets this field if the number of bytes in the transmit FIFO is less than or equal to the transmit FIFO threshold level. Write 1 to clear. This field is automatically cleared by the hardware when the transmit FIFO contains fewer bytes than the transmit threshold level.
        <div style="margin-left: 20px;">
        0: Transmit FIFO contains more bytes than the transmit threshold level.<br>
        1: Transmit FIFO contains fewer than or equal to the transmit threshold level.</div>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>rx_thd</td>
        <td>R/W1C</td>
        <td>1</td>
        <td><strong>Receive FIFO Threshold Level Interrupt Flag</strong><br> The hardware sets this field if the number of bytes in the receive FIFO is greater than or equal to the receive FIFO threshold level. This field is automatically cleared when the receive FIFO contains fewer bytes than the receive threshold setting.
        <div style="margin-left: 20px;">
        0: Receive FIFO contains fewer bytes than the receive threshold level.<br>
        1: Receive FIFO contains at least receive threshold level of bytes.</div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>addr_match</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Slave Mode Incoming Address Match Status Interrupt Flag</strong><br> Write 1 to clear. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Slave address match has not occurred.<br>
        1: Slave address match occurred.</div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>gc_addr_match</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Slave Mode General Call Address Match Received Interrupt Flag</strong><br> Write 1 to clear. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: General call address match occurred.<br>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>irxm</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>IRXM Interrupt Flag</strong><br> 
        Write 1 to clear. Writing 0 is ignored.
        <div style="margin-left: 20px;">
        0: Normal operation<br>
        1: Interrupt condition occurred.</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>done</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Transfer Complete Interrupt Flag</strong><br>
        This flag is set for both master and slave mode once a transaction completes. Write 1 to clear. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Transfer is not complete.<br>
        1: Transfer complete.</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-9: I2C Interrupt Enable 0 Register*
<a name="i2c-interrupt-enable-0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Interrupt Enable 0</td>
        <td colspan="1">I2Cn_INTEN0</td>
        <td>[0x000C]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:24</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>23</td>
        <td>wr_addr_match</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Write Address Match Interrupt Enable</strong><br> This bit is set to enable interrupts when the device is accessed in slave mode, and the address received matches the device slave addressed for a write transaction.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>22</td>
        <td>rd_addr_match</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Read Address Match Interrupt Enable</strong><br> This bit is set to enable interrupts when the device is accessed in slave mode, and the address received matches the device slave addressed for a read transaction.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>21:17</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>16</td>
        <td>mami</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>MAMI Interrupt Enable</strong></td>
    </tr>
    <tr>
        <td>15</td>
        <td>tx_lockout</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Lock Out Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>14</td>
        <td>stop_err</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Out of Sequence STOP Condition Detected Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>13</td>
        <td>start_err</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Out of Sequence START Condition Detected Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>12</td>
        <td>dnr_err</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Do Not Respond Interrupt Enable</strong><br> Set this field to enable interrupts in slave mode when the "Do Not Respond" condition occurs.
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>11</td>
        <td>data_err</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Master Mode Received Data NACK from Slave Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>10</td>
        <td>addr_nack_err</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Master Mode Received Address NACK from Slave Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>9</td>
        <td>to_err</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Timeout Error Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>8</td>
        <td>arb_err</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Master Mode Arbitration Lost Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>7</td>
        <td>addr_ack</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Received Address ACK from Slave Interrupt Enable</strong><br> Set this field to enable interrupts for master mode slave device address ACK events.</p>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>6</td>
        <td>stop</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>STOP Condition Detected Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>5</td>
        <td>tx_thd</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Threshold Level Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>rx_thd</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Threshold Level Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>addr_match</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Incoming Address Match Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>gc_addr_match</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode General Call Address Match Received Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>irxm</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Interactive Receive Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>done</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transfer Complete Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-10: I2C Interrupt Flag 1 Register*
<a name="i2c-interrupt-flags-1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Interrupt Status Flags 1</td>
        <td colspan="1">I2Cn_INTFL1</td>
        <td>[0x0010]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:3</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>2</td>
        <td>start</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>START Condition Status Flag</strong><br> If set, a device START condition has been detected.
        <div style="margin-left: 20px;">
        0: START condition not detected.<br>
        1: START condition detected. </div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>tx_un</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Slave Mode Transmit FIFO Underflow Status Flag</strong><br> In slave mode operation, the hardware sets this flag automatically if the transmit FIFO is empty and the master requests more data by sending an ACK after the previous byte is transferred.
        <div style="margin-left: 20px;">
        0: Slave mode transmit FIFO underflow condition has not occurred.<br>
        1: Slave mode transmit FIFO underflow condition occurred.</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>rx_ov</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Slave Mode Receive FIFO Overflow Status Flag</strong><br> In slave mode operation, the hardware sets this flag automatically when a receive FIFO overflow occurs. Write 1 to clear. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Slave mode receive FIFO overflow event has not occurred.<br>
        1: Slave mode receive FIFO overflow condition occurred (data lost).</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-11: I2C Interrupt Enable 1 Register*
<a name="i2c-interrupt-enable-1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Interrupt Enable 1</td>
        <td colspan="1">I2Cn_INTEN1</td>
        <td>[0x0014]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:3</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>2</td>
        <td>start</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>START Condition Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled.<br>
        1: Enabled.</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>tx_un</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Transmit FIFO Underflow Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled.<br>
        1: Enabled.</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>rx_ov</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Receive FIFO Overflow Interrupt Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled.<br>
        1: Enabled.</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-12: I2C FIFO Length Register*
<a name="i2c-fifo-length-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C FIFO Length</td>
        <td colspan="1">I2Cn_FIFOLEN</td>
        <td>[0x0018]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:16</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>15:8</td>
        <td>tx_depth</td>
        <td>RO</td>
        <td>8</td>
        <td><strong>Transmit FIFO Length</strong><br>
        Reading this field returns the depth of the transmit FIFO.
        <div style="margin-left: 20px;">
        8: 8-bytes</div>
        </td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>rx_depth</td>
        <td>RO</td>
        <td>8</td>
        <td><strong>Receive FIFO Length</strong> <br>
        Reading this field returns the depth of the receive FIFO.
        <div style="margin-left: 20px;">
        8: 8-bytes</div></td>
    </tr>
</tbody>
</table>

*Table 14-13: I2C Receive Control 0 Register*
<a name="i2c-receive-control-0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Receive Control 0</td>
        <td colspan="1">I2Cn_RXCTRL0</td>
        <td>[0x001C]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:12</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>11:8</td>
        <td>thd_lvl</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Threshold Level</strong> <br>
        Set this field to the required number of bytes to trigger a receive FIFO threshold event. When the number of bytes in the receive FIFO is equal to or greater than this field, the hardware sets the <em>I2Cn_INTFL0</em>.<em>rx_thd</em> bit indicating a receive FIFO
        threshold level event.
        <div style="margin-left: 20px;">
        0: 0 bytes or more in the receive FIFO causes a threshold event.<br>
        1: 1+ bytes in the receive FIFO triggers a receive threshold event (recommended minimum value).<br>
        …<br>
        8: Receive FIFO threshold event only occurs when the receive FIFO is full.</div>
        </td>
    </tr>
    <tr>
        <td>7</td>
        <td>flush</td>
        <td>R/W1O</td>
        <td>0</td>
        <td><strong>Flush Receive FIFO</strong><br>
        Write 1 to this field to initiate a receive FIFO flush, clearing all data in the receive FIFO. This field is automatically cleared by the hardware when the receive FIFO flush completes. Writing 0 has no effect.
        <div style="margin-left: 20px;">
        0: Receive FIFO flush complete or not active.<br>
        1: Flush the receive FIFO</div>
        </td>
    </tr>
    <tr>
        <td>6:1</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>dnr</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Do Not Respond</strong><br>
        Slave mode operation only. If the device has been addressed for a write operation, and there is still data in the receive FIFO, then:
        <div style="margin-left: 20px;">
        0: Always respond to an address match with an ACK and always respond to data bytes with a NACK.<br>
        1: NACK the address.</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-14: I2C Receive Control 1 Register*
<a name="i2c-receive-control-1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Receive Control 1</td>
        <td colspan="1">I2Cn_RXCTRL1</td>
        <td>[0x0020]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:12</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>11:8</td>
        <td>lvl</td>
        <td>R</td>
        <td>0</td>
        <td><strong>Receive FIFO Byte Count Status</strong><br>
        This field returns the number of bytes in the receive FIFO.
        <div style="margin-left: 20px;">
        0: 0 bytes (No data)</p>
        1: 1 byte<br>
        2: 2 bytes<br>
        3: 3 bytes<br>
        4: 4 bytes<br>
        5: 5 bytes<br>
        6: 6 bytes<br>
        7: 7 bytes<br>
        8: 8 bytes
        </div></td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>cnt</td>
        <td>R/W</td>
        <td>1</td>
        <td><strong>Receive FIFO Transaction Byte Count Configuration</strong><br>
        When in master mode, write the number of bytes to be received in a transaction from 1 to 256. 0x00 represents 256.
        <div style="margin-left: 20px;">
        0: 256 byte receive transaction.<br>
        1: 1 byte receive transaction.<br>
        2: 2 byte receive transaction.<br>
        …<br>
        255: 255 byte receive transaction.<br>
        This field is ignored when <em>I2Cn_CTRL</em>.irxm_en = 1. To receive more than 256 bytes, use <em>I2Cn_CTRL</em>.irxm_en = 1</div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-15: I2C Transmit Control 0 Register*
<a name="i2c-transmit-control-0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Transmit Control 0</td>
        <td colspan="1">I2Cn_TXCTRL0</td>
        <td>[0x0024]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:12</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>11:8</td>
        <td>thd_val</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Threshold Level</strong> <br>
        This field sets the level for a transmit FIFO threshold event interrupt. If the number of bytes remaining in the transmit FIFO falls to this level or lower, the interrupt flag
        <em>I2Cn_INTFL0</em>.<em>thd_val</em> is set, indicating a transmit FIFO Threshold Event occurred.
        <div style="margin-left: 20px;">
        0: 0 bytes remaining in the transmit FIFO triggers a transmit FIFO threshold event.<br>
        1: 1 byte or fewer remaining in the transmit FIFO triggers a transmit FIFO threshold event (recommended minimum value).<br>
        …<br>
        7: 7 or fewer bytes remaining in the transmit FIFO triggers a transmit FIFO threshold event
        </div>
        </td>
    </tr>
    <tr>
        <td>7</td>
        <td>flush</td>
        <td>R/W1O</td>
        <td>0</td>
        <td><strong>Transmit FIFO Flush</strong><br>
        A transmit FIFO flush clears all remaining data from the transmit FIFO.
        <div style="margin-left: 20px;">
        0: transmit FIFO flush is complete or not active.<br>
        1: Flush the transmit FIFO</div>
        <em>Note: The hardware automatically clears this bit to 0 after it is written to 1 when the flush is completed.</em>
        <p>If <em>I2Cn_INTFL0</em>.<em>tx_lockout </em>= 1, then <em>I2Cn_TXCTRL0.flush </em>= 1.
        </p>
        </td>
    </tr>
    <tr>
        <td>6</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>5</td>
        <td>nack_flush_dis</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO received NACK Auto Flush Disable</strong><br>
        Various situations or conditions are described in this user guide that leads to the transmit FIFO being flushed and locked out (<em>I2Cn_INTFL0</em>.<em>tx_lockout</em> = 1).
        <div style="margin-left: 20px;">
        0: Received NACK at the end of a slave transmit operation enabled<br>
        1: Received NACK at the end of a slave transmit operation disabled.</div>
        <em>Note: upon entering transmit preload mode, the hardware automatically sets this bit to 0
        </em>
        <p><em>The software can subsequently set to any value desired (i.e., The hardware does not continuously force the bitfield to this value).</em></p>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>rd_addr_flush_dis</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Slave Address Match Read Auto Flush Disable</strong><br>
        Various situations or conditions are described in this user guide that leads to the transmit FIFO being flushed and locked out (<em>I2Cn_INTFL0</em>.<em>tx_lockout</em> = 1).
        <div style="margin-left: 20px;">
        0: Enabled.<br>
        1: Disabled.
        <p><em>Note: upon entering transmit preload mode, the hardware automatically sets this bit to 1 </em></p>
        </div>
        <p><em>The software can subsequently set to any value desired (i.e., The hardware does not continuously force the bitfield to this value).</em></p>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>wr_addr_flush_dis</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Slave Address Match Write Auto Flush Disable</strong><br>
        Various situations or conditions are described in this user guide that leads to the transmit FIFO being flushed and locked out (<em>I2Cn_INTFL0</em>.<em>tx_lockout</em> = 1).
        <div style="margin-left: 20px;">
        0: Enabled<br>
        1: Disabled.
        </div>
        <p><em>Note: upon entering transmit preload mode, the hardware automatically sets this bit to 1</em></p>
        <p><em>The software can subsequently set to any value desired (i.e., The hardware does not continuously force the bitfield to this value).</em></p></td>
    </tr>
    <tr>
    <td>2</td>
        <td>gc_addr_flush_dis</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO General Call Address Match Auto Flush Disable</strong> <br>
        Various situations or conditions are described in this user guide that leads to the transmit FIFO being flushed and locked out (<em>I2Cn_INTFL0</em>.<em>tx_lockout</em> = 1).
        <div style="margin-left: 20px;">
        0: Enabled.<br>
        1: Disabled.
        </div>
        <p><em>Note: upon entering transmit preload mode, the hardware automatically sets this bit to 1</em></p>
        <p><em>The software can subsequently set to any value desired (i.e., The hardware does not continuously force the bitfield to this value).</em></p></td>
    </tr>
    <tr>
        <td>1</td>
        <td>tx_ready_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Ready Manual Mode</strong><br>
        <div style="margin-left: 20px;">
        0: The hardware controls the <em>I2Cn_TXCTRL1</em>.<em>preload_rdy</em> field.<br>
        1: The software control of the <em>I2Cn_TXCTRL1</em>.<em>preload_rdy</em> field.
        </div>
        </td>
        </tr>
    <tr>
        <td>0</td>
        <td>preload_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Preload Mode Enable</strong><br>
        <div style="margin-left: 20px;">
        0: Normal operation. An address match in slave mode, or a general call address match, flushes and locks the transmit FIFO so it cannot be written and sets the <em>I2Cn_INTFL0.tx_lockout</em> field to 1.<br>
        1: transmit FIFO preload mode. An address match in slave mode, or a general call address match, does not lock the transmit FIFO and does not set <em>I2Cn_INTFL0</em>.<em>tx_lockout</em>. This allows the software to preload data into the transmit FIFO. The status of the I<sup>2</sup>C is controllable at <em>I2Cn_TXCTRL1</em>.<em>preload_rdy.</em></div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-16: I2C Transmit Control 1 Register*
<a name="i2c-transmit-control-1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Transmit Control Register 1</td>
        <td colspan="1">I2Cn_TXCTRL1</td>
        <td>[0x0028]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:12</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>11:8</td>
        <td>lvl</td>
        <td>R</td>
        <td>0</td>
        <td><strong>Transmit FIFO Byte Count Status</strong>
        <div style="margin-left: 20px;">
        0: 0 bytes (No data)<br>
        1: 1 byte<br>
        2: 2 bytes<br>
        3: 3 bytes<br>
        4: 4 bytes<br>
        5: 5 bytes<br>
        6: 6 bytes<br>
        7: 7 bytes<br>
        8: 8 bytes (max value)</div>
        </td>
    </tr>
    <tr>
        <td>7:1</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>preload_rdy</td>
        <td>R/W1O</td>
        <td>1</td>
        <td><strong>Transmit FIFO Preload Ready Status</strong><br>
        When transmit FIFO preload mode is enabled, <em>I2Cn_TXCTRL0</em>.<em>preload_mode</em> = 1, this bit is automatically cleared to 0. While this bit is 0, if the hardware receives a slave address match, a NACK is sent. Once the hardware is ready, the software must set this bit to 1, so the hardware sends an ACK on a slave address match. See <em>Transmit FIFO Preloading</em> for additional details.</p>
        <p>When transmit FIFO preload mode is disabled, <em>I2Cn_TXCTRL0</em>.<em>preload_mode</em> = 1, this bit is forced to 1, and the hardware behaves normally.</p></td>
    </tr>
</tbody>
</table>

*Table 14-17: I2C Data Register*
<a name="i2c-transmit-and-receive-fifo-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Data</td>
        <td colspan="1">I2Cn_FIFO</td>
        <td>[0x002C]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
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
    <td>data</td>
    <td>R/W</td>
    <td>0xFF</td>
    <td><strong>FIFO Data</strong> <br> Reads from this register pop data off the receive FIFO. Writes to this register push data onto the transmit FIFO. Reading from an empty receive FIFO returns 0xFF. Writes to a full transmit FIFO are ignored.</p>
    </td>
    </tr>
</tbody>
</table>

*Table 14-18: I2C Master Control Register*
<a name="i2c-master-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Master Control</td>
        <td colspan="1">I2Cn_MSTCTRL</td>
        <td>[0x0030]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:11</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>10:8</td>
        <td>mcode</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>MCODE</strong><br>
        This field sets the master code used in Hs-Mode operation
        </td>
    </tr>
    <tr>
        <td>7</td>
        <td>ex_addr_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Extended Addressing Enable</strong>
        <div style="margin-left: 20px;">
        0: Send a 7-bit address to the slave.<br>
        1: Send a 10-bit address to the slave.</div>
        </td>
    </tr>
    <tr>
        <td>6:3</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>2</td>
        <td>stop</td>
        <td>R/W1O</td>
        <td>0</td>
        <td><strong>Send STOP Condition</strong>
        <div style="margin-left: 20px;">
        1: Send a STOP Condition at the end of the current transaction</div>
        <p><em>Note: This bit is automatically cleared by the hardware when the STOP condition begins.</em></p>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>restart</td>
        <td>R/W1O</td>
        <td>0</td>
        <td><strong>Send Repeated START Condition</strong><br>
        After sending data to a slave, the master may send another START to retain control of the bus.
        <div style="margin-left: 20px;">
        1: Send a repeated START condition to the slave instead of sending a STOP condition at the end of the current transaction.</div>
        <p><em>Note: This bit is automatically cleared by the hardware when the repeated START condition begins.</em></p></td>
    </tr>
    <tr>
        <td>0</td>
        <td>start</td>
        <td>R/W1O</td>
        <td>0</td>
        <td><strong>Start Master Mode Transfer</strong><br>
        <div style="margin-left: 20px;">
        1: Start master mode transfer</div>
        <p><em>Note: This bit is automatically cleared by the hardware when the transfer is completed or aborted.</em></p></td>
    </tr>
</tbody>
</table>

*Table 14-19: I2C SCL Low Control Register*
<a name="i2c-clock-low-time-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Clock Low Control</td>
        <td colspan="1">I2Cn_CLKLO</td>
        <td>[0x0034]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:9</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>8:0</td>
        <td>lo</td>
        <td>R/W</td>
        <td>0x001</td>
        <td><strong>Clock Low Time</strong><br> In master mode, this configures the SCL low time.
        <div style="margin-left: 20px;">
        <span
        class="math display"><em>t</em><sub><em>S</em><em>C</em><em>L</em>_<em>L</em><em>O</em></sub> = <em>f</em><sub><em>I</em>2<em>C</em>_<em>C</em><em>L</em><em>K</em></sub> × (<em>l</em><em>o</em> + 1)</span>
        </div>
        <p><em>Note: 0 is not a valid setting for this field.</em></p>
        </td>
    </tr>
</tbody>
</table>

*Table 14-20: I2C SCL High Control Register*
<a name="i2c-clock-high-time-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Clock High Control</td>
        <td colspan="1">I2Cn_CLKHI</td>
        <td>[0x0038]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:9</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>8:0</td>
        <td>hi</td>
        <td>R/W</td>
        <td>0x001</td>
        <td><strong>Clock High Time</strong> <br> In master mode, this configures the SCL high time.
        <div style="margin-left: 20px;">
        <p><span class="math display">$$t_{SCL\text{_}HI} =
        \frac{1}{f_{I2C\text{_}CLK}} \times (hi + 1)$$</span></div>
        <p>In both master and slave mode, this configures the time SCL is held low after new data is loaded from the transmit FIFO or after the software clears <em>I2Cn_INTFL0</em>.<em>irxm</em> during IRXM.</p>
        <p><em>Note: 0 is not a valid setting for this field.</em></p></td>
    </tr>
</tbody>
</table>

*Table 14-21: I2C Hs-Mode Clock Control Register*
<a name="i2c-hs-mode-clock-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Hs-Mode Clock Control</td>
        <td colspan="1">I2Cn_HSCLK</td>
        <td>[0x003C]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:16</td>
        <td>-</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
    <td>15:8</td>
    <td>hi</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Hs-Mode Clock High Time</strong><br> This field sets the Hs-Mode clock high count. In slave mode, this is the time SCL is held high after data is output on SDA.
    <p><em>Note: See SCL Clock Generation for Hs-Mode for details on the requirements for the Hs-Mode clock high and low times.</em></p></td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>lo</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Hs-Mode Clock Low Time</strong><br> This field sets the Hs-Mode clock low count. In slave mode, this is the time SCL is held low after data is output on SDA.
        <p><em>Note: See SCL Clock Generation for Hs-Mode for details on the requirements for the Hs-Mode clock high and low times.</em></p></td>
    </tr>
</tbody>
</table>

*Table 14-22: I2C Timeout Register*
<a name="i2c-timeout-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Timeout</td>
        <td colspan="1">I2Cn_TIMEOUT</td>
        <td>[0x0040]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:16</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>15:0</td>
        <td>scl_to_val</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Bus Error SCL Timeout Period</strong> <br> Set this value to the number of I<sup>2</sup>C clock cycles desired to cause a bus timeout error. The peripheral timeout timer starts when it pulls SCL low. After the peripheral releases the line, if the line is not pulled high before the timeout number of I<sup>2</sup>C clock cycles, a bus error condition is set (<em>I2Cn_INTFL0.to_err</em> = 1), and the peripheral releases the
        SCL and SDA lines
        <div style="margin-left: 20px;">
        0: Timeout disabled.<br>
        All other values result in a timeout calculation of:<br>
        <p><span class="math display">$$t_{BUS\text{_}TIMEOUT} =
        \frac{1}{f_{I2C\text{_}CLK}} \times
        scl\text{_}to\text{_}val$$</span>
        </div>
        <p><em>Note: The timeout counter monitors the I<sup>2</sup>C peripheral's driving of the SCL pin, not an external I<sup>2</sup>C device driving the SCL pin.</em></p></td>
    </tr>
</tbody>
</table>

*Table 14-23: I2C DMA Register*
<a name="i2c-dma-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C DMA</td>
        <td colspan="1">I2Cn_DMA</td>
        <td>[0x0048]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
<tbody>
    <tr>
        <td>31:2</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>1</td>
        <td>rx_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive DMA Channel Enable</strong>
        <div style="margin-left: 20px;">
        0: Disable<br>
        1: Enable</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>tx_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit DMA Channel Enable</strong>
        <div style="margin-left: 20px;">
        0: Disable<br>
        1: Enable </div>
        </td>
    </tr>
</tbody>
</table>

*Table 14-24: I2C Slave Address Register*
<a name="i2c-slave-address-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">I2C Slave Address</td>
        <td colspan="1">I2Cn_SLAVE</td>
        <td>[0x004C]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Name</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
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
        <td>ext_addr_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Extended Address Length Select</strong>
        <div style="margin-left: 20px;">
        0: 7-bit addressing<br>
        1: 10-bit addressing</div>
        </td>
    </tr>
    <tr>
        <td>14:10</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>9:0</td>
        <td>addr</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Slave Mode Slave Address</strong> <br>
        In slave mode operation (<em>I2Cn_CTRL.mstr</em> = 0), set this field to the slave address for the I<sup>2</sup>C port. For 7-bit addressing, the address occupies the least significant 7 bits. For 10-bit addressing, the 9-bits of address occupies the most significant 9 bit,
        and the R/W bit occupies the least significant bit.
        <p><em>Note: I2Cn_SLAVE.ext_addr_en controls if this field is a 7-bit or 10-bit address.</em></p></td>
    </tr>
</tbody>
</table>