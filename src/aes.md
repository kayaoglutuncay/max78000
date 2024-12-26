The device provides a hardware AES accelerator to perform calculations on blocks up to 128 bits.

The features include:

-   Supports multiple key sizes:

    -   128-bits
    -   192-bits
    -   256-bits

-   DMA support for both receive and transmit channels
-   Supports multiple key sources:

    -   Encryption using the external AES key
    -   Decryption using the external AES key
    -   Decryption using the locally generated decryption key

## Instances
Instances of the peripheral are listed in [Table 24‑1](#table24-1). Disable the peripheral by clearing <a href="#aes-control-register">AES_CTRL</a>.*en* = 0 before writing to any register field.

*Table 24-1: MAX78000 AES Instances*
<a name="table24-1"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th>Instance</th>
    <th>128-Bit Key</th>
    <th>192-Bit Key</th>
    <th>256-Bit Key</th>
    <th>DMA Support</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>AES</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>Yes</td>
  </tr>
</tbody>
</table>

## Encryption of 128-Bit Blocks of Data Using FIFO
AES operations are typically performed on 128-bits of data at a time. The simplest use case is to have software encrypt 128-bit blocks of data. The <a href="#aes-control-register">AES_CTRL</a>.*start* field is unused in this case.

1.  Generate a key.
2.  Wait for the hardware to clear <a href="#aes-status-register">AES_STATUS</a>.*busy* = 0.
3.  Clear <a href="#aes-control-register">AES_CTRL</a>.*en* = 0 to disable the peripheral.
4.  If <a href="#aes-status-register">AES_STATUS</a>. *input_em* = 0, set <a href="#aes-control-register">AES_CTRL</a>.*input_flush* = 1 to flush the input FIFO.
5.  If <a href="#aes-status-register">AES_STATUS</a>. *output_em* = 0, set <a href="#aes-control-register">AES_CTRL</a>.*output_flush* = 1 to flush the output FIFO.
6.  Set <a href="#aes-control-register">AES_CTRL</a>.*key_size* to desired setting.
7.  Configure <a href="#aes-control-register">AES_CTRL</a>.*type* = 00 (encryption with external key).
8.  If interrupts are desired, set <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 1 so that an interrupt is triggered at the end of the AES calculation.
9.  Set <a href="#aes-control-register">AES_CTRL</a>.*en* = 1 to enable the peripheral.
10. Write four 32-bit words of data to <a href="#aes-data-fifo">AES_FIFO</a>.*data*.

    1. The hardware starts the AES calculation.

11. If <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 1, an interrupt is triggered after the AES calculation is complete.
12. If <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 0, the software should poll <a href="#aes-status-register">AES_STATUS</a>.*busy* until it reads 0.
13. Read four 32-bit words from <a href="#aes-data-fifo">AES_FIFO</a>.*data* (least significant word first).
14. Repeat steps 10 to 13 until all 128-bit blocks are processed.

## Encryption of 128-Bit Blocks Using DMA 

For this example, it is assumed that the DMA both reads and writes data
to/from the AES FIFO. This is not a requirement. The AES could use DMA
on one side and software on the other for the application. It is
required that for each DMA transmit request the DMA writes four 32-bit
words of data into the AES FIFO. It is required that for each DMA
receive request, the DMA reads four 32-bit words of data out of the AES
FIFO.

The <a href="#aes-control-register">AES_CTRL</a>.*start* field is unused in this case. The state of
<a href="#aes-status-register">AES_STATUS</a>.*busy* and <a href="#aes-interrupt-flag-register">AES_INTFL</a>.*done* is indeterminate during DMA
operations. The software must clear <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 0 when using the DMA mode. Use the appropriate DMA interrupt instead to determine when the DMA operation is complete, and the results can be read from <a href="#aes-data-fifo">AES_FIFO</a>.*data*.

Assuming the DMA is continuously filling the data input FIFO, the calculations complete in the following number of SYS_CLK cycles:

-   128-bit key: 181
-   192-bit key: 213
-   256-bit key: 245

The procedure to use DMA encryption/decryption is:

15. Generate a key.
16. Initialize the AES receive and transmit channels for the DMA controller.
17. Wait for the hardware to clear <a href="#aes-status-register">AES_STATUS</a>.*busy* = 0.
18. Clear <a href="#aes-control-register">AES_CTRL</a>.*en* = 0 to disable the peripheral.
19. If <a href="#aes-status-register">AES_STATUS</a>.*input_em* = 0, set <a href="#aes-control-register">AES_CTRL</a>.*input_flush* = 1 to flush the input FIFO.
20. If <a href="#aes-status-register">AES_STATUS</a>.*output_em* = 0, set <a href="#aes-control-register">AES_CTRL</a>.*output_flush* = 1 to flush the output FIFO.
21. Set <a href="#aes-control-register">AES_CTRL</a>.*key_size* to the desired setting.
22. Configure <a href="#aes-control-register">AES_CTRL</a>.*type* = 0 (encryption with external key).
23. Ensure <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 0 during DMA operations.
24. Set <a href="#aes-control-register">AES_CTRL</a>.*en* = 1 to enable the peripheral.
25. The DMA fills the FIFO, and the hardware begins the AES calculation.
26. When an AES calculation is completed, the AES hardware signals to the DMA that the data output FIFO is full and that it should be emptied. If the DMA does not empty the FIFO before the next calculation is complete, the hardware sets <a href="#aes-status-register">AES_STATUS</a>.*output_full* = 1.

Step 11 and step 12 are repeated if the DMA has new data to write to the data input FIFO.

*Note: The interface from the DMA to the AES only works when the amount of data is a multiple of 128-bits. For non‑multiples of 128-bits, the remainder after calculating all of the 128-bit blocks must be encrypted manually. See *Encryption of Blocks Less Than 128-Bits* for details*

## Encryption of Blocks Less Than 128-Bits

The AES engine automatically starts a calculation when 128 bits (four writes of 32 bits) occurs. Operations of less than 128‑bits use the start field to initiate the AES calculation.

27. Generate a key.
28. Wait for the hardware to clear <a href="#aes-status-register">AES_STATUS</a>.*busy* = 0.
29. Clear <a href="#aes-control-register">AES_CTRL</a>.*en* = 0 to disable the peripheral.
30. If <a href="#aes-status-register">AES_STATUS</a>.*input_em* =0, set <a href="#aes-control-register">AES_CTRL</a>.*input_flush* = 1 to flush the input FIFO.
31. If <a href="#aes-status-register">AES_STATUS</a>.*output_em* =0, set <a href="#aes-control-register">AES_CTRL</a>.*output_flush* = 1 to flush the output FIFO.
32. Set <a href="#aes-control-register">AES_CTRL</a>.*key_size* to desired setting.
33. Configure <a href="#aes-control-register">AES_CTRL</a>.*type* =00 (encryption with external key).
34. If interrupts are desired, set <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 1, so that an interrupt is triggered at the end of the AES calculation.
35. Set <a href="#aes-control-register">AES_CTRL</a>.*en* = 1 to enable the peripheral.
36. Write from one to three 32-bit words of data to <a href="#aes-data-fifo">AES_FIFO</a>.*data* (least significant word first).
37. Start the calculation manually by setting <a href="#aes-control-register">AES_CTRL</a>.*start* = 1.
38. If <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 1, an interrupt is triggered after the AES calculation is complete.
39. If <a href="#aes-interrupt-enable-register">AES_INTEN</a>.*done* = 0, the software should poll <a href="#aes-status-register">AES_STATUS</a>.*busy* until it reads 0. 
40. Read four 32-bit words from <a href="#aes-data-fifo">AES_FIFO</a>.*data* (least significant word first).

## Decryption
The decryption of data is very similar to encryption. The only difference is in the setting of the <a href="#aes-control-register">AES_CTRL</a>.*type* field. There are two settings of this field for decryption:

-   Decrypt with external key
-   Decrypt with internal decryption key

The internal decryption key is generated during an encryption operation. It may be necessary to complete a dummy encryption before doing the first decryption to ensure that it has been generated.

## Interrupt Events
The peripheral generates interrupts for the events shown in [Table 24‑2](#table24-2). Unless noted otherwise, each instance has its own independent set of interrupts and higher-level flag and enable fields.

Multiple events may set an interrupt flag and generate an interrupt if the corresponding interrupt enable field is set. The flags must be cleared by the software, typically in the interrupt handler.

*Table 24-2: Interrupt Events*
<a name="table24-2"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th>Event</th>
    <th>Local Interrupt Flag</th>
    <th>Local Interrupt Enable</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Data Output FIFO Overrun</td>
    <td><a href="#aes-interrupt-flag-register">AES_INTFL</a>.ov</td>
    <td><a href="#aes-interrupt-enable-register">AES_INTEN</a>.<em>ov</em></td>
  </tr>
  <tr>
    <td>Key Zero</td>
    <td><a href="#aes-interrupt-flag-register">AES_INTFL</a>.<em>key_zero</em></td>
    <td><a href="#aes-interrupt-enable-register">AES_INTEN</a>.<em>key_zero</em></td>
  </tr>
  <tr>
    <td>Key Change</td>
    <td><a href="#aes-interrupt-flag-register">AES_INTFL</a>.<em>key_change</em></td>
    <td><a href="#aes-interrupt-enable-register">AES_INTEN</a>.<em>key_change</em></td>
  </tr>
  <tr>
    <td>Calculation Done</td>
    <td><a href="#aes-interrupt-flag-register">AES_INTFL</a>.<em>done</em></td>
    <td><a href="#aes-interrupt-enable-register">AES_INTEN</a>.<em>done</em></td>
  </tr>
</tbody>
</table>

### Data Output FIFO Overrun
When an AES calculation is completed, the AES hardware signals to the DMA that the data output FIFO is full and that it should be emptied. If the DMA does not empty the FIFO before the next calculation is complete, a data output FIFO overrun event occurs, and the corresponding local interrupt flag is set.

### Key Zero
Attempting a calculation with a key of all zeros generates a key zero event.

### Key Change
Writing to any key register while <a href="#aes-status-register">AES_STATUS</a>.*busy* = 1 generates a key change event.

### Calculation Done
The transition of <a href="#aes-status-register">AES_STATUS</a>.*busy* = 1 to <a href="#aes-status-register">AES_STATUS</a>.*busy* = 0 generates a calculation done event. The calculation done event interrupt must be disabled when using the DMA.

## Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 24-3: AES Register Summary*
<a name="table24-3"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th>Offset</th>
    <th>Name</th>
    <th>Description</th>
  </tr>
</thead>
<tbody>
<tr>
    <td>[0x0000]</td>
    <td><a href="#aes-control-register">AES_CTRL</a></td>
    <td>AES Control Register</td>
</tr>
<tr>
    <td>[0x0004]</td>
    <td><a href="#aes-status-register">AES_STATUS</a></td>
    <td>AES Status Register</td>
</tr>
<tr>
    <td>[0x0008]</td>
    <td><a href="#aes-interrupt-flag-register">AES_INTFL</a></td>
    <td>AES Interrupt Flag Register</td>
</tr>
<tr>
    <td>[0x000C]</td>
    <td><a href="#aes-interrupt-enable-register">AES_INTEN</a></td>
    <td>AES Interrupt Enable Register</td>
</tr>
<tr>
    <td>[0x0010]</td>
    <td><a href="#aes-data-fifo">AES_FIFO</a></td>
    <td>AES Data FIFO</td>
</tr>
</tbody>
</table>


### Register Details

*Table 24-4: AES Control Register*
<a name="aes-control-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">AES Control</th>
    <th colspan="1">AES_CTRL</th>
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
    <td>31:10</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>9:8</td>
    <td>type</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Encryption Type</strong>
    <div style="margin-left: 20px">
    00: Encryption using the external AES key.<br>
    01: Decryption using the external AES key.<br>
    10: Decryption using the locally generated decryption key.<br>
    11: Reserved
    </div>
    </td>
  </tr>
  <tr>
    <td>7:6</td>
    <td>key_size</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Encryption Key Size</strong>
    <div style="margin-left: 20px">
    00: 128-bits<br>
    01: 192-bits<br>
    10: 256-bits<br>
    11: Reserved</div>
    </td>
  </tr>
  <tr>
    <td>5</td>
    <td>output_flush</td>
    <td>W1</td>
    <td>0</td>
    <td><strong>Flush Data Output FIFO</strong><br> This field always read 0.
    <div style="margin-left: 20px">
    0: No action<br>
    1: Flush</div>
    </td>
  </tr>
  <tr>
    <td>4</td>
    <td>input_flush</td>
    <td>W1</td>
    <td>0</td>
    <td><strong>Flush Data Input FIFO</strong><br> This field always read 0.
    <div style="margin-left: 20px">
    0: No action<br>
    1: Flush</div>
    </td>
  </tr>
  <tr>
    <td>3</td>
    <td>start</td>
    <td>W1</td>
    <td>0</td>
    <td><strong>Start AES Calculation</strong><br> This field forces the start of an AES calculation regardless of the state of the data input FIFO. This allows an AES calculation on less than 128-bits of data since an AES calculation normally starts when the data input FIFO is full.</p>
    <p>This field always read 0.</p>
    <div style="margin-left: 20px">
    0: No action<br>
    1: Start calculation</div>
    </td>
  </tr>
  <tr>
    <td>2</td>
    <td>dma_tx_en</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>DMA Request To Write Data Input FIFO</strong><br> When enabled, a DMA request is generated when the data input FIFO is empty.
    <div style="margin-left: 20px">
    0: Disabled<br>
    1: Enabled</div>
    </td>
  </tr>
  <tr>
    <td>1</td>
    <td>dma_rx_en</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>DMA Request To Read Data Output FIFO</strong><br> When enabled, a DMA request is generated when the data output FIFO is full.
    <div style="margin-left: 20px">
    0: Disabled<br>
    1: Enabled</div>
    </td>
  </tr>
  <tr>
    <td>0</td>
    <td>en</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>AES Enable</strong>
    <div style="margin-left: 20px">
    0: Disabled<br>
    1: Enabled.</div>
    </td>
  </tr>
</tbody>
</table>

*Table 24-5: AES Status Register*
<a name="aes-status-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">AES Status</th>
    <th colspan="1">AES_STATUS</h5></th>
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
    <td>31:5</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>4</td>
    <td>output_full</td>
    <td>R</td>
    <td>0</td>
    <td><strong>Output FIFO Full</strong>
    <div style="margin-left: 20px">
    0: Not full<br>
    1: Full</div>
    </td>
  </tr>
  <tr>
    <td>3</td>
    <td>output_em</td>
    <td>R</td>
    <td>0</td>
    <td><strong>Output FIFO Empty</strong>
    <div style="margin-left: 20px">
    0: Not empty<br>
    1: Empty</div>
    </td>
  </tr>
  <tr>
    <td>2</td>
    <td>input_full</td>
    <td>R</td>
    <td>0</td>
    <td><strong>Input FIFO Full</strong>
    <div style="margin-left: 20px">
    0: Not full<br>
    1: Full</div>
    </td>
  </tr>
  <tr>
    <td>1</td>
    <td>input_em</td>
    <td>R</td>
    <td>0</td>
    <td><strong>Input FIFO Empty</strong>
    <div style="margin-left: 20px">
    0: Not empty<br>
    1: Empty</div>
    </td>
  </tr>
  <tr>
    <td>0</td>
    <td>busy</td>
    <td>R</td>
    <td>0</td>
    <td><strong>AES Busy</strong>
    <div style="margin-left: 20px">
    0: Not busy<br>
    1: Busy</div>
    </td>
  </tr>
</tbody>
</table>

*Table 24-6: AES Interrupt Flag Register*
<a name="aes-interrupt-flag-register"></a>


<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">AES Interrupt Flag</th>
    <th colspan="1">AES_INTFL</th>
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
    <td>31:4</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>3</td>
    <td>ov</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Data Output FIFO Overrun Event Interrupt</strong>
    <div style="margin-left: 20px">
    0: No event<br>
    1: Event occurred</div>
    </td>
  </tr>
  <tr>
    <td>2</td>
    <td>key_zero</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Key Zero Event Interrupt</strong>
    <div style="margin-left: 20px">
    0: No event<br>
    1: Event occurred</div>
    </td>
  </tr>
  <tr>
    <td>1</td>
    <td>key_change</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Key Change Event Interrupt</strong>
    <div style="margin-left: 20px">
    0: No event<br>
    1: Event occurred</div>
    </td>
  </tr>
  <tr>
    <td>0</td>
    <td>done</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Calculation Done Event Interrupt</strong>
    <div style="margin-left: 20px">
    0: No event<br>
    1: Event occurred</div>
    </td>
  </tr>
</tbody>
</table>

*Table 24-7: AES Interrupt Enable Register*
<a name="aes-interrupt-enable-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">AES Interrupt Enable</th>
    <th colspan="1">AES_INTEN</th>
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
    <td>31:4</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>3</td>
    <td>ov</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Data Output FIFO Overrun Event Interrupt Enable</strong>
    <div style="margin-left: 20px">
    0: Enabled<br>
    1: Disabled</div>
    </td>
  </tr>
  <tr>
    <td>2</td>
    <td>key_zero</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Key Zero Event Interrupt Enable</strong>
    <div style="margin-left: 20px">
    0: Enabled<br>
    1: Disabled</div>
    </td>
  </tr>
  <tr>
    <td>1</td>
    <td>key_change</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Key Change Event Interrupt Enable</strong>
    <div style="margin-left: 20px">
    0: Enabled<br>
    1: Disabled</div></td>
  </tr>
  <tr>
    <td>0</td>
    <td>done</td>
    <td>W1C</td>
    <td>0</td>
    <td><strong>Calculation Done Event Interrupt Enable</strong><br> This interrupt must be disabled when using the DMA.
    <div style="margin-left: 20px">
    0: Enabled<br>
    1: Disabled</div>
    </td>
  </tr>
</tbody>
</table>

*Table 24-8: AES FIFO Register*
<a name="aes-data-fifo"></a>


<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">AES Data</th>
    <th colspan="1">AES_FIFO</th>
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
    <td>31:0</td>
    <td>data</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>AES FIFO</strong><br> Writing this register puts data to the data input FIFO. The hardware automatically starts a calculation after 4 words are written to this FIFO. The data should be written with the least significant word first.
    <p>Reading this register pulls data from the data output FIFO. The least significant word is read first.</p></td>
  </tr>
</tbody>
</table>