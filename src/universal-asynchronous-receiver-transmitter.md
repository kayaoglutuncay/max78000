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

Figure 12-1 shows a high-level diagram of the UART peripheral.

*Note: See Table 12-1 for the clock options supported by each UART instance.*

## Instances

## DMA

## UART Frame

## FIFOs

### Transmit FIFO Operation

### Receive FIFO Operation

### Flushing

## Interrupt Events

### Frame Error
### Parity Error
### CTS Signal Change
### Overrun
### Receive FIFO Threshold
### Transmit FIFO Half-Empty

## LPUART Wakeup Events
### Receive FIFO Threshold
### Receive FIFO Full
### Receive Not Empty

## Inactive State
## Receive Sampling
## Baud Rate Generation

### UART Clock Sources

### LPUART Clock Sources

### Baud Rate Calculation

### Low-Power Mode Operation of LPUARTs for 9600 Baud and Below

#### Configuring an LPUART for Low-Power Modes of Operation

## Hardware Flow Control

### Automated HFC

### Application Controlled HFC

#### RTC/CTS Handling for Application Controlled HFC

## Registers

### Register Details