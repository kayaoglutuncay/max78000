The CRC engine performs CRC calculations on data written to the CRC data input register.
The features include:

- User-definable polynomials up to x<sup>32</sup> (33 terms)
- DMA support
- Supports automatic byte swap of data input and calculated output
- Supports big-endian or little-endian data input and calculated output
- Supports input reflection

An *n*-bit CRC can detect the following types of errors:

- Single-bit errors.
- Two bit errors for block lengths less than 2<sup>k</sup> where k is the order of the longest irreducible factor of the polynomial.
- Odd numbers of errors for polynomials with the parity polynomial (x+1) as one of its factors (polynomials with an even number of terms)
- Burst errors less than *n*-bits.

In general, all but 1 out of 2<sup>*n*</sup> errors are detected:

- 99.998% for a 16-bit CRC.
- 99.99999998% for a 32-bit CRC.

## Instances
Instances of the peripheral are listed in [Table 23‑1](#table23-1).

*Table 23‑1: MAX78000 CRC Instances*
<a name="table23-1"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Instance</th>
        <th>Maximum Terms</th>
        <th>DMA Support</th>
        <th>Big- and Little-Endian</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>CRC</td>
        <td>33 (2<sup>32</sup>)</td>
        <td>Yes</td>
        <td>Yes</td>
    </tr>
</tbody>
</table>

## Usage
A CRC value is often appended to the end of a data exchange between a
transmitter and receiver. The transmitter appends the calculated CRC to
the end of the transmission. The receiver independently calculates a CRC
on the data it received. The result should be a known constant if the
data and CRC were received error-free.

The software must configure the CRC polynomial, the starting CRC value,
and the endianness of the data. Once configured, the software or the
standard DMA engine transfers the data in either 8-bit, 16-bit, or
32-bit words to the CRC engine by writing to the corresponding data
input register. Use the <a href="#table23-6">CRC_DATAIN8</a> register for 8-bit data, the
<a href="#table23-7">CRC_DATAIN16</a> register for 16-bit data, and the <a href="#table23-8">CRC_DATAIN32</a> register for 32-bit data. The hardware automatically sets the <a href="#table23-5">CRC_CTRL</a>.*busy* field to 1 while the CRC engine is calculating a CRC over the input data. When the <a href="#table23-5">CRC_CTRL</a>.*busy* field reads 0, the CRC result is available in the <a href="#table23-10">CRC_VAL</a> register. The software or the standard DMA engine must track the data transferred to the CRC engine to determine when the CRC is finalized.

Because the receiving end calculates a new CRC on both the data and
received CRC, send the received CRC in the correct order, so the
highest-order term of the CRC is shifted through the generator first.
Because data is typically shifted through the generator LSB first, the
CRC is reversed bitwise, with the highest-order term of the remainder in
the LSB position. Software CRC algorithms typically manage this by
calculating everything backward. The software reverses the polynomial
and does right shifts on the data. The resulting CRC is bit swapped and
in the correct format.

By default, the CRC is calculated using the LSB first
(<a href="#table23-5">CRC_CTRL</a>.*msb* = 0.) When calculating the CRC using MSB first data
(reflected), the software must set <a href="#table23-5">CRC_CTRL</a>.*msb* to 1.

When calculating the CRC on data LSB first, the polynomial should be
reversed so that the coefficient of the highest power term is in the LSB
position. The largest term, *x*<sup>*n*</sup>, is implied (always
one) and should be omitted when writing to the <a href="#table23-9">CRC_POLY</a> register. This
is necessary because the polynomial is always one bit larger than the
resulting CRC, so a 32-bit CRC has a polynomial with 33 terms
(*x*<sup>0</sup>…*x*<sup>32</sup>).

*Table 23‑2: Organization of Calculated Result in the CRC_VAL.value Field*
<a name="table23-2"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th><a href="#table23-5">CRC_CTRL</a>.<em>msb</em></th>
        <th><a href="#table23-5">CRC_CTRL</a>.<em>byte_swap_out</em></th>
        <th>Order</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>0</td>
        <td>0</td>
        <td>The CRC value returned is the raw value</td>
    </tr>
    <tr>
        <td>1</td>
        <td>0</td>
        <td>The CRC value returned is reflected but not byte swapped</td>
    </tr>
    <tr>
        <td>0</td>
        <td>1</td>
        <td>The CRC value returned is byte swapped but not reflected</td>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>The CRC value returned is reflected and then byte swapped</td>
    </tr>
</tbody>
</table>

The CRC can be calculated on the MSB of the data first by setting the
<a href="#table23-5">CRC_CTRL</a>.*msb* field to 1, this is referred to as reflection. The CRC
polynomial register, <a href="#table23-9">CRC_POLY</a>, must be left-justified. The hardware
implies the MSB of the polynomial just as it does when calculating the
CRC LSB first. The LSB position of the polynomial must be set; this
defines the length of the CRC. The initial value of the CRC,
<a href="#table23-10">CRC_VAL</a>.*value*, must also be left justified. When the CRC calculation
is complete using MSB first, the software must right shift the
calculated CRC value, <a href="#table23-10">CRC_VAL</a>.*value*, by right shifting the output
value if the CRC polynomial is less than 32-bits.

## Polynomial Generation

The CRC can be configured for any polynomial up to x<sup>32</sup> (33 terms) by writing to the <a href="#table23-9">CRC_POLY</a>.*poly* field. *Table 23‑3* shows common CRC polynomials.

The reset value of the <a href="#table23-9">CRC_POLY</a>.*poly* field is the *CRC-32 Ethernet*
polynomial. This polynomial is used by Ethernet and file compression
utilities such as zip or gzip.

Note: Only write to the CRC polynomial register, <a href="#table23-9">CRC_POLY</a>.poly, when the <a href="#table23-5">CRC_CTRL</a>.busy field is 0.

*Table 23‑3: Common CRC Polynomials*
<a name="table23-3"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>Algorithm</th>
        <th>Polynomial Expression</th>
        <th>Order</th>
        <th>Polynomial</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>CRC-32 Ethernet</td>
        <td>x<sup>32</sup>+x<sup>26</sup>+x<sup>23</sup>+x<sup>22</sup>+x<sup>16</sup><br />
        +x<sup>12</sup>+x<sup>11</sup>+x<sup>10</sup>+x<sup>8</sup>+x<sup>7</sup><br />
        +x<sup>5</sup>+x<sup>4</sup>+x<sup>2</sup>+x<sup>1</sup>+x<sup>0</sup></td>
        <td>LSB</td>
        <td>0xEDB8 8320</td>
    </tr>
    <tr>
        <td>CRC-CCITT</td>
        <td>x<sup>16</sup>+x<sup>12</sup>+x<sup>5</sup>+x<sup>0</sup></td>
        <td>LSB</td>
        <td>0x0000 8408</td>
    </tr>
    <tr>
        <td>CRC-16</td>
        <td>x<sup>16</sup>+x<sup>15</sup>+x<sup>2</sup>+x<sup>0</sup></td>
        <td>LSB</td>
        <td>0x0000 A001</td>
    </tr>
    <tr>
        <td>USB Data</td>
        <td>x<sup>16</sup>+x<sup>15</sup>+x<sup>2</sup>+x<sup>0</sup></td>
        <td>MSB</td>
        <td>0x8005 0000</td>
    </tr>
    <tr>
        <td>Parity</td>
        <td>x<sup>1</sup>+x<sup>0</sup></td>
        <td>LSB</td>
        <td>0x0000 0001</td>
    </tr>
</tbody>
</table>

## Software CRC Calculations
The software can perform CRC calculations by writing directly to the CRC_DATAIN register. Each write to the CRC_DATAIN register triggers the hardware to compute the CRC value. The software is responsible for loading all data for the CRC into the CRC_DATAIN register. When complete, the result is read from the <a href="#table23-10">CRC_VAL</a> register.

Use the following procedure to calculate a CRC value by writing to the CRC_DATAIN register:

1.  Disable the CRC peripheral by setting the field <a href="#table23-5">CRC_CTRL</a>.*en* to 0.
2.  Configure input and output data format fields:

    a. <a href="#table23-5">CRC_CTRL</a>.*msb*
    
    b. <a href="#table23-5">CRC_CTRL</a>.*byte_swap_in*
    
    c. <a href="#table23-5">CRC_CTRL</a>.*byte_swap_out*

3.  Set the polynomial by writing to the <a href="#table23-9">CRC_POLY</a>.*poly* field.
4.  Set the initial value by writing to the <a href="#table23-10">CRC_VAL</a>.*value* field.

    a. For a 32-bit CRC, write the initial value to the <a href="#table23-10">CRC_VAL</a> register.
   
    b. For a 16-bit or 8-bit CRC, the unused bits in the <a href="#table23-10">CRC_VAL</a> register must be set to 0.

5.  Set the <a href="#table23-5">CRC_CTRL</a>.*en* field to 1 to enable the peripheral.
6.  Write a value to be processed to CRC_DATAIN register.

    a. Calculate an 8-bit CRC by writing an 8-bit value to the <a href="#table23-6">CRC_DATAIN8</a> register.

    b. Calculate a 16-bit CRC by writing a 16-bit value to the <a href="#table23-7">CRC_DATAIN16</a> register.

    c. Calculate a 32-bit CRC by writing a 32-bit value to the <a href="#table23-8">CRC_DATAIN32</a> register.

7. Poll the <a href="#table23-5">CRC_CTRL</a>.*busy* field until it reads 0.

8. Repeat steps 6 and 7 until all input data is complete.

9. Disable the CRC peripheral by clearing the <a href="#table23-5">CRC_CTRL</a>.*en* field to 0.

10. Read the CRC value from the <a href="#table23-10">CRC_VAL</a>.*value* field.

## DMA CRC Calculations
The CRC engine requests new data from the DMA controller when the fields <a href="#table23-5">CRC_CTRL</a>.en and <a href="#table23-5">CRC_CTRL</a>.dma_en are both set to 1. Enable the corresponding DMA channel's interrupt to receive an interrupt event when the CRC is complete. It is also possible for software to poll the DMA channel's interrupt flag directly by reading the DMA_INTFL.ch<n> flag until it reads 1.

Use the following procedure to calculate a CRC value using DMA:

10. Set <a href="#table23-5">CRC_CTRL</a>.*en* = 0 to disable the peripheral.

11. Configure the DMA:

    a. Set <a href="#table23-5">CRC_CTRL</a>.*dma_en* = 1 to enable DMA mode.
    
    b. See the DMA *Usage* section for details on configuring the DMA for a memory to peripheral transfer.
    
    c. Set the *DMA_CHn_CTRL*.*dstwd* field to match the size of the CRC calculation (0 for 8-bit, 1 for half-word, or 2 for word)

12. Configure the input and output data formats:

    a. <a href="#table23-5">CRC_CTRL</a>.*msb*

    b. <a href="#table23-5">CRC_CTRL</a>.*byte_swap_in*

    c. <a href="#table23-5">CRC_CTRL</a>.*byte_swap_out*

13. Set the polynomial by writing to the <a href="#table23-9">CRC_POLY</a>.*poly* field.
14. Set the initial value by writing to the <a href="#table23-10">CRC_VAL</a> register.

    a. For a 32-bit CRC, write the initial value to the <a href="#table23-10">CRC_VAL</a> register.

    b. For a 16-bit or an 8-bit CRC, the unused bits in the <a href="#table23-10">CRC_VAL</a> register must be set to 0.

15. Set the <a href="#table23-5">CRC_CTRL</a>.*en* field to 1 to enable the peripheral.
16. When the DMA operation completes, the hardware:

    a. Clears the <a href="#table23-5">CRC_CTRL</a>.*busy* field to 0.

    b. Loads the new CRC value into the <a href="#table23-10">CRC_VAL</a>.*value* field.

    c. Sets the *DMA_INTFL*.*ch\<n\>* field to 1 and generates a DMA interrupt if the *DMA_INTEN*.*ch\<n\>* field was set to 1.

17. Disable the CRC peripheral by clearing the <a href="#table23-5">CRC_CTRL</a>.*en* field to 0.
18. Read the CRC value from the <a href="#table23-10">CRC_VAL</a>.*value* field.

## Registers

See *Table 3‑3* for the base address of this peripheral/module. See
*Table 1‑1* for an explanation of the read and write access of each
field. Unless specified otherwise, all fields are reset on a system
reset, soft reset, POR, and the peripheral-specific resets.

*Table 23-4: CRC Register Summary*
<a name="table23-4"></a>

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
        <td><a href="#table23-5">CRC_CTRL</a></td>
        <td>CRC Control Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#table23-6">CRC_DATAIN8</a></td>
        <td>CRC 8-Bit Data Input Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#table23-7">CRC_DATAIN16</a></td>
        <td>CRC 16-Bit Data Input Register</td>
    </tr>
    <tr>
        <td>[0x0004]</td>
        <td><a href="#table23-8">CRC_DATAIN32</a></td>
        <td>CRC 32-Bit Data Input Register</td>
    </tr>
    <tr>
        <td>[0x0008]</td>
        <td><a href="#table23-9">CRC_POLY</a></td>
        <td>CRC Polynomial Register</td>
    </tr>
    <tr>
        <td>[0x000C]</td>
        <td><a href="#table23-10">CRC_VAL</a></td>
        <td>CRC Value Register</td>
    </tr>
</tbody>
</table>

### Register Details

*Table 23-5: CRC Control Register*
<a name="table23-5"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>CRC Control</th>
        <th>CRC_CTRL</th>
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
        <td>31:17</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>16</td>
        <td>busy</td>
        <td>R</td>
        <td>0</td>
        <td><strong>CRC Busy</strong>
        <div style="margin-left: 20px;">
        0: Not busy<br>
        1: Busy</div>
        </td>
    </tr>
    <tr>
        <td>15:5</td>
        <td>-</td>
        <td>RO</td>
        <td>0</td>
        <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>4</td>
        <td>byte_swap_out</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Byte Swap CRC Value Output</strong>
        <div style="margin-left: 20px;">
        0: <a href="#table23-10">CRC_VAL</a>.<em>value</em> is not byte swapped.
        1: <a href="#table23-10">CRC_VAL</a>.<em>value</em> is byte swapped.</div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>byte_swap_in</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Byte Swap CRC Data Input</strong>
        <div style="margin-left: 20px;">
        0: The data input is processed least significant byte first.<br>
        1: The data input is processed most significant byte first.</div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>msb</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Most Significant Bit Order</strong>
        <div style="margin-left: 20px;">
        0: LSB data first<br>
        1: MSB data first (reflected)</div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>dma_en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>DMA Enable</strong><br>
        Set this field to 1 to enable a DMA request when the CRC calculation is complete (<a href="#table23-5">CRC_CTRL</a>.<em>busy</em> = 0.)
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>CRC Enable</strong>
        <div style="margin-left: 20px;">
        0: Disabled<br>
        1: Enabled</div>
        </td>
    </tr>
</tbody>
</table>

*Table 23-6: CRC Data Input 8 Register*
<a name="table23-6"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>CRC 8-Bit Data Input</th>
        <th>CRC_DATAIN8</th>
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
        <td>7:0</td>
        <td>data</td>
        <td>W</td>
        <td>0</td>
        <td><strong>CRC Data Input</strong><br> Write 8-bit values to this register to calculate 8-bit CRCs. See <a href="#table23-2">Table 23‑2</a> for details on the byte and bit ordering of the data in this register.
        <p><em>Note: Do not write to this register if <a href="#table23-5">CRC_CTRL</a>.busy = 1 or <a href="#table23-5">CRC_CTRL</a>.en = 0.
        </em></p>
        </td>
    </tr>
</tbody>
</table>

*Table 23-7: CRC Data Input 16 Register*
<a name="table23-7"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>CRC Data 16-Bit Input</th>
        <th>CRC_DATAIN16</th>
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
        <td>15:0</td>
        <td>data</td>
        <td>W</td>
        <td>0</td>
        <td><strong>CRC Data Input</strong><br> Write 16-bit values to this register to calculate 16-bit CRCs. See <a href="#table23-2">Table 23‑2</a> for details on the byte and bit ordering of the data in this register.
        <p><em>Note: Do not write to this register if <a href="#table23-5">CRC_CTRL</a>.busy = 1 or <a href="#table23-5">CRC_CTRL</a>.en = 0.
        </em></p></td>
    </tr>
</tbody>
</table>

*Table 23-8: CRC Data Input 32 Register*
<a name="table23-8"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>CRC 32-Bit Data Input</th>
        <th>CRC_DATAIN32</th>
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
        <td>31:0</td>
        <td>data</td>
        <td>W</td>
        <td>0</td>
        <td><strong>CRC Data Input</strong><br> Write 32-bit values to this register to calculate 32-bit CRCs. See <a href="#table23-2">Table 23‑2</a> for details on the byte and bit ordering of the data in this register.
        <p><em>Note: Do not write to this register if <a href="#table23-5">CRC_CTRL</a>.busy = 1 or <a href="#table23-5">CRC_CTRL</a>.en = 0.
        </em></p></td>
    </tr>
</tbody>
</table>

*Table 23-9: CRC Polynomial Register*
<a name="table23-9"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>CRC Polynomial</th>
        <th>CRC_POLY</th>
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
        <td>31:0</td>
        <td>poly</td>
        <td>R/W</td>
        <td>0xEDB8 8320</td>
        <td><strong>CRC Polynomial</strong> <br> See <a href="#table23-2">Table 23‑2</a> for details on the byte and bit ordering of the data in this register.</p></td>
    </tr>
</tbody>
</table>

*Table 23-10: CRC Value Register*
<a name="table23-10"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
    <tr>
        <th>CRC Value</th>
        <th>CRC_VAL</th>
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
        <td>value</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Current CRC Value</strong><br> The software can write to this register to set the initial state of the accelerator. This register should only be read or written when <a href="#table23-5">CRC_CTRL</a>.<em>busy</em> = 0.
        <p>See <a href="#table23-2">Table 23‑2</a> for details on the byte and bit ordering of the data in this register.</p></td>
    </tr>
</tbody>
</table>