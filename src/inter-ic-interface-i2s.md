I²S is a serial audio interface for communicating pulse-code modulation (PCM) encoded streams between devices. The peripheral supports both master and slave modes.

Key features:

- Stereo (2 channel) and mono (left or right channel option) formats
- Separate DMA channels for transmit and receive.
- Flexible timing:

    - Configurable sampling rate from 165536 to 1 of the I2S input clock.

- Flexible data format:

    - The number of bits per data word can be selected from 1 to 32, typically 8, 16, 24, or 32-bit width.
    - Feature enhancement not in the I2S specification.

        - Word/Channel select polarity control.
        - First bit position selection.
        - Selectable FIFO data alignment to the MSB or the LSB of the sample
        - Sample size less than the word size with adjustment to MSB or LSB of the word.
        - Optional sign extension.

- Full-duplex serial communication with separate I2S serial data input and serial data output pins

## Instances
*Table 15-1: MAX78000 I2S Instances*

### I2S Bus Lines and Definitions

## Details

## Master and Slave Mode Configuration

## Clocking

### BCLK Generation for Master Mode

### LRCLK Period Calculation

## Data Formatting
### Sample Size

### Word Select Polarity

### First Bit Location Control

### Sample Adjustment

### Stereo/Mono Configuration

## Transmit and Receive FIFOs
### FIFO Data Width

### Transmit FIFO

### Receive FIFO

### FIFO Word Control

### FIFO Data Alignment

### Typical Audio Configurations

## Interrupt Events

### Receive FIFO Overrun

### Receive FIFO Threshold

### Transmit FIFO Half-Empty

### Transmit FIFO One Entry Remaining

## Direct Memory Access

## Block Operation

## Registers
See Table 3-3 for the base address of this peripheral/module. See Table 1-1 for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 15-9: I2S Register Summary*

### Register Details

*Table 15-10: I2S Control 0 Register*