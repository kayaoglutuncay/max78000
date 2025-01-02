The CAMERAIF is a peripheral designed to read data from camera sensors.

Key features:

- Reads 8-bit, 10-bit, or 12-bit parallel data from an external camera sensor.
- Supports multiple synchronization timing modes:

    - Horizontal and vertical synchronization timing mode using the PCIF_HSYNC and PCIF_VSYNC pins.
    - Start active video (SAV) and end active video (EAV) embedded timing codes within the data stream.

- 8 × 32-bit word FIFO depth.
- Interrupt support for:

    - FIFO not empty
    - FIFO threshold
    - FIFO full
    - Image complete

- Supports either single image capture mode or continuous image capture mode

## Instances
There is one instance of the CAMERAIF, shown in *Table 16‑1*. The pins and alternate functions for the CAMERAIF are shown in *Table 16‑2*.

*Table 16-1: MAX78000 CAMERAIF Instances*
<a name="table16-1"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Instance</th>
        <th>CAMERAIF Peripheral Clock Clock Options</th>
        <th>Receive FIFO Depth</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>CAMERAIF</td>
        <td>PCLK</td>
        <td>8</td>
    </tr>
</tbody>
</table>

*Table 16-2: MAX78000 CAMERAIF Signals*
<a name="table16-2"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Signal Name</th>
        <th>81-CTBGA Pin</th>
        <th>Alternate Function</th>
        <th>Signal Direction</th>
        <th>Description</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>PCIF_PCLK</td>
        <td>P1.9</td>
        <td>AF1</td>
        <td>Input</td>
        <td>Pixel Clock Input</td>
    </tr>
    <tr>
        <td>PCIF_HSYNC</td>
        <td>P1.8</td>
        <td>AF1</td>
        <td>Input</td>
        <td>Horizontal Synchronization Input</td>
    </tr>
    <tr>
        <td>PCIF_VSYNC</td>
        <td>P0.15</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Vertical Synchronization Input</td>
    </tr>
    <tr>
        <td>PCIF_D0</td>
        <td>P0.20</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 0</td>
    </tr>
    <tr>
        <td>PCIF_D1</td>
        <td>P0.21</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 1</td>
    </tr>
    <tr>
        <td>PCIF_D2</td>
        <td>P0.22</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 2</td>
    </tr>
    <tr>
        <td>PCIF_D3</td>
        <td>P0.23</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 3</td>
    </tr>
    <tr>
        <td>PCIF_D4</td>
        <td>P0.24</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 4</td>
    </tr>
    <tr>
        <td>PCIF_D5</td>
        <td>P0.25</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 5</td>
    </tr>
    <tr>
        <td>PCIF_D6</td>
        <td>P0.26</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 6</td>
    </tr>
    <tr>
        <td>PCIF_D7</td>
        <td>P0.27</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 7</td>
    </tr>
    <tr>
        <td>PCIF_D8</td>
        <td>P0.30</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 8</td>
    </tr>
    <tr>
        <td>PCIF_D9</td>
        <td>P0.31</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 9</td>
    </tr>
    <tr>
        <td>PCIF_D10</td>
        <td>P1.6</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 10</td>
    </tr>
    <tr>
        <td>PCIF_D11</td>
        <td>P1.7</td>
        <td>AF2</td>
        <td>Input</td>
        <td>Pixel Data Input 11</td>
    </tr>
</tbody>
</table>

## Capture Modes

The CAMERAIF supports either single image capture mode or continuous
capture mode. Each mode and the CAMERAIF configuration are described in
the following sections.

### Single Image Capture

In this mode, the CAMERAIF waits for one image from the sensor, then
stops reading data. Configure the CAMERAIF for this mode by setting the
<a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*read_mode* field to 1. The <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*read_mode*
field remains set to 1 before and while receiving image data from the
camera. Once the image is complete, the hardware automatically sets the
<a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*read_mode* field to 0 and sets the
<a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*img_done* status to 1.

### Continuous Capture

In this mode, the CAMERAIF continues to read image data as long as the
connected camera sensor continues to provide image data. Configure the
CAMERAIF for continuous capture mode by setting the
<a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*read_mode* field to 2. Disable continuous mode capture
by setting the <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*read_mode* field to 0.

## Timing Modes

There are two different timing modes, horizontal and vertical
synchronization mode and data streaming mode. Both timing modes can be
combined with single image capture, or continuous capture read modes.

### Horizontal and Vertical Synchronization Timing Mode

In this timing mode, the CAMERAIF uses the PCIF_HSYNC and the PCIF_VSYNC
input pins to determine the beginning and end of image data. The
CAMERAIF begins to accept image data on the PCIF_Dx pins once the
PCIF_VSYNC input pin is transitioned from 0 to 1 and the PCIF_HSYNC
input pin reads 1. The PCIF_VSYNC pin only needs to remain high for one
PCIF_PCLK period to detect the start of the video signal. The PCIF_HSYNC
signal is used to frame a complete set of pixel data. Re-assertion of
the PCIF_VSYNC signal indicates to the CAMERAIF that the image is
complete.

Set the bit <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*ds_timing_en* to 0 to configure the
CAMERAIF for horizontal and vertical synchronization mode.

### Data Stream Timing Mode

In this timing mode, the PCIF_HSYNC and PCIF_VSYNC input pins are
ignored. The CAMERAIF uses embedded timing codes to determine the start
and end of a single image or continuous stream. These codes can be
configured by setting the SAV code (<a href="#cameraif-timing-code-register">CAMERAIF_DS_TIMING_CODES</a>.*sav*)
and the EAV code (<a href="#cameraif-timing-code-register">CAMERAIF_DS_TIMING_CODES</a>.*eav*). These two codes
must match the codes sent by the connected camera respectively and
cannot be identical. Set <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*ds_timing_en* to 1 to
configure the CAMERAIF for embedded timing codes mode.

## Data Width

The width of the pixel data can be configured as 8-bit, 10-bit, or
12-bit. Pixel data is read from the PCIF_Dx input pins on the rising
edge of the PCIF_PCLK input pixel clock. It is assumed that PCIF_Dx
changes on the negative edge of PCIF_PCLK.

### 8-Bit Width

Setting <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*data_width* to 0 sets the recognized pixel
width on the PCIF_Dx bus to 8 bits. The upper 4 bits of PCIF_Dx inputs
are ignored. Pixel data is framed as 32-bit words before these words are
transferred to the 32-bit wide data FIFO and made ready to be read. The
32-bit data FIFO word is oriented with the most significant byte most
recently received 8-bit PCIF_Dx data. See *Figure 16‑1* and
*Figure 16‑2* examples.

*Figure 16-1: Horizontal and Vertical Synchronization Timing Mode with 8-Bit Data Width*
<a name="figure16-1"></a>

![Figure 16-1](assets/images/figure16-1.svg)

*Figure 16-2: Data Stream Timing Mode with 8-Bit Data Width*
<a name="figure16-2"></a>

![Figure 16-2](assets/images/figure16-2.svg)

#### 10 and 12-bit Width

Setting <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*data_width* to 1 sets the recognized pixel
width on the PCIF_Dx bus to 10-bits. Set <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*data_width* to
2 to set the recognized pixel width on the PCIF_Dx bus to 12-bits. As
with the 8-bit width setting, the pixel data is framed as 32-bit words
before these words are transferred to the 32-bit wide data FIFO
<a href="#cameraif-fifo-data-register">CAMERAIF_FIFO_DATA</a> and made ready to be read. These pixel widths are
MSB zero-padded to 16-bits, and two 16-bit pixels are concatenated to
form the 32-bit word. The most recently received PCIF_Dx data is the
most significant 16-bits of the FIFO data. See *Figure 16‑3* for a
PCIF_VSYNC/PCIF_HSYNC timing example.

*Figure 16-3: 10 or 12-bit PCIF_VSYNC/PCIF_HSYNC*
<a name="figure16-3"></a>


### Data FIFO

The data FIFO <a href="#cameraif-fifo-data-register">CAMERAIF_FIFO_DATA</a> is a 32-bit wide 8-word deep buffer
that contains data read from the PCIF_Dx pixel data input pins. The data
FIFO threshold can be configured by setting
<a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*fifo_thrsh*. The <a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*fifo_thresh* is set
if the data FIFO depth becomes greater than or equal to
<a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*fifo_thrsh*. An interrupt can be generated when this
condition happens if <a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*fifo_thresh* is set. The data
FIFO also provides status flags for FIFO full
(<a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*fifo_full*)and FIFO not empty
(<a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*fifo_not_empty*). Both status flags have associated
interrupts (<a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*fifo_full* and
<a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*fifo_not_empty*) that can be enabled and triggered
when the status flags are set.

### Usage

#### DMA

1.  Set <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*data_width* and <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*ds_timing_en* as required by the camera sensor attached.
2.  Enable the <a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*img_done* to generate an interrupt once the image is complete.
3.  Set <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*read_mode* for a single image or continuous capture. Triggering the camera sensor to output an image starts the PCI automatically.
4.  Set the <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*rx_dma_thrsh* field to the desired FIFO level required to trigger a DMA threshold event.
5.  Enable the receive DMA by setting the <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*rx_dma* field to 1.
6.  Enable the CAMERAIF by setting the <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*pcif_sys* field to 1.
7.  As data is read from the camera sensor by the CAMERAIF, it triggers a read request whenever it has a full 32-bit word in the data FIFO. Once the camera sensor has finished transmitting data, signaled by a rising edge on PCIF_VSYNC or a data stream EAV code, the CAMERAIF triggers the <a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*img_done* interrupt.
8.  The interrupt handler can then reset the interrupt flag by writing 1 to <a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*img_done.*

#### Interrupts

9.  Set <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*data_width* and <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*ds_timing_en* as required by the camera sensor attached.

10. Set <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*fifo_thrsh* to the desired level to allow the interrupt to service the FIFO before it fills.

11. Enable the <a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*img_done* and the <a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*fifo_thresh* interrupts, generating an interrupt when the image is complete or the FIFO has been filled to the threshold level set in the <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*fifo_thrsh* field.

12. Set <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*read_mode* for a single image or continuous capture. When the camera sensor is triggered to output an image, the CAMERAIF automatically starts receiving data.

13. Enable the CAMERAIF by setting the <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*pcif_sys* field to 1.

    As data is read from the camera sensor by the PCIF, the hardware triggers an interrupt when the FIFO threshold <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*fifo_thrsh* is met. The interrupt handler should perform a burst read from the FIFO (<a href="#cameraif-fifo-data-register">CAMERAIF_FIFO_DATA</a>.*data*). When the camera sensor finishes transmitting image data, signaled either by a rising edge on PCIF_VSYNC or a data stream EAV code, the hardware generates a <a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a>.*img_done* interrupt.

14. After servicing an image done interrupt, the interrupt handler must reset the image done interrupt flag by writing 1 to the <a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*img_done.*

15. The software should check <a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*fifo_not_empty* and perform a read of <a href="#cameraif-fifo-data-register">CAMERAIF_FIFO_DATA</a>.*data* to receive the remainder of the words of data that occupy the FIFO less than <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.*fifo_thrsh*. When all of the data is read from the FIFO, hardware clears the <a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.*fifo_not_empty* flag automatically.

## Register

*Table 16-3: Parallel Camera Interface Register Summary*
<a name="table16-3"></a>

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
    <td><a href="#cameraif-revision-register">CAMERAIF_VER</a></td>
    <td>CAMERAIF Revision Register</td>
</tr>
<tr>
    <td>[0x0004]</td>
    <td><a href="#cameraif-fifo-size-register">CAMERAIF_FIFO_SIZE</a></td>
    <td>CAMERAIF FIFO Size Register</td>
</tr>
<tr>
    <td>[0x0008]</td>
    <td><a href="#cameraif-configuration-register">CAMERAIF_CTRL</a></td>
    <td>CAMERAIF Configuration Register</td>
</tr>
<tr>
    <td>[0x000C]</td>
    <td><a href="#cameraif-interrupt-enable-register">CAMERAIF_INT_EN</a></td>
    <td>CAMERAIF Interrupt Enable Register</td>
</tr>
<tr>
    <td>[0x0010]</td>
    <td><a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a></td>
    <td>CAMERAIF Status Flag Register</td>
</tr>
<tr>
    <td>[0x0014]</td>
    <td><a href="#cameraif-timing-code-register">CAMERAIF_DS_TIMING_CODES</a></td>
    <td>CAMERAIF Timing Code Register</td>
</tr>
<tr>
    <td>[0x0030]</td>
    <td><a href="#cameraif-fifo-data-register">CAMERAIF_FIFO_DATA</a></td>
    <td>CAMERAIF FIFO Data Register</td>
</tr>
</table>

### Parallel Camera Register Details

*Table 16-4: CAMERAIF Version Register*
<a name="cameraif-revision-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">CAMERAIF Version</td>
        <td colspan="1">CAMERAIF_VER</td>
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
        <td>31:16</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>15:8</td>
        <td>major</td>
        <td>RO</td>
        <td>*</td>
        <td>
            <strong>Major Revision</strong><br>
            This field returns the major revision number of the CAMERAIF.
        </td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>minor</td>
        <td>RO</td>
        <td>*</td>
        <td>
            <strong>Minor Revision</strong><br>
            This field returns the minor revision number of the CAMERAIF.
        </td>
    </tr>
</table>

*Table 16-5: CAMERAIF FIFO Size Register*
<a name="cameraif-fifo-size-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">CAMERAIF FIFO Size</td>
        <td colspan="1">CAMERAIF_FIFO_SIZ</td>
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
        <td>31:8</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>fifo_size</td>
        <td>RO</td>
        <td>8</td>
        <td>
            <strong>FIFO Size</strong><br>
            This field returns the size of the CAMERAIF FIFO in words.
            <div style="margin-left: 20px;">
            8: FIFO size is 8 words.
            </div>
        </td>
    </tr>
</table>

*Table 16-6: CAMERAIF Configuration Register*
<a name="cameraif-configuration-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">CAMERAIF Configuration</td>
        <td colspan="1">CAMERAIF_CTRL</td>
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
        <td>31</td>
        <td>pcif_sys</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>Camera Interface Enable</strong><br>
            Set this field to 1 to enable the Camera interface.
            <div style="margin-left: 20px;">
            0: Camera interface disabled<br>
            1: Camera interface enabled
            </div>
        </td>
    </tr>
    <tr>
        <td>30</td>
        <td>three_ch_en</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>CNN Mode Enable</strong><br>
            Enables CNN mode to pad 5:6:5 and similar camera modes into 8:8:8, aligning the pixels and padding the top byte for 32-bit data.<br>
            <div style="margin-left: 20px;">
            0: CNN mode disabled<br>
            1: CNN mode enabled
            </div>
        </td>
    </tr>
    <tr>
        <td>29:16</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>30:17</td>
        <td>rx_dma_thrsh</td>
        <td>R/W</td>
        <td>1</td>
        <td>
            <strong>DMA Threshold</strong><br>
            Set the receive FIFO level to trigger a DMA request.<br>
            <div style="margin-left: 20px;">
            0: Invalid<br>
            1: Threshold event at level ≥ 1<br>
            ...<br>
            8: Threshold event at level = 8
            </div>
        </td>
    </tr>
    <tr>
        <td>16</td>
        <td>rx_dma</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>Receive DMA Enable</strong>
            <div style="margin-left: 20px;">
            0: DMA events disabled, pending events cleared<br>
            1: DMA events enabled
            </div>
        </td>
    </tr>
    <tr>
        <td>15:10</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>9:5</td>
        <td>fifo_thrsh</td>
        <td>R/W</td>
        <td>1</td>
        <td>
            <strong>Data FIFO Threshold Setting</strong><br>
            Threshold sets FIFO words for <em>fifo_thresh</em> flag.<br>
            <div style="margin-left: 20px;">
            0: Invalid<br>
            1: Threshold = 1 word<br>
            ...<br>
            8: Threshold = 8 words<br>
            9–31: Reserved
            </div>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>ds_timing_en</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>Camera Timing Select</strong><br>
            Selects synchronization mode.
            <div style="margin-left: 20px;">
            0: HSYNC/VSYNC timing<br>
            1: Embedded timing codes (SAV/EAV)
            </div>
        </td>
    </tr>
    <tr>
        <td>3:2</td>
        <td>data_width</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>Camera Data Width</strong><br>
            <div style="margin-left: 20px;">
            0: 8-bit data<br>
            1: 10-bit data<br>
            2: 12-bit data<br>
            3: Reserved
            </div>
        </td>
    </tr>
    <tr>
        <td>1:0</td>
        <td>read_mode</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>Camera Read Mode</strong><br>
            <div style="margin-left: 20px;">
            0: Disabled<br>
            1: Single image capture<br>
            2: Continuous capture<br>
            3: Reserved
            </div>
        </td>
    </tr>
</table>

*Table 16-7: CAMERAIF Interrupt Enable Register*
<a name="cameraif-interrupt-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">CAMERAIF Interrupt Enable</td>
        <td colspan="1">CAMERAIF_INT_EN</td>
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
        <td>31:4</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>fifo_not_empty</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>FIFO Not Empty Interrupt Enable</strong><br>
            Generates an interrupt when the FIFO is not empty (<a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.<em>fifo_not_empty</em> = 1), indicating data is available to read.
            <div style="margin-left: 20px;">
            0: Interrupt disabled<br>
            1: Interrupt enabled
            </div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>fifo_thresh</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>FIFO Threshold Interrupt Enable</strong><br>
            Generates an interrupt when the FIFO threshold is reached (<a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.<em>fifo_thresh</em> = 1).
            <div style="margin-left: 20px;">
            0: Interrupt disabled<br>
            1: Interrupt enabled
            </div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>fifo_full</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>FIFO Full Interrupt Enable</strong><br>
            Generates an interrupt when the FIFO is full (<a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.<em>fifo_full</em> = 1).
            <div style="margin-left: 20px;">
            0: Interrupt disabled<br>
            1: Interrupt enabled
            </div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>img_done</td>
        <td>R/W</td>
        <td>0</td>
        <td>
            <strong>Image Complete Interrupt Enable</strong><br>
            Generates an interrupt when the image is complete (<a href="#cameraif-status-flag-register">CAMERAIF_INT_FL</a>.<em>img_done</em> = 1).<br>
            <div style="margin-left: 20px;">
            0: Interrupt disabled<br>
            1: Interrupt enabled
            </div>
        </td>
    </tr>
</table>


*Table 16-8: CAMERAIF Status Flags Register*
<a name="cameraif-status-flag-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">CAMERAIF Status Flags</td>
        <td colspan="1">CAMERAIF_INT_FL</td>
        <td>[0x0010]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:4</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>3</td>
        <td>fifo_not_empty</td>
        <td>RO</td>
        <td>0</td>
        <td>
            <strong>FIFO Not Empty Status Flag</strong><br>
            Set by hardware when the FIFO level is 1 or greater. Automatically cleared when all data is read.
            <div style="margin-left: 20px;">
            0: FIFO is empty<br>
            1: FIFO is not empty
            </div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>fifo_thresh</td>
        <td>RO</td>
        <td>0</td>
        <td>
            <strong>FIFO Threshold Status Flag</strong><br>
            Set by hardware when the FIFO level meets or exceeds the threshold in <a href="#cameraif-configuration-register">CAMERAIF_CTRL</a>.<em>fifo_thrsh</em>. Cleared when the level falls below the threshold.
            <div style="margin-left: 20px;">
            0: FIFO threshold not exceeded<br>
            1: FIFO threshold exceeded
            </div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>fifo_full</td>
        <td>RO</td>
        <td>0</td>
        <td>
            <strong>FIFO Full Status Flag</strong><br>
            Set by hardware when the FIFO reaches full capacity (8 × 32-bit words). Automatically cleared when data is read.
            <div style="margin-left: 20px;">
            0: FIFO is not full<br>
            1: FIFO is full
            </div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>img_done</td>
        <td>R/W1C</td>
        <td>0</td>
        <td>
            <strong>Image Complete Status Flag</strong><br>
            Set by hardware when a PCIF_VSYNC transition or EAV code (<a href="#cameraif-timing-code-register">CAMERAIF_DS_TIMING_CODES</a>.<em>eav</em>) is detected.<br>
            0: End of the image not detected<br>
            1: End of the image detected
            </div>
        </td>
    </tr>
</table>

*Table 16-9: CAMERAIF Timing Codes Register*
<a name="cameraif-timing-code-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">CAMERAIF Camera Timing Codes</td>
        <td colspan="1">CAMERAIF_DS_TIMING_CODES</td>
        <td>[0x0014]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
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
        <td>15:8</td>
        <td>eav</td>
        <td>R/W</td>
        <td>0x9D</td>
        <td>
            <strong>End Active Video</strong><br>
            The 8-bit end active video code, camera-dependent. Must not equal <em>sav</em>. Set to the camera's code, default 0x9D.
        </td>
    </tr>
    <tr>
        <td>7:0</td>
        <td>sav</td>
        <td>R/W</td>
        <td>0x80</td>
        <td>
            <strong>Start Active Video</strong><br>
            The 8-bit start active video code, camera-dependent. Must not equal <em>eav</em>. Set to the camera's code, default 0x80.
        </td>
    </tr>
</table>

*Table 16-10: CAMERAIF FIFO Data Register*
<a name="cameraif-fifo-data-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">CAMERAIF FIFO Data</td>
        <td colspan="1">CAMERAIF_FIFO_DATA</td>
        <td>[0x0030]</td>
    </tr>
    <tr>
        <th>Bits</th>
        <th>Field</th>
        <th>Access</th>
        <th>Reset</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>31:0</td>
        <td>data</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Data</strong><br>
            Data from the FIFO to be read. Reading makes the next FIFO value available immediately.
        </td>
    </tr>
</table>