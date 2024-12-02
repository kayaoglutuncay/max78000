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
Instances of the peripheral are shown in Table 12-1. The standard UARTs and the LPUARTs are functionally similar; they are referred to as UART for common functionality. The LPUART instance supports fractional division mode (FDM) and is referenced as LPUART for feature-specific options.

*Table 12-1: MAX78000 UART/LPUART Instances*
<a name="table12-1-max78000-uart-lpuart-instances"></a>


## DMA
Each UART instance supports DMA for both transmit and receive; separate DMA channels can be connected to the receive and transmit FIFOs.

The UART DMA channels are configured using the UART DMA configuration register, UARTn_DMA. Enable the receive FIFO DMA channel by setting UARTn_DMA.rx_en to 1 and enable the transmit FIFO DMA channel by setting UARTn_DMA.tx_en to 1. DMA transfers are automatically triggered by the hardware based on the number of bytes in the receive FIFO and transmit FIFO.

When DMA is enabled, the following describes the behavior of the DMA requests:

- A receive DMA request is asserted when the number of bytes in the receive FIFO transitions to be greater than or equal to the receive FIFO threshold.
- A transmit DMA request is asserted when the number of bytes in the transmit FIFO transitions to be less than the transmit FIFO threshold.

## UART Frame
Figure 12-2 shows the UART frame structure. Character sizes of 5 to 8 bits are configurable through the UARTn_CTRL.char_size field. Stop bits are configurable as 1 or 1.5 bits for 5-character frames and 1 or 2 stop bits for 6, 7, or 8-character frames. Parity support includes even, odd, mark, space, and none.

## FIFOs
Separate receive and transmit FIFOs are provided. The FIFOs are both accessed through the same UARTn_FIFO.data field. The current level of the transmit FIFO is read from the UARTn_STATUS.tx_lvl field. The current level of the receive FIFO is read from the UARTn_STATUS.rx_lvl field. Data for character sizes less than 7 bits are right justified.

### Transmit FIFO Operation
Writing data to the UARTn_FIFO.data field increments the transmit FIFO pointer, UARTn_STATUS.tx_lvl, and loads the data into the transmit FIFO. The UARTn_TXPEEK.data register provides a feature that allows the software to "peek" at the current value of the write-only transmit FIFO without changing the UARTn_STATUS.tx_lvl. Writes to the transmit FIFO are ignored while UARTn_STATUS.tx_lvl = C_TX_FIFO_DEPTH.

### Receive FIFO Operation
Reads of the UARTn_FIFO.data field return the character values in the receive FIFO and decrement the UARTn_STATUS.rx_lvl. An overrun event occurs if a valid frame, including parity, is detected while UARTn_STATUS.rx_lvl = C_RX_FIFO_DEPTH. When an overrun event occurs, the data is discarded by hardware.

A parity error event indicates that the value read from UARTn_FIFO.data contains a parity error.

### Flushing
The FIFOs are flushed on the following conditions:

- Setting the UARTn_CTRL.rx_flush field to 1 flushes the receive FIFO by setting its pointer to 0.
- Setting the UARTn_CTRL.tx_flush field to 1 flushes the transmit FIFO by setting its pointer to 0.
- Flush the FIFOs by setting the GCR_RST0.uart0, GCR_RST0.uart1, or GCR_RST0.uart2 field to 1.

## Interrupt Events
The peripheral generates interrupts for the events shown in Table 12-2. Unless noted otherwise, each instance has its own set of interrupts and higher-level flag and enable fields, as shown in Table 12-2.

*Figure 12-3: UART Interrupt Functional Diagram*


Some activities may cause more than one event, setting one or more event flags. An event interrupt occurs if the corresponding interrupt enable is set. The interrupt flags, when set, must be cleared by the software by writing 1 to the corresponding interrupt flag field.

*Table 12-2: MAX78000 Interrupt Events*

### Frame Error
A frame error is generated when the UART sampling circuitry detects an invalid bit. As shown in Figure 12-4, each bit is sampled three times and can generate a frame error on the start bit, stop bit, data bits, and optionally the parity bit. When a frame error occurs, the data is discarded.

The frame error criteria are different based on the following:

- Standard UART and LPUART with FDM disabled:

    - The start bit is sampled 3 times, and all samples must be 0, or a frame error is generated.
    - Each data bit is sampled, and 2 of the 3 samples must match, or a frame error is generated.
    - If parity is enabled, the parity bit is sampled 3 times, and all samples must match, or a frame error is generated.
    - The stop bit is sampled 3 times, and all samples must be 1, or a frame error is generated.
    - See Table 12-3 for details.

- LPUART with FDM enabled (UARTn_CTRL.fdm = 1) and data/parity edge detect enabled (UARTn_CTRL.dpfe_en = 1):

    - The start bit is sampled 3 times, and all samples must be 0, or a frame error is generated.
    - Each data bit is sampled 3 times, and all samples must match, or a frame error is generated.
    - If parity is enabled, the parity bit is sampled 3 times, and all samples must match, or a frame error is generated.
    - The stop bit is sampled 3 times, and all samples must be 1, or a frame error is generated.
    - See Table 12-4 for details.

*Table 12-3: Frame Error Detection for Standard UARTs and LPUART*

*Table 12-4: Frame Error Detection for LPUARTs with UARTn_CTRL.fdm = 1 and UARTn_CTRL.dpfe_en = 1*

### Parity Error
Set UARTn_CTRL.par_en = 0 to enable parity checking of the received frame. If the calculated parity does not match the parity bit, then the corresponding interrupt flag is set. The data received is saved to the receive FIFO when a parity error occurs.

### CTS Signal Change
A CTS signal change condition occurs if HFC is enabled, the UART baud clock is enabled, and the CTS pin changes state.

### Overrun
An overrun condition occurs if a valid frame is received when the receive FIFO is full. The interrupt flag is set at the end of the stop bit, and the frame is discarded.

### Receive FIFO Threshold
A receive FIFO threshold event occurs when a valid frame is received that causes the number of bytes to exceed the configured receive FIFO threshold UARTn_CTRL.rx_thd_val.

### Transmit FIFO Half-Empty
The transmit FIFO half-empty event occurs when UARTn_STATUS.tx_lvl transitions from more than half-full to half-empty, as shown in Equation 12-1.

*Note: When this condition occurs, verify the number of bytes in the transmit FIFO (UARTn_STATUS.tx_lvl) before re-filling.*

*Equation 12-1: UART Transmit FIFO Half-Empty Condition*

## LPUART Wakeup Events
LPUART instances can receive characters while in the low-power modes listed in Table 12-1. If enabled, each of the receive FIFO conditions shown in Table 12-5 wakes the device, exits the low-power mode, and returns the device to ACTIVE.

Unlike interrupts, wake-up activity is based on a condition, not an event. As long as the condition is true and the wake-up enable field is set to 1, the wake-up flag remains set.

*Table 12-5: MAX78000 Wakeup Events*

### Receive FIFO Threshold
This condition persists while UARTn_STATUS.rx_lvl ≥ UARTn_CTRL.rx_thd_val.

### Receive FIFO Full
This condition persists while UARTn_STATUS.rx_lvl ≥ C_RX_FIFO_DEPTH.

### Receive Not Empty
This condition persists while UARTn_STATUS.rx_lvl > 0.

## Inactive State
The following conditions result in the UART being inactive:

- When UARTn_CTRL.bclken = 0
- After setting UARTn_CTRL.bclken to 1 until UARTn_CTRL.bclkrdy = 1
- Any write to the UARTn_CLKDIV.clkdiv field while UARTn_CTRL.bclken = 1
- Any write to the UARTn_OSR.osr field when UARTn_CTRL.bclken = 1

## Receive Sampling
Each bit of a frame is oversampled to improve noise immunity. The oversampling rate (OSR) is configurable with the UARTn_OSR.osr field. In most cases, the bit is evaluated based on three samples at the midpoint of each bit time, as shown in Figure 12-4.

*Figure 12-4: Oversampling Example*

Whenever UARTn_CLKDIV.clkdiv < 0x10 (i.e., division rate less than 8.0), OSR is not used, and the oversampling rate is adjusted to full sampling by the hardware. In full sampling, the receive input is sampled on every clock cycle regardless of the OSR setting.

Note: For 9600 baud low-power operation, the dual-edge sampling mode must be enabled (UARTn_CTRL.desm = 1).

## Baud Rate Generation
The baud rate is determined by the selected UART clock source and the value of the clock divisor. Multiple clock sources are available for each UART instance. See Table 12-1 for available clock sources.

Note: Changing the clock source should only be done between data transfers to avoid corrupting an ongoing data transfer.

### UART Clock Sources
Standard UART instances operate only in ACTIVE and SLEEP. Standard UART instances can only wake the device from SLEEP. Figure 12-5 shows the baud rate generation path for standard UARTs.

*Figure 12-5: UART Baud Rate Generation*

### LPUART Clock Sources
LPUART instances support FDM and are configurable for operation at 9600 and lower baud rates for operation in SLEEP, LPM, and UPM. Operation in LPM and UPM requires the use of the ERTCO as the baud rate clock source. The ERTCO can be configured to remain active in LPM and UPM, allowing the LPUART to receive data and serve as a wake-up source while power consumption is at a minimum.

*Figure 12-6: LPUART Timing Generation*

### Baud Rate Calculation
The transmit and receive circuits share a common baud rate clock, the selected UART clock source divided by the clock divisor. Instances that support FDM offer a 0.5 fractional clock division when enabled by setting UARTn_CTRL.fdm = 1. This allows for greater accuracy when operating at low baud rates and finer granularity for the oversampling rate.

Use the following formula to calculate the UARTn_CLKDIV.clkdiv value based on the clock source, desired baud rate, and integer or fractional divisor.

*Equation 12-2: UART Clock Divisor Formula*

*Equation 12-3: LPUART Clock Divisor Formula for UARTn_CTRL.fdm = 1*

For example, in a case where the UART clock is 50MHz, and the target baud rate is 115,200 bps:

### Low-Power Mode Operation of LPUARTs for 9600 Baud and Below
LPUART instances can configure the receiver for 9600 and lower baud rates and enable the LPUART in the low-power modes SLEEP, LPM, and UPM. Receipt of a valid frame loads the receive FIFO and increments UARTn_STATUS.rx_lvl. If a wake-up event, shown in Table 12-5, is enabled, the device exits the current low-power mode and returns to ACTIVE if the wake-up event occurs. See section Baud Rate Calculation and Equation 12-3 for details on setting the baud rate for LPUART instances with UARTn_CTRL.fdm set to 1.

*Table 12-6: LPUART Low Baud Rate Generation Examples (UARTn_CTRL.fdm = 1)*

#### Configuring an LPUART for Low-Power Modes of Operation
Use the following procedure to receive characters at 9600 or lower baud rates while in low-power modes:

1. Clear UARTn_CTRL.bclken = 0 to disable the baud clock. The hardware immediately clears UARTn_CTRL.bclkrdy to 0.
2. Set PWRSEQ_LPCN.x32ken= 1 to ensure the 32kHz clock source remains active in LPM and UPM modes.
3. Ensure UARTn_CTRL.ucagm = 1.
4. Configure UARTn_CTRL.bclksrc to select the ERTCO.
5. Set UARTn_CTRL.fdm to 1 to enable FDM.
6. Set UARTn_CLKDIV.clkdiv to the calculated clock divisor shown in Table 12-6 for the required baud rate.
7. Set UARTn_CTRL.desm to 1 to enable receive dual-edge sampling mode.
8. Choose the desired wake-up conditions from Table 12-5.
a. Clear any of the wake-up conditions chosen if currently active in the UARTn_WKFL register.
b. Enable the wake-up condition; set the wake-up field to 1 in the UARTn_WKEN register.
9. Set the UARTn_CTRL.bclken field to 1 to enable the baud clock.
10. Poll the UARTn_CTRL.bclkrdy field until it reads 1.
11. Enter the desired low-power mode.

## Hardware Flow Control
The optional HFC uses two additional pins, CTS and RTS, as a handshaking protocol to manage UART communications. For full-duplex operation, the RTS output pin on the peripheral is connected to the CTS input pin on the external UART, and the CTS input pin on the peripheral is connected to the RTS output pin on the external UART, as shown in Figure 12-7.

*Figure 12-7: Hardware Flow Control Physical Connection*

In HFC operation, a UART transmitter waits for the external device to assert its CTS pin. When CTS is asserted, the UART transmitter sends data to the external device. The external device keeps CTS asserted until it cannot receive additional data, typically because the external device's receive FIFO is full. The external device then deasserts CTS until the device can receive more data. The external device then asserts CTS again, allowing additional data to be sent.

Hardware flow control can be fully automated by the peripheral hardware or by software through direct monitoring of the CTS input signal and control of the RTS output signal.

### Automated HFC
Setting UARTn_CTRL.hfc_en = 1 enables automated HFC. When automated HFC is enabled, the hardware manages the CTS and RTS signals. The deassertion of the RTS signal is configurable using the UARTn_CTRL.rtsdc field:

- UARTn_CTRL.rtsdc = 0: Deassert RTS when UARTn_STATUS.rx_lvl = C_RX_FIFO_DEPTH
- UARTn_CTRL.rtsdc = 1: Deassert RTS while UARTn_STATUS.rx_lvl ≥= UARTn_CTRL.rx_thd_val

The transmitter continues to send data as long as the CTS signal is asserted and there is data in the transmit FIFO. If the receiver de-asserts the CTS pin, the transmitter finishes the transmission of the current character and then waits until the CTS pin state is asserted before continuing transmission. Figure 12-8 shows the state of the CTS pin during a transmission under automated HFC.

Automated HFC does not generate interrupt events related to the state of the transmit FIFO or the receive FIFO. The software must handle FIFO management. See Interrupt Events for additional information.

*Figure 12-8: Hardware Flow Control Signaling for Transmitting to an External Receiver*

### Application Controlled HFC
Application controlled HFC requires the software to manually control the RTS output pin and monitor the CTS input pin. Using application controlled HFC requires the automated HFC to be disabled by setting the UARTn_CTRL.hfc_en field to 1. Additionally, the software should enable CTS sampling (UARTn_CTRL.cts_dis = 0) if performing application controlled HFC.

#### RTC/CTS Handling for Application Controlled HFC
The software can manually monitor the CTS pin state by reading the field UARTn_PNR.cts. The software can manually set the state of the RTS output pin and read the current state of the RTS output pin using the field UARTn_PNR.rts. The software must manage the state of the RTS pin when performing application controlled HFC.

Interrupt support for CTS input signal change events is supported even when automated HFC is disabled. The software can enable the CTS interrupt event by setting the UARTn_INT_EN.cts_ev field to 1. The CTS signal change interrupt flag is set by the hardware any time the CTS pin state changes. The software must clear this interrupt flag manually by writing 1 to the UARTn_INT_FL.cts_ev field.

*Note: CTS pin state monitoring is disabled any time the UART baud clock is disabled (UARTn_CTRL.bclken = 0). The software must enable CTS pin monitoring by setting the field UARTn_CTRL.cts_dis to 0 after enabling the baud clock if CTS pin state monitoring is required.*

## Registers
See Table 3-3 for the base address of this peripheral/module. If multiple instances of the peripheral are provided, each instance has its own independent set of the registers shown in Table 12-7. Register names for a specific instance are defined by replacing "n" with the instance number. As an example, a register PERIPHERALn_CTRL resolves to PERIPHERAL0_CTRL and PERIPHERAL1_CTRL for instances 0 and 1, respectively.

See Table 1-1 for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

All registers and fields apply to both UART and LPUART instances unless specified otherwise.

*Table 12-7: UART/LPUART Register Summary*

### Register Details

*Table 12-8: UART Control Register*