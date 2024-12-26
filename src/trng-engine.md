The Analog Devices-supplied Universal Cryptographic Library (UCL)
provides a function to generate random numbers intended to meet the
requirements of common security validations. The entropy from one or
more internal noise sources continually feeds a TRNG, the output of
which is then processed by software and hardware to generate the number
returned by the UCL function. Analog Devices works directly with the
customer's accredited testing laboratory to provide any information
regarding the TRNG needed to support the customer's validation
requirements.

The general information in this section is provided only for
completeness; customers are expected to use the Analog Devices UCL to
generate random numbers.

Software can use the TRNG engine to generate AES keys using a hardware
key derivation function (HKDF) and using the TRNG as input to the HKDF.

## Registers

See *Table 3‑3* for the base address of this peripheral/module. See
*Table 1‑1* for an explanation of the read and write access of each
field. Unless specified otherwise, all fields are reset on a system
reset, soft reset, POR, and the peripheral-specific resets.

*Table 25-1: TRNG Register Summary*
<a name="table25-1"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th>Offset</th>
    <th>Register</th>
    <th>Name</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>[0x0000]</td>
    <td><a href="#table25-2">TRNG_CTRL</a></td>
    <td>TRNG Control Register</td>
  </tr>
  <tr>
    <td>[0x0004]</td>
    <td><a href="#table25-3">TRNG_STATUS</a></td>
    <td>TRNG Status Register</td>
  </tr>
  <tr>
    <td>[0x0008]</td>
    <td><a href="#table25-4">TRNG_DATA</a></td>
    <td>TRNG Data Register</td>
  </tr>
</tbody>
</table>

### Register Details

*Table 25-2: TRNG Control Register*
<a name="table25-2"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">Cryptographic Control</th>
    <th colspan="1">TRNG_CTRL</th>
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
    <td>31:16</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>15</td>
    <td>keywipe</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Wipe Key</strong><br>Write this field to 1 to wipe the TRNG key.</td>
  </tr>
  <tr>
    <td>14:4</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>3</td>
    <td>keygen</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Generate Key</strong><br>Write this field to 1 to generate a key using the TRNG.
    </td>
  </tr>
  <tr>
    <td>2</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>1</td>
    <td>rnd_ie</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Random Number Interrupt Enable</strong><br>
    This bit enables an interrupt to be generated when <a href="#table25-3">TRNG_STATUS</a>.<em>rdy</em> = 1.
    <div style="margin-left: 20px;">
    0: Disabled<br>
    1: Enabled</div>
    </td>
  </tr>
  <tr>
    <td>0</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
</tbody>
</table>

*Table 25-3: TRNG Status Register*
<a name="table25-3"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">TRNG Status</th>
    <th colspan="1">TRNG_STATUS</th>
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
    <td>31:1</td>
    <td>-</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>0</td>
    <td>rdy</td>
    <td>R</td>
    <td>0</td>
    <td><strong>Random Number Ready</strong><br> This bit is automatically cleared to 0, and a new random number is generated when <a href="#table25-4">TRNG_DATA</a>.<em>data</em> is read.</p>
    <div style="margin-left: 20px;">  
    0: Random number generation in progress. The content of <a href="#table25-4">TRNG_DATA</a>.<em>data</em> is invalid.<br>
    1: <a href="#table25-4">TRNG_DATA</a>.<em>data</em> contains new 32-bit random data. An interrupt is generated if <a href="#table25-2">TRNG_CTRL</a>.<em>rnd_ie</em> = 1.</div>
    </td>
  </tr>
</tbody>
</table>

*Table 25-4: TRNG Data Register*
<a name="table25-4"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th colspan="3">TRNG Data</th>
    <th colspan="1">TRNG_DATA</th>
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
    <td>31:0</td>
    <td>data</td>
    <td>RO</td>
    <td>0</td>
    <td><strong>TRNG Data</strong><br>The 32-bit random number generated is available in this field when <a href="#table25-3">TRNG_STATUS</a>.<em>rdy</em> = 1.</p></td>
  </tr>
</tbody>
</table>