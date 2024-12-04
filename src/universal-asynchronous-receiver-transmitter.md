The universal asynchronous receiver/transmitter (UART) and the low-power universal asynchronous receiver/transmitter (LPUART) interfaces communicate with external devices using industry-standard serial communications protocols. The UARTs are full-duplex serial ports. Each UART instance is independently configurable unless using a shared external clock source.

The LPUART is a special version of the peripheral that can receive characters at up to 9600 baud while in low-power modes. Hardware loads valid received characters into the receive FIFO and wakes the device when an enabled interrupt condition occurs.

The peripheral provides the following features:

- Flexible baud rate generation up to 12.5Mbps for UART
- Programmable character size of 5-bits to 8-bits
- Stop bit settings of 1, 1.5, or 2-bits
- Parity settings of even, odd, mark (always 1), space (always 0), and no parity
- Automatic parity error detection with selectable parity bias
- Automatic frame error detection.
- Separate 8-byte transmit and receive FIFOs.
- Flexible interrupt conditions.
- Hardware flow control (HFC) using ready-to-send (RTS) and clear-to-send (CTS) pins.
- Separate DMA channels for transmit and receive.
    - DMA support is available in ACTIVE and SLEEP.

The LPUART instance provides these additional features:

- Baud rate support for up to 1.85Mbps in ACTIVE
- Receive characters in SLEEP, LPM, and UPM at up to 9600 baud.
- Fractional baud rate divisor improves baud rate accuracy for 9600 and lower baud rates.
- Wake up from low-power modes to ACTIVE on multiple receive FIFO conditions.

[Figure 12-1](#figure12-1) shows a high-level diagram of the UART peripheral.

*Figure 12-1: UART Block Diagram*
<a name="figure12-1"></a>

![Figure 12-1](assets/images/figure12-1.svg)

*Note: See [Table 12-1](#table12-1-max78000-uart-lpuart-instances) for the clock options supported by each UART instance.*

## Instances
Instances of the peripheral are shown in [Table 12-1](#table12-1-max78000-uart-lpuart-instances). The standard UARTs and the LPUARTs are functionally similar; they are referred to as UART for common functionality. The LPUART instance supports fractional division mode (FDM) and is referenced as LPUART for feature-specific options.

*Table 12-1: MAX78000 UART/LPUART Instances*
<a name="table12-1-max78000-uart-lpuart-instances"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th rowspan="2">Instance</th>
        <th rowspan="2">Register Access Name</th>
        <th rowspan="2">LPUART</th>
        <th rowspan="2">Power Modes</th>
        <th colspan="4" align="center">Clock Option</th>
        <th rowspan="2">Hardware Flow Control</th>
        <th rowspan="2">Transmit FIFO Depth</th>
        <th rowspan="2">Receive FIFO Depth</th>
    </tr>
    <tr>
        <th>0</th>
        <th>1</th>
        <th>2</th>
        <th>3</th>
    </tr>
    </thead>
<tbody>
    <tr>
        <td>UART0</td>
        <td>UART0</td>
        <td rowspan="3">No</td>
        <td rowspan="3"><em>ACTIVE SLEEP</em></td>
        <td rowspan="3">PCLK</td>
        <td rowspan="3">-</td>
        <td rowspan="3">IBRO</td>
        <td rowspan="3">-</td>
        <td rowspan="3">Yes</td>
        <td rowspan="4">8</td>
        <td rowspan="4">8</td>
    </tr>
    <tr>
        <td>UART1</td>
        <td>UART1</td>
    </tr>
    <tr>
        <td>UART2</td>
        <td>UART2</td>
    </tr>
    <tr>
        <td>LPUART0</td>
        <td>UART3</td>
        <td>Yes</td>
        <td>
        <em>ACTIVE</em><br>
        <em>SLEEP</em><br>
        <em>LPM</em><br>
        <em>UPM</em>
        </td>
        <td>-</td>
        <td>-</td>
        <td>IBRO</td>
        <td>ERTCO</td>
        <td>No</td>
    </tr>
</tbody>
</table>

## DMA
Each UART instance supports DMA for both transmit and receive; separate DMA channels can be connected to the receive and transmit FIFOs.

The UART DMA channels are configured using the UART DMA configuration register, [UARTn_DMA](#uart-dma-control-register). Enable the receive FIFO DMA channel by setting [UARTn_DMA](#uart-dma-control-register).*rx_en* to 1 and enable the transmit FIFO DMA channel by setting [UARTn_DMA](#uart-dma-control-register).*tx_en* to 1. DMA transfers are automatically triggered by the hardware based on the number of bytes in the receive FIFO and transmit FIFO.

When DMA is enabled, the following describes the behavior of the DMA requests:

- A receive DMA request is asserted when the number of bytes in the receive FIFO transitions to be greater than or equal to the receive FIFO threshold.
- A transmit DMA request is asserted when the number of bytes in the transmit FIFO transitions to be less than the transmit FIFO threshold.

## UART Frame
[Figure 12-2](#figure12-2) shows the UART frame structure. Character sizes of 5 to 8 bits are configurable through the [UARTn_CTRL](#uart-control-register).*char_size* field. Stop bits are configurable as 1 or 1.5 bits for 5-character frames and 1 or 2 stop bits for 6, 7, or 8-character frames. Parity support includes even, odd, mark, space, and none.

*Figure 12-2: UART Frame Structure*
<a name="figure12-2"></a>

![Figure 12-2](assets/images/figure12-2.svg)

## FIFOs
Separate receive and transmit FIFOs are provided. The FIFOs are both accessed through the same [UARTn_FIFO](#uart-fifo-data-register).*data* field. The current level of the transmit FIFO is read from the [UARTn_STATUS](#uart-status-register).*tx_lvl* field. The current level of the receive FIFO is read from the [UARTn_STATUS](#uart-status-register).*rx_lvl* field. Data for character sizes less than 7 bits are right justified.

### Transmit FIFO Operation
Writing data to the UARTn_FIFO.data field increments the transmit FIFO pointer, [UARTn_STATUS](#uart-status-register).*tx_lvl*, and loads the data into the transmit FIFO. The [UARTn_TXPEEK](#uart-transmit-fifo).*data* register provides a feature that allows the software to "peek" at the current value of the write-only transmit FIFO without changing the [UARTn_STATUS](#uart-status-register).*tx_lvl*. Writes to the transmit FIFO are ignored while [UARTn_STATUS](#uart-status-register).*tx_lvl* = C_TX_FIFO_DEPTH.

### Receive FIFO Operation
Reads of the UARTn_FIFO.data field return the character values in the receive FIFO and decrement the [UARTn_STATUS](#uart-status-register).*rx_lvl*. An overrun event occurs if a valid frame, including parity, is detected while [UARTn_STATUS](#uart-status-register).*rx_lvl* = C_RX_FIFO_DEPTH. When an overrun event occurs, the data is discarded by hardware.

A parity error event indicates that the value read from [UARTn_FIFO](#uart-fifo-data-register).*data* contains a parity error.

### Flushing
The FIFOs are flushed on the following conditions:

- Setting the [UARTn_CTRL](#uart-control-register).*rx_flush* field to 1 flushes the receive FIFO by setting its pointer to 0.
- Setting the [UARTn_CTRL](#uart-control-register).*tx_flush* field to 1 flushes the transmit FIFO by setting its pointer to 0.
- Flush the FIFOs by setting the [GCR_RST0](system-power-clocks-reset.md#reset-register0).*uart0*, [GCR_RST0](system-power-clocks-reset.md#reset-register0).*uart1*, or [GCR_RST0](system-power-clocks-reset.md#reset-register0).*uart2* field to 1.

## Interrupt Events
The peripheral generates interrupts for the events shown in [Table 12-2](#table12-2-max78000-interrupt-events). Unless noted otherwise, each instance has its own set of interrupts and higher-level flag and enable fields, as shown in [Table 12-2](#table12-2-max78000-interrupt-events).

*Figure 12-3: UART Interrupt Functional Diagram*
<a name="figure12-3"></a>

![Figure 12-3](assets/images/figure12-3.svg)

Some activities may cause more than one event, setting one or more event flags. An event interrupt occurs if the corresponding interrupt enable is set. The interrupt flags, when set, must be cleared by the software by writing 1 to the corresponding interrupt flag field.

*Table 12-2: MAX78000 Interrupt Events*
<a name="table12-2-max78000-interrupt-events"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <thead>
        <tr>
        <th><strong>Event</th>
        <th><strong>Interrupt Flag</th>
        <th><strong>Interrupt Enable</th>
        </tr>
    </thead>
<tbody>
    <tr>
    <td>Frame Error</td>
    <td><a href="#uart-interrupt-flag-register">UARTn_INT_FL</a>.<em>rx_ferr</em></td>
    <td><a href="#uart-interrupt-enable-register">UARTn_INT_EN</a>.<em>rx_ferr</em></td>
    </tr>
    <tr>
    <td>Parity Error</td>
    <td><a href="#uart-interrupt-flag-register">UARTn_INT_FL</a>.<em>rx_par</em></td>
    <td><a href="#uart-interrupt-enable-register">UARTn_INT_EN</a>.<em>rx_par</em></td>
    </tr>
    <tr>
    <td>CTS Signal Change</td>
    <td><a href="#uart-interrupt-flag-register">UARTn_INT_FL</a>.<em>cts_ev</em></td>
    <td><a href="#uart-interrupt-enable-register">UARTn_INT_EN</a>.<em>cts_ev</em></td>
    </tr>
    <tr>
    <td>Receive FIFO Overrun</td>
    <td><a href="#uart-interrupt-flag-register">UARTn_INT_FL</a>.<em>rx_ov</em></td>
    <td><a href="#uart-interrupt-enable-register">UARTn_INT_EN</a>.<em>rx_ov</em></td>
    </tr>
    <tr>
    <td>Receive FIFO Threshold</td>
    <td><a href="#uart-interrupt-flag-register">UARTn_INT_FL</a>.<em>rx_thd</em></td>
    <td><a href="#uart-interrupt-enable-register">UARTn_INT_EN</a>.<em>rx_thd</em></td>
    </tr>
    <tr>
    <td>Transmit FIFO Half-Empty</td>
    <td><a href="#uart-interrupt-flag-register">UARTn_INT_FL</a>.<em>tx_he</em></td>
    <td><a href="#uart-interrupt-enable-register">UARTn_INT_EN</a>.<em>tx_he</em></td>
    </tr>
</tbody>
</table>

### Frame Error
A frame error is generated when the UART sampling circuitry detects an invalid bit. As shown in [Figure 12-4](#figure12-4), each bit is sampled three times and can generate a frame error on the start bit, stop bit, data bits, and optionally the parity bit. When a frame error occurs, the data is discarded.

The frame error criteria are different based on the following:

- Standard UART and LPUART with FDM disabled:

    - The start bit is sampled 3 times, and all samples must be 0, or a frame error is generated.
    - Each data bit is sampled, and 2 of the 3 samples must match, or a frame error is generated.
    - If parity is enabled, the parity bit is sampled 3 times, and all samples must match, or a frame error is generated.
    - The stop bit is sampled 3 times, and all samples must be 1, or a frame error is generated.
    - See [Table 12-3](#table12-3-frame-error-detection-for-standard-uarts-and-lpuart) for details.

- LPUART with FDM enabled ([UARTn_CTRL](#uart-control-register).*fdm* = 1) and data/parity edge detect enabled ([UARTn_CTRL](#uart-control-register).*dpfe_en* = 1):

    - The start bit is sampled 3 times, and all samples must be 0, or a frame error is generated.
    - Each data bit is sampled 3 times, and all samples must match, or a frame error is generated.
    - If parity is enabled, the parity bit is sampled 3 times, and all samples must match, or a frame error is generated.
    - The stop bit is sampled 3 times, and all samples must be 1, or a frame error is generated.
    - See [Table 12-4](#table12-4-frame-error-detection-for-lpuarts) for details.

*Table 12-3: Frame Error Detection for Standard UARTs and LPUART*
<a name="table12-3-frame-error-detection-for-standard-uarts-and-lpuart"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <thead>
        <tr>
        <th><a href="#uart-control-register">UARTn_CTRL</a>.<em>par_en</em></th>
        <th><a href="#uart-control-register">UARTn_CTRL</a>.<em>par_md</em></th>
        <th><a href="#uart-control-register">UARTn_CTRL</a>.<em>par_eo</em></th>
        <th style="text-align: center; vertical-align: middle;">Start Samples</th>
        <th style="text-align: center; vertical-align: middle;">Data Samples</th>
        <th style="text-align: center; vertical-align: middle;">Parity Samples</th>
        <th style="text-align: center; vertical-align: middle;">Stop Samples</th>
        </tr>
    </thead>
<tbody>
    <tr>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td style="text-align: center; vertical-align: middle;">N/A</td>
        <td style="text-align: center; vertical-align: middle;">N/A</td>
        <td rowspan="5" style="text-align: center; vertical-align: middle;">3 of 3 must be 0</td>
        <td rowspan="5" style="text-align: center; vertical-align: middle;">2/3 must match</td>
        <td style="text-align: center; vertical-align: middle;">Not Present</td>
        <td rowspan="5" style="text-align: center; vertical-align: middle;">3 of 3 must be 1</td>
    </tr>
    <tr>
        <td rowspan="4" style="text-align: center; vertical-align: middle;">1</td>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td>
            3/3 = 1 if even number "1"<br>
            3/3 = 0 if odd number "0"
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td>
            3/3 = 1 if odd number "1"<br>
            3/3 = 0 if even number "0"<br>
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td>
            3/3 = 1 if even number "0"<br>
            3/3 = 0 if odd number "1"
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td>
            3/3 = 1 if odd number "0"<br>
            3/3 = 0 if even number "1"
        </td>
    </tr>
</tbody>
</table>

*Table 12-4: Frame Error Detection for LPUARTs with UARTn_CTRL.fdm = 1 and UARTn_CTRL.dpfe_en = 1*
<a name="table12-4-frame-error-detection-for-lpuarts"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th><a href="#uart-control-register">UARTn_CTRL</a>.<em>par_en</em></th>
        <th><a href="#uart-control-register">UARTn_CTRL</a>.<em>par_md</em></th>
        <th><a href="#uart-control-register">UARTn_CTRL</a>.<em>par_eo</em></th>
        <th style="text-align: center; vertical-align: middle;">Start Samples</th>
        <th style="text-align: center; vertical-align: middle;">Data Samples</th>
        <th style="text-align: center; vertical-align: middle;">Parity Samples</th>
        <th style="text-align: center; vertical-align: middle;">Stop Samples</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>0</td>
        <td style="text-align: center; vertical-align: middle;">N/A</td>
        <td style="text-align: center; vertical-align: middle;">N/A</td>
        <td rowspan="5" style="text-align: center; vertical-align: middle;">3 of 3 must be 0</td>
        <td rowspan="5" style="text-align: center; vertical-align: middle;">3 of 3 must match</td>
        <td style="text-align: center; vertical-align: middle;">Not Present</td>
        <td rowspan="5" style="text-align: center; vertical-align: middle;">3 of 3 must be 1</td>
    </tr>
    <tr>
        <td rowspan="4" style="text-align: center; vertical-align: middle;">1</td>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td>
            3 of 3 = 1 if even number of 1s<br>
            3 of 3 = 0 if odd number 0s
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td>
            3 of 3 = 1 if odd number 1s<br>
            3 of 3 = 0 if even number 0s
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td style="text-align: center; vertical-align: middle;">0</td>
        <td>
            3 of 3 = 1 if even number 0s<br>
            3 of 3 = 0 if odd number 1s
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td style="text-align: center; vertical-align: middle;">1</td>
        <td>
            3 of 3 = 1 if odd number 0s<br>
            3 of 3 = 0 if even number 1s
        </td>
    </tr>
</tbody>
</table>


### Parity Error
Set [UARTn_CTRL](#uart-control-register).*par_en* = 0 to enable parity checking of the received frame. If the calculated parity does not match the parity bit, then the corresponding interrupt flag is set. The data received is saved to the receive FIFO when a parity error occurs.

### CTS Signal Change
A CTS signal change condition occurs if HFC is enabled, the UART baud clock is enabled, and the CTS pin changes state.

### Overrun
An overrun condition occurs if a valid frame is received when the receive FIFO is full. The interrupt flag is set at the end of the stop bit, and the frame is discarded.

### Receive FIFO Threshold
A receive FIFO threshold event occurs when a valid frame is received that causes the number of bytes to exceed the configured receive FIFO threshold [UARTn_CTRL](#uart-control-register).*rx_thd_val*.

### Transmit FIFO Half-Empty
The transmit FIFO half-empty event occurs when [UARTn_STATUS](#uart-status-register).*tx_lvl* transitions from more than half-full to half-empty, as shown in [Equation 12-1](#equation12-1).

*Note: When this condition occurs, verify the number of bytes in the transmit FIFO ([UARTn_STATUS](#uart-status-register).tx_lvl) before re-filling.*

*Equation 12-1: UART Transmit FIFO Half-Empty Condition*
<a name="equation12-1"></a>

$$
\left(\frac{C{\_}TX{\_}FIFO{\_}DEPTH}{2} + 1 \right)\ \overset{Transistions\ from}{\rightarrow}\ \left(\frac{C{\_}TX{\_}FIFO{\_}DEPTH}{2} \right)
$$

## LPUART Wakeup Events
LPUART instances can receive characters while in the low-power modes listed in [Table 12-1](#table12-1-max78000-uart-lpuart-instances). If enabled, each of the receive FIFO conditions shown in [Table 12-5](#table12-5-max78000-wakeup-events) wakes the device, exits the low-power mode, and returns the device to ACTIVE.

Unlike interrupts, wake-up activity is based on a condition, not an event. As long as the condition is true and the wake-up enable field is set to 1, the wake-up flag remains set.

*Table 12-5: MAX78000 Wakeup Events*
<a name="table12-5-max78000-wakeup-events"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
    <th style="text-align: center; vertical-align: middle;">Receive FIFO Condition</th>
    <th style="text-align: center; vertical-align: middle;">
        Wake-Up Flag<br>
        <em><a href="#uart-wakeup-interrupt-flag-register">UARTn_WKFL</a></em>
    </th>
    <th style="text-align: center; vertical-align: middle;">   
        Wake-Up Enable<br>
        <em><a href="#uart-wakeup-interrupt-enable-register">UARTn_WKEN</a></em>
    </th>
    <th style="text-align: center; vertical-align: middle;">Low-Power Peripheral <br> Wake-Up Flag</th>
    <th style="text-align: center; vertical-align: middle;">Low-Power Peripheral <br> Wake-Up Enable</th>
    <th style="text-align: center; vertical-align: middle;">Low-Power <br> Clock Disable</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td style="text-align: center; vertical-align: middle;">Threshold</td>
        <td style="text-align: center; vertical-align: middle;"><em>rx_thd</em></td>
        <td style="text-align: center; vertical-align: middle;"><em>rx_thd</em></td>
        <td rowspan="3" style="text-align: center; vertical-align: middle;"><em><a href="../system-power-clocks-reset/#low-power-peripheral-wakeup-status-register">PWRSEQ_LPPWST</a></em>.<em>uart3</em></td>
        <td rowspan="3" style="text-align: center; vertical-align: middle;"><em><a href="../system-power-clocks-reset.md/#low-power-peripheral-wakeup-enable-register">PWRSEQ_LPPWEN</a></em>.<em>uart3</em></td>
        <td rowspan="3" style="text-align: center; vertical-align: middle;"><em><a href="../system-power-clocks-reset.md/#clock-control-register">LPGCR_PCLKDIS</a></em>.<em>uart3</em></td>
    </tr>
    <tr>
    <td style="text-align: center; vertical-align: middle;">Full</td>
    <td style="text-align: center; vertical-align: middle;"><em>rx_full</em></td>
    <td style="text-align: center; vertical-align: middle;"><em>rx_full</em></td>
    </tr>
    <tr>
    <td style="text-align: center; vertical-align: middle;">Not Empty</td>
    <td style="text-align: center; vertical-align: middle;"><em>rx_ne</em></td>
    <td style="text-align: center; vertical-align: middle;"><em>rx_ne</em></td>
    </tr>
</tbody>
</table>

### Receive FIFO Threshold
This condition persists while [UARTn_STATUS](#uart-status-register).*rx_lvl* ≥ [UARTn_CTRL](#uart-control-register).*rx_thd_val*.

### Receive FIFO Full
This condition persists while [UARTn_STATUS](#uart-status-register).*rx_lvl* ≥ C_RX_FIFO_DEPTH.

### Receive Not Empty
This condition persists while [UARTn_STATUS](#uart-status-register).*rx_lvl* > 0.

## Inactive State
The following conditions result in the UART being inactive:

- When [UARTn_CTRL](#uart-control-register).*bclken* = 0
- After setting [UARTn_CTRL](#uart-control-register).*bclken* to 1 until [UARTn_CTRL](#uart-control-register).*bclkrdy* = 1
- Any write to the [UARTn_CLKDIV](#uart-clock-divisor-register).*clkdiv* field while [UARTn_CTRL](#uart-control-register).*bclken* = 1
- Any write to the [UARTn_OSR](#uart-oversampling-control-register).*osr* field when [UARTn_CTRL](#uart-control-register).*bclken* = 1

## Receive Sampling
Each bit of a frame is oversampled to improve noise immunity. The oversampling rate (OSR) is configurable with the [UARTn_OSR](#uart-oversampling-control-register).*osr* field. In most cases, the bit is evaluated based on three samples at the midpoint of each bit time, as shown in Figure 12-4.

*Figure 12-4: Oversampling Example*
<a name="figure12-4"></a>

![Figure 12-4](assets/images/figure12-4.svg)

Whenever [UARTn_CLKDIV](#uart-clock-divisor-register).*clkdiv* < 0x10 (i.e., division rate less than 8.0), OSR is not used, and the oversampling rate is adjusted to full sampling by the hardware. In full sampling, the receive input is sampled on every clock cycle regardless of the OSR setting.

Note: For 9600 baud low-power operation, the dual-edge sampling mode must be enabled ([UARTn_CTRL](#uart-clock-divisor-register).*desm* = 1).

## Baud Rate Generation
The baud rate is determined by the selected UART clock source and the value of the clock divisor. Multiple clock sources are available for each UART instance. See [Table 12-1](#table12-1-max78000-uart-lpuart-instances) for available clock sources.

Note: Changing the clock source should only be done between data transfers to avoid corrupting an ongoing data transfer.

### UART Clock Sources
Standard UART instances operate only in ACTIVE and SLEEP. Standard UART instances can only wake the device from SLEEP. Figure 12-5 shows the baud rate generation path for standard UARTs.

*Figure 12-5: UART Baud Rate Generation*
<a name="figure12-5"></a>

![Figure 12-5](assets/images/figure12-5.svg)

### LPUART Clock Sources
LPUART instances support FDM and are configurable for operation at 9600 and lower baud rates for operation in SLEEP, LPM, and UPM. Operation in LPM and UPM requires the use of the ERTCO as the baud rate clock source. The ERTCO can be configured to remain active in LPM and UPM, allowing the LPUART to receive data and serve as a wake-up source while power consumption is at a minimum.

*Figure 12-6: LPUART Timing Generation*
<a name="figure12-6"></a>

![Figure 12-6](assets/images/figure12-6.svg)

### Baud Rate Calculation
The transmit and receive circuits share a common baud rate clock, the selected UART clock source divided by the clock divisor. Instances that support FDM offer a 0.5 fractional clock division when enabled by setting [UARTn_CTRL](#uart-control-register).*fdm* = 1. This allows for greater accuracy when operating at low baud rates and finer granularity for the oversampling rate.

Use the following formula to calculate the [UARTn_CLKDIV](#uart-clock-divisor-register).*clkdiv* value based on the clock source, desired baud rate, and integer or fractional divisor.

*Equation 12-2: UART Clock Divisor Formula*
<a name="equation12-2-uart-clock-divisor-formula"></a>

$$
{UARTn{\_}CTRL.fdm = 0:}
$$

$$
{UARTn{\_}CLKDIV.clkdiv = INT\left\lbrack \frac{UART\ Clock}{Baud\ Rate} \right\rbrack}
$$

*Equation 12-3: LPUART Clock Divisor Formula for UARTn_CTRL.fdm = 1*
<a name="equation12-3"></a>

$$
{UARTn{\_}CTRL.fdm = 1:}
$$

$$
{UARTn{\_}CLKDIV.clkdiv = INT\left\lbrack \frac{UART\ Clock}{Baud\ Rate} \times 2 \right\rbrack}
$$

For example, in a case where the UART clock is 50MHz, and the target baud rate is 115,200 bps:

- *UARTn_CTRL.fdm* = 0, 
    $UARTn{\_}CLKDIV.clkdiv = \left(\frac{50,000000}{115,200} \right) = 434$

- *UARTn_CTRL.fdm* = 1, 
    $UARTn{\_}CLKDIV.clkdiv = \left(\frac{50,000000}{115,200} \right) = 434.03 × 2 = 868$

### Low-Power Mode Operation of LPUARTs for 9600 Baud and Below
LPUART instances can configure the receiver for 9600 and lower baud rates and enable the LPUART in the low-power modes SLEEP, LPM, and UPM. Receipt of a valid frame loads the receive FIFO and increments [UARTn_STATUS](#uart-status-register).*rx_lvl*. If a wake-up event, shown in [Table 12-5](#table12-5-max78000-wakeup-events), is enabled, the device exits the current low-power mode and returns to ACTIVE if the wake-up event occurs. See section [Baud Rate Calculation](#baud-rate-calculation) and [Equation 12-3](#equation12-3) for details on setting the baud rate for LPUART instances with [UARTn_CTRL](#uart-control-register).*fdm* set to 1.

*Table 12-6: LPUART Low Baud Rate Generation Examples ([UARTn_CTRL](#uart-control-register).*fdm* = 1)*
<a name="table12-6-lpuart-low-baud-rate-generation-examples"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th style="text-align: center; vertical-align: middle;">Clock Source</th>
        <th style="text-align: center; vertical-align: middle;">BAUD (bits/s)</th>
        <th style="text-align: center; vertical-align: middle;">Ratio (Clock/BAUD)</th>
        <th style="text-align: center; vertical-align: middle;">
            Calculated<br>
            <em><a href="#uart-clock-divisor-register">UARTn_CLKDIV</a>.clkdiv</em>
        </th>
        <th style="text-align: center; vertical-align: middle;">Error</th>
        <th style="text-align: center; vertical-align: middle;"><em><a href="#uart-oversampling-control-register">UARTn_OSR</a>.osr</em></th>
    </tr>
</thead>
<tbody>
    <tr>
        <td rowspan="6" style="text-align: center; vertical-align: middle;">ERTCO</td>
        <td style="text-align: center; vertical-align: middle;">9,600</td>
        <td style="text-align: center; vertical-align: middle;">3.413</td>
        <td style="text-align: center; vertical-align: middle;">7</td>
        <td style="text-align: center; vertical-align: middle;">-2.5%</td>
        <td style="text-align: center; vertical-align: middle;">N/A (1×)</td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">7,200</td>
        <td style="text-align: center; vertical-align: middle;">4.551</td>
        <td style="text-align: center; vertical-align: middle;">9</td>
        <td style="text-align: center; vertical-align: middle;">+1.1%</td>
        <td style="text-align: center; vertical-align: middle;">N/A (1×)</td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">4,800</td>
        <td style="text-align: center; vertical-align: middle;">6.827</td>
        <td style="text-align: center; vertical-align: middle;">14</td>
        <td style="text-align: center; vertical-align: middle;">-2.5%</td>
        <td style="text-align: center; vertical-align: middle;">N/A (1×)</td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">2,400</td>
        <td style="text-align: center; vertical-align: middle;">13.653</td>
        <td style="text-align: center; vertical-align: middle;">27</td>
        <td style="text-align: center; vertical-align: middle;">+1.1%</td>
        <td style="text-align: center; vertical-align: middle;">
            0: 8×<br>
            1: 12×
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">1,800</td>
        <td style="text-align: center; vertical-align: middle;">18.204</td>
        <td style="text-align: center; vertical-align: middle;">36</td>
        <td style="text-align: center; vertical-align: middle;">+1.1%</td>
        <td style="text-align: center; vertical-align: middle;">
            0: 8×<br>
            1: 12×<br>
            2: 16×
        </td>
    </tr>
    <tr>
        <td style="text-align: center; vertical-align: middle;">1,200</td>
        <td style="text-align: center; vertical-align: middle;">27.307</td>
        <td style="text-align: center; vertical-align: middle;">54</td>
        <td style="text-align: center; vertical-align: middle;">+1.1%</td>
        <td style="text-align: center; vertical-align: middle;">
            0: 8×<br>
            1: 12×<br>
            2: 16×<br>
            3: 20×<br>
            4: 24×
         </td>
    </tr>
</tbody>
</table>

#### Configuring an LPUART for Low-Power Modes of Operation
Use the following procedure to receive characters at 9600 or lower baud rates while in low-power modes:

1. Clear [UARTn_CTRL](#uart-control-register).*bclken* = 0 to disable the baud clock. The hardware immediately clears [UARTn_CTRL](#uart-control-register).*bclkrdy* to 0.
2. Set [PWRSEQ_LPCN](system-power-clocks-reset.md#low-power-control-register).*x32ken*= 1 to ensure the 32kHz clock source remains active in LPM and UPM modes.
3. Ensure [UARTn_CTRL](#uart-control-register).ucagm = 1.
4. Configure [UARTn_CTRL](#uart-control-register).*bclksrc* to select the ERTCO.
5. Set [UARTn_CTRL](#uart-control-register).*fdm* to 1 to enable FDM.
6. Set [UARTn_CLKDIV](#uart-clock-divisor-register).*clkdiv* to the calculated clock divisor shown in [Table 12-6](#table12-6-lpuart-low-baud-rate-generation-examples) for the required baud rate.
7. Set [UARTn_CTRL](#uart-control-register).desm to 1 to enable receive dual-edge sampling mode.
8. Choose the desired wake-up conditions from [Table 12-5](#table12-5-max78000-wakeup-events).
a. Clear any of the wake-up conditions chosen if currently active in the [UARTn_WKFL](#uart-wakeup-interrupt-flag-register) register.
b. Enable the wake-up condition; set the wake-up field to 1 in the [UARTn_WKEN](#uart-wakeup-interrupt-enable-register) register.
9. Set the [UARTn_CTRL](#uart-control-register).*bclken* field to 1 to enable the baud clock.
10. Poll the [UARTn_CTRL](#uart-control-register).*bclkrdy* field until it reads 1.
11. Enter the desired low-power mode.

## Hardware Flow Control
The optional HFC uses two additional pins, CTS and RTS, as a handshaking protocol to manage UART communications. For full-duplex operation, the RTS output pin on the peripheral is connected to the CTS input pin on the external UART, and the CTS input pin on the peripheral is connected to the RTS output pin on the external UART, as shown in [Figure 12-7](#figure12-7).

*Figure 12-7: Hardware Flow Control Physical Connection*
<a name="figure12-7"></a>

![Figure 12-7](assets/images/figure12-7.svg)

In HFC operation, a UART transmitter waits for the external device to assert its CTS pin. When CTS is asserted, the UART transmitter sends data to the external device. The external device keeps CTS asserted until it cannot receive additional data, typically because the external device's receive FIFO is full. The external device then deasserts CTS until the device can receive more data. The external device then asserts CTS again, allowing additional data to be sent.

Hardware flow control can be fully automated by the peripheral hardware or by software through direct monitoring of the CTS input signal and control of the RTS output signal.

### Automated HFC
Setting [UARTn_CTRL](#uart-control-register).*hfc_en* = 1 enables automated HFC. When automated HFC is enabled, the hardware manages the CTS and RTS signals. The deassertion of the RTS signal is configurable using the [UARTn_CTRL](#uart-control-register).*rtsdc* field:

- [UARTn_CTRL](#uart-control-register).*rtsdc* = 0: Deassert RTS when [UARTn_STATUS](#uart-status-register).*rx_lvl* = C_RX_FIFO_DEPTH
- [UARTn_CTRL](#uart-control-register).*rtsdc* = 1: Deassert RTS while [UARTn_STATUS](#uart-status-register).*rx_lvl* ≥= [UARTn_CTRL](#uart-control-register).*rx_thd_val*

The transmitter continues to send data as long as the CTS signal is asserted and there is data in the transmit FIFO. If the receiver de-asserts the CTS pin, the transmitter finishes the transmission of the current character and then waits until the CTS pin state is asserted before continuing transmission. [Figure 12-8](#figure12-8) shows the state of the CTS pin during a transmission under automated HFC.

Automated HFC does not generate interrupt events related to the state of the transmit FIFO or the receive FIFO. The software must handle FIFO management. See Interrupt Events for additional information.

*Figure 12-8: Hardware Flow Control Signaling for Transmitting to an External Receiver*
<a name="figure12-8"></a>

![Figure 12-8](assets/images/figure12-8.svg)

### Application Controlled HFC
Application controlled HFC requires the software to manually control the RTS output pin and monitor the CTS input pin. Using application controlled HFC requires the automated HFC to be disabled by setting the [UARTn_CTRL](#uart-control-register).hfc_en field to 1. Additionally, the software should enable CTS sampling ([UARTn_CTRL](#uart-control-register).*cts_dis* = 0) if performing application controlled HFC.

#### RTC/CTS Handling for Application Controlled HFC
The software can manually monitor the CTS pin state by reading the field UARTn_PNR.cts. The software can manually set the state of the RTS output pin and read the current state of the RTS output pin using the field UARTn_PNR.rts. The software must manage the state of the RTS pin when performing application controlled HFC.

Interrupt support for CTS input signal change events is supported even when automated HFC is disabled. The software can enable the CTS interrupt event by setting the [UARTn_INT_EN](#uart-interrupt-enable-register).*cts_ev* field to 1. The CTS signal change interrupt flag is set by the hardware any time the CTS pin state changes. The software must clear this interrupt flag manually by writing 1 to the [UARTn_INT_FL](#uart-interrupt-flag-register).*cts_ev* field.

*Note: CTS pin state monitoring is disabled any time the UART baud clock is disabled ([UARTn_CTRL](#uart-control-register).bclken = 0). The software must enable CTS pin monitoring by setting the field [UARTn_CTRL](#uart-control-register).cts_dis to 0 after enabling the baud clock if CTS pin state monitoring is required.*

## Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. If multiple instances of the peripheral are provided, each instance has its own independent set of the registers shown in [Table 12-7](#table12-7-uart-lpuart-register-summary). Register names for a specific instance are defined by replacing "n" with the instance number. As an example, a register PERIPHERALn_CTRL resolves to PERIPHERAL0_CTRL and PERIPHERAL1_CTRL for instances 0 and 1, respectively.

See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

All registers and fields apply to both UART and LPUART instances unless specified otherwise.

*Table 12-7: UART/LPUART Register Summary*
<a name="table12-7-uart-lpuart-register-summary"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td>Offset</td>
        <td>Register</td>
        <td>Name</td>
    </tr>
    <tr>
        <td>[0x0000]</td>
        <td><a href="#uart-control-register">UARTn_CTRL</a></td>
        <td>UART Control Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#uart-status-register">UARTn_STATUS</a></td>
        <td>UART Status Register</td>
    </tr>
    <tr>
        <td>[0x0008]</td>
        <td><a href="#uart-interrupt-enable-register">UARTn_INT_EN</a></td>
        <td>UART Interrupt Enable Register</td>
    </tr>
    <tr>
        <td>[0x000C]</td>
        <td><a href="#uart-interrupt-flag-register">UARTn_INT_FL</a></td>
        <td>UART Interrupt Flag Register</td>
    </tr>
    <tr>
        <td>[0x0010]</td>
        <td><a href="#uart-clock-divisor-register">UARTn_CLKDIV</a></td>
        <td>UART Clock Divisor Register</td>
    </tr>
    <tr>
        <td>[0x0014]</td>
        <td><a href="#uart-oversampling-control-register">UARTn_OSR</a></td>
        <td>UART Oversampling Control Register</td>
    </tr>
    <tr>
        <td>[0x0018]</td>
        <td><a href="#uart-transmit-fifo">UARTn_TXPEEK</a></td>
        <td>UART Transmit FIFO</td>
    </tr>
    <tr>
        <td>[0x001C]</td>
        <td><a href="#uart-pin-control-register">UARTn_PNR</a></td>
        <td>UART Pin Control Register</td>
    </tr>
    <tr>
        <td>[0x0020]</td>
        <td><a href="#uart-fifo-data-register">UARTn_FIFO</a></td>
        <td>UART FIFO Data Register</td>
    </tr>
    <tr>
        <td>[0x0030]</td>
        <td><a href="#uart-dma-control-register">UARTn_DMA</a></td>
        <td>UART DMA Control Register</td>
    </tr>
    <tr>
        <td>[0x0034]</td>
        <td><a href="#uart-wakeup-interrupt-enable-register">UARTn_WKEN</a></td>
        <td>UART Wakeup Interrupt Enable Register</td>
    </tr>
    <tr>
        <td>[0x0038]</td>
        <td><a href="#uart-wakeup-interrupt-flag-register">UARTn_WKFL</a></td>
        <td>UART Wakeup Interrupt Flag Register</td>
    </tr>
</table>

### Register Details

*Table 12-8: UART Control Register*
<a name="uart-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<tr>
    <td colspan="3">UART Control</td>
    <td colspan="1">UARTn_CTRL</td>
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
    <td>31:23</td>
    <td>-</td>
    <td>DNM</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
</tr>
<tr>
    <td>22</td>
    <td>desm</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Receive Dual Edge Sampling Mode</strong><br>
    LPUART instances only. This field is reserved in standard UART instances.
    <div style="margin-left: 20px">
    0: Sample receive input signal on clock rising edge only <br>
    1: Sample receive input signal on both rising and falling edges
    </div>
    </td>
</tr>
<tr>
    <td>21</td>
    <td>fdm</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Fractional Division Mode</strong><br>
    LPUART instances only. This field is reserved in standard UART instances.
    <div style="margin-left: 20px">
    0: Baud rate divisor is an integer <br>
    1: Baud rate divisor supports 0.5 division resolution
    </div>
    </td>
</tr>
<tr>
    <td>20</td>
    <td>ucagm</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>UART Clock Auto Gating Mode</strong><br>
    <em>Note: Software must set this field to 1 for proper operation.</em>
    <div style="margin-left: 20px">
    0: No gating <br>
    1: UART clock is paused during transmit and receive idle states
    </div>
    </td>
</tr>
<tr>
    <td>19</td>
    <td>bclkrdy</td>
    <td>R</td>
    <td>0</td>
    <td><strong>Baud Clock Ready</strong><br>
    <em>Note: Software must set this field to 1 for proper operation.</em>
    <div style="margin-left: 20px">
    0: No gating <br>
    1: UART clock is paused during transmit and receive idle states
    </div>
    </td>
</tr>
<tr>
    <td>18</td>
    <td>dpfe_en</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Data/Parity Bit Frame Error Detection Enable</strong><br>
    LPUART instances only. This field is reserved in standard UART instances.
    <div style="margin-left: 20px">
    0: Disable. Do not detect frame errors on receive between the start bit and stop bit. <br>
    1: Enable. Detect frame errors when receive changes at the center of a bit time.
    </div>
    </td>
</tr>
<tr>
    <td>17:16</td>
    <td>bclksrc</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Baud Clock Source</strong><br>
    This field selects the baud clock source. See <a href="#table12-1-max78000-uart-lpuart-instances">Table 12-1</a> for available clock options for each UART instance.
    <div style="margin-left: 20px">
    0: Clock option 0 <br>
    1: Clock option 1 <br>
    2: Clock option 2 <br>
    3: Clock option 3
    </div>
    </td>
</tr>
<tr>
    <td>15</td>
    <td>bclken</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Baud Clock Enable</strong><br>
    <div style="margin-left: 20px">
    0: Disabled <br>
    1: Enabled
    </div>
    </td>
</tr>
<tr>
    <td>14</td>
    <td>rtsdc</td>
    <td>R</td>
    <td>0</td>
    <td><strong>Hardware Flow Control RTS Deassert Condition</strong><br>
    <div style="margin-left: 20px">
    0: Deassert RTS when receive FIFO Level = C_RX_FIFO_DEPTH (FIFO full) <br>
    1: Deassert RTS while receive FIFO Level >= <a href="#uart-control-register">UARTn_CTRL</a>.<em>rx_thd_val</em>.
    </div>
    </td>
</tr>
<tr>
    <td>13</td>
    <td>hfc_en</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Hardware Flow Control Enable</strong><br>
    <div style="margin-left: 20px">
    0: Disabled <br>
    1: Enabled
    </div>
    </td>
</tr>
<tr>
    <td>12</td>
    <td>stopbits</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Number of Stop Bits</strong><br>
    <div style="margin-left: 20px">
    0: Disabled <br>
    1: Enabled
    </div>
    </td>
</tr>
<tr>
    <td>11:10</td>
    <td>char_size</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Character Length</strong><br>
    <div style="margin-left: 20px">
    0: 5 bits <br>
    1: 6 bits <br>
    2: 7 bits <br>
    3: 8 bits
    </div>
    </td>
</tr>
<tr>
    <td>9</td>
    <td>rx_flush</td>
    <td>W1</td>
    <td>0</td>
    <td><strong>Receive FIFO Flush</strong><br>
    Write 1 to flush the receive FIFO. This bit always reads 0.
    <div style="margin-left: 20px">
    0: N/A <br>
    1: Flush FIFO
    </div>
    </td>
</tr>
<tr>
    <td>8</td>
    <td>tx_flush</td>
    <td>W1</td>
    <td>0</td>
    <td><strong>Transmit FIFO Flush</strong><br>
    Write 1 to flush the transmit FIFO. This bit always reads 0.
    <div style="margin-left: 20px">
    0: N/A <br>
    1: Flush FIFO
    </div>
    </td>
</tr>
<tr>
    <td>7</td>
    <td>cts_dis</td>
    <td>R/W</td>
    <td>1</td>
    <td><strong>CTS Sampling Disable</strong><br>
        <div style="margin-left: 20px">
        0: Enabled <br>
        1: Disabled
        </div>
    </td>
</tr>
<tr>
    <td>6</td>
    <td>par_md</td>
    <td>R/W</td>
    <td>1</td>
    <td><strong>Parity Value Select</strong><br>
        <div style="margin-left: 20px">
        0: Parity calculation is based on 1 bits. (Mark) <br>
        1: Parity calculation is based on 0 bits. (Space)
        </div>
    </td>
</tr>
<tr>
    <td>5</td>
    <td>par_eo</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Parity Odd/Even Select</strong><br>
        <div style="margin-left: 20px">
        0: Even parity <br>
        1: Odd parity
        </div>
    </td>
</tr>
<tr>
    <td>4</td>
    <td>par_en</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Transmit Parity Generation Enable</strong><br>
        <div style="margin-left: 20px">
        0: Parity transmission disabled. <br>
        1: Parity bit is calculated and transmitted after the last character bit.
        </div>
    </td>
</tr>
<tr>
    <td>3:0</td>
    <td>rx_thd_val</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Receive FIFO Threshold</strong><br>
    Valid settings are from 1 to C_RX_FIFO_DEPTH.
        <div style="margin-left: 20px">
        0: Reserved. <br>
        1: 1. <br>
        2: 2. <br>
        3: 3. <br>
        4: 4. <br>
        5: 5. <br>
        6: 6. <br>
        7: 7. <br>
        8: 8. <br>
        9 - 15: Reserved.
        </div>
    </td>
</tr>
</table>

*Table 12-9: UART Status Register*
<a name="uart-status-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<tr>
    <td colspan="3">UART Status</td>
    <td colspan="1">UARTn_STATUS</td>
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
    <td>31:16</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
</tr>
    <tr>
        <td>15:12</td>
        <td>tx_lvl</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Transmit FIFO Level</strong><br>
        This field returns the number of characters in the transmit FIFO.
        <div style="margin-left: 20px">
        0 - 8: Number of bytes in the transmit FIFO<br>
        9 - 15: Reserved for Future Use
        </td>
    </tr>
    <tr>
        <td>11:8</td>
        <td>rx_lvl</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Receive FIFO Level</strong><br>
        This field returns the number of characters in the Receive FIFO.
        <div style="margin-left: 20px">
        0 - 8: Number of bytes in the receive FIFO<br>
        9 - 15: Reserved for Future Use
        </td>
    </tr>
    <tr>
        <td>7</td>
        <td>tx_full</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Transmit FIFO Full</strong>
        <div style="margin-left: 20px">
        0: Not full<br>
        1: Full
        </td>
    </tr>
    <tr>
        <td>6</td>
        <td>tx_em</td>
        <td>RO</td>
        <td>1</td>
        <td><strong>Transmit FIFO Empty</strong>
        <div style="margin-left: 20px">
        0: Not empty<br>
        1: Empty
        </td>
    </tr>
    <tr>
        <td>5</td>
        <td>rx_full</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Receive FIFO Full</strong>
        <div style="margin-left: 20px">
        0: Not full<br>
        1: Full
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>rx_em</td>
        <td>RO</td>
        <td>1</td>
        <td><strong>Receive FIFO Empty</strong>
        <div style="margin-left: 20px">
        0: Not empty<br>
        1: Empty
        </td>
    </tr>
    <tr>
        <td>3:2</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>1</td>
        <td>rx_busy</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Receive Busy</strong>
        <div style="margin-left: 20px">
        0: UART is not receiving a character<br>
        1: UART is receiving a character
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>tx_busy</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Transmit Busy</strong>
        <div style="margin-left: 20px">
        0: UART is not transmitting data<br>
        1: UART is transmitting data
        </td>
    </tr>
</tbody>
</table>

*Table 12‑10: UART Interrupt Enable Register*
<a name="uart-interrupt-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Interrupt Enable Register</td>
        <td colspan="1">UARTn_INT_EN</td>
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
        <td>31:7</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>6</td>
        <td>tx_he</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Half-Empty Event Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
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
        <td>rx_thd</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Threshold Event Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>rx_ov</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Overrun Event Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>cts_ev</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>CTS Signal Change Event Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>rx_par</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive Parity Event Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>rx_ferr</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive Frame Error Event Interrupt Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
</tbody>
</table>

*Table 12-11: UART Interrupt Flag Register*
<a name="uart-interrupt-flag-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Interrupt Flag</td>
        <td colspan="1">UARTn_INT_FL</td>
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
        <td>31:7</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>6</td>
        <td>tx_he</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Transmit FIFO Half-Empty Interrupt Flag</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled</div>
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
        <td>rx_thd</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Receive FIFO Threshold Interrupt Flag</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>rx_ov</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Receive FIFO Overrun Interrupt Flag</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>cts_ev</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>CTS Signal Change Interrupt Flag</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>rx_par</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Receive Parity Error Interrupt Flag</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>rx_ferr</td>
        <td>R/W1C</td>
        <td>0</td>
        <td><strong>Receive Frame Error Interrupt Flag</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
</tbody>
</table>

*Table 12-12: UART Clock Divisor Register*
<a name="uart-clock-divisor-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Clock Divisor</td>
        <td colspan="1">UARTn_CLKDIV</td>
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
        <td>31:20</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
        <tr>
        <td>19:0</td>
        <td>clkdiv</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Baud Rate Divisor</strong><br>
        This field sets the divisor used to generate the baud tick from the baud clock. For LPUART instances, if <a href="#uart-control-register">UARTn_CTRL</a>.<em>fdm</em> = 1, the fractional divisors are in increments of 0.5. The over-sampling rate must be no greater than this divisor. See section <a href="#baud-rate-generation">Baud Rate Generation</a> for information on how to use this field.        
        </td>
    </tr>
</table>

*Table 12-13: UART Oversampling Control Register*
<a name="uart-oversampling-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Oversampling Control</td>
        <td colspan="1">UARTn_OSR</td>
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
        <td>31:20</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>31:20</td>
        <td>osr</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>LPUART Over Sampling Rate</strong> <br>
        For LPUART instances with FDM enabled (<a href="#uart-control-register">UARTn_CTRL</a>.<em>fdm</em> = 1):
        <div style="margin-left: 20px">
            0: 8 × <br>
            1: 12 × <br>
            2: 16 × <br>
            3: 20 × <br>
            4: 24 × <br>
            5: 28 × <br>
            6: 32 × <br>
            7: 36 ×
        </div>
        For LPUART instances with FDM disabled (<a href="#uart-control-register">UARTn_CTRL</a>.<em>fdm</em> = 0): <br>
        <div style="margin-left: 20px">
            0: 128 × <br>
            1: 64 × <br>
            2: 32 × <br>
            3: 16 × <br>
            4: 8 × <br>
            5: 4 × <br>
            6 - 7: Reserved for Future Use
        </div>
        </td>
    </tr>
</tbody>
</table>

*Table 12-14: UART Transmit FIFO Register*
<a name="uart-transmit-fifo"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Transmit FIFO</td>
        <td colspan="1">UARTn_TXPEEK</td>
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
        <td>31:8</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
        <tr>
        <td>7:0</td>
        <td>data</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Transmit FIFO Data</strong><br>
        Read the transmit FIFO next data without affecting the contents of the transmit FIFO. If there are no entries in the transmit FIFO, this field reads 0. <br>
        <em>Note: The parity bit is available from this field.</em>
        </td>
    </tr>
</tbody>
</table>

*Table 12-15: UART Pin Control Register*
<a name="uart-pin-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Pin Control</td>
        <td colspan="1">UARTn_PNR</td>
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
        <td>31:2</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>1</td>
        <td>rts</td>
        <td>R/W</td>
        <td>1</td>
        <td><strong>RTS Pin Output State</strong><br>
        <div style="margin-left: 20px">
        0: RTS signal is driven to 0 <br>
        1: RTS signal is driven to 1
        </div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>cts</td>
        <td>RO</td>
        <td>1</td>
        <td><strong>CTS Pin State</strong><br>
        This field returns the current sampled state of the GPIO associated with the CTS signal.
        <div style="margin-left: 20px">
        0: CTS state is 0 <br>
        1: CTS state is 1
        </div>
        </td>
    </tr>
</tbody>
</table>

*Table 12-16: UART Data Register*
<a name="uart-fifo-data-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Data</td>
        <td colspan="1">UARTn_FIFO</td>
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
        <td>31:9</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>8</td>
        <td>rx_par</td>
        <td>R</td>
        <td>0</td>
        <td><strong>Receive FIFO Byte Parity</strong><br>
        If a parity error occurred during the reception of the character at the output end of the receive FIFO (this is returned by reading the <a href="#uart-fifo-data-register">UARTn_FIFO</a>.<em>data</em> field), this bit reads 1, otherwise it reads 0. <br>
        <em>Note: If parity is disabled, this bit always reads 0.</em>
        </td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>data</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit/Receive FIFO Data</strong><br>
        Writing to this field loads the next character into the transmit FIFO if the transmit FIFO is not full. <br>
        Reading from this field returns the next character from the receive FIFO if the receive FIFO is not empty. If the receive FIFO is empty, 0 is returned by the hardware. <br>
        For character widths less than 8, the unused bit(s) are ignored when the transmit FIFO is loaded, and the unused high bit(s) read 0 on characters read from the receive FIFO.
        </td>
    </tr>
</tbody>
</table>

*Table 12-17: UART DMA Register*
<a name="uart-dma-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART DMA</td>
        <td colspan="1">UARTn_DMA</td>
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
        <td>31:10</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>9</td>
        <td>rx_en</td>
        <td>0</td>
        <td>0</td>
        <td><strong>Receive DMA Channel Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>8:5</td>
        <td>rx_thd_val</td>
        <td>0</td>
        <td>0</td>
        <td><strong>Receive FIFO Level DMA Threshold</strong> <br>
        If <a href="#uart-status-register">UARTn_STATUS</a>.<em>rx_lvl</em> &lt; <a href="#uart-dma-control-register">UARTn_DMA</a>.<em>rx_thd_val</em>, then the receive FIFO DMA interface sends a signal to the DMA indicating characters are available in the UART receive FIFO to transfer to memory.
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>tx_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit DMA Channel Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>3:0</td>
        <td>tx_thd_val</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Transmit FIFO Level DMA Threshold</strong> <br>
        If <a href="#uart-status-register">UARTn_STATUS</a>.<em>tx_lvl</em> &lt; <a href="#uart-dma-control-register">UARTn_DMA</a>.<em>tx_thd_val</em>, the transmit DMA channel sends a signal to the DMA indicating that the UART transmit FIFO is ready to receive data from memory.
        </td>
    </tr>
</tbody>
</table>

*Table 12-18: UART Wakeup Enable*
<a name="uart-wakeup-interrupt-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Wakeup Enable</td>
        <td colspan="1">UARTn_WKEN</td>
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
        <td>31:3</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>2</td>
        <td>rx_thd</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Threshold Wake-up Event Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>rx_full</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Full Wake-Up Event Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div> 
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>rx_ne</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Not Empty Wake-Up Event Enable</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div> 
        </td>
    </tr>
</tbody>
</table>

*Table 12-19: UART Wakeup Flag Register*
<a name="uart-wakeup-interrupt-flag-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">UART Wakeup Flag</td>
        <td colspan="1">UARTn_WKFL</td>
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
        <td>31:3</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>2</td>
        <td>rx_thd</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Threshold Wake-up Event</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div> 
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>rx_full</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Full Wake-Up Event</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div> 
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>rx_ne</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Receive FIFO Not Empty Wake-Up Event</strong>
        <div style="margin-left: 20px">
        0: Disabled<br>
        1: Enabled
        </div> 
        </td>
    </tr>
</tbody>
</table>