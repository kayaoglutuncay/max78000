# Introduction
For ordering information, mechanical and electrical characteristics for the MAX78000 family of devices refer to the device data sheet. For information on the Arm® Cortex®-M4 with FPU core, please refer to the [Arm Cortex-M4 Processor Technical Reference Manual](https://developer.arm.com/documentation/100166/0001).

## Related Documentation
The MAX78000 data sheet and errata are available from the Analog Devices website, [MAX78000](https://www.analog.com/en/products/max78000.html)

## Document Conventions
### Number Notations
<a name="number-notations"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td><strong>Notation</strong></td>
        <td><strong>Description</strong></td>
    </tr>
    <tr>
        <td>0xNN</td>
        <td>Hexadecimal (Base 16) numbers are preceded by the prefix 0x.</td>
    </tr>
    <tr>
        <td>0bNN</td>
        <td>Binary (Base 2) numbers are preceded by the prefix 0b.</td>
    </tr>
    <tr>
        <td>NN</td>
        <td>Decimal (Base 10) numbers are represented using no additional prefix or suffix.</td>
    </tr>
    <tr>
        <td>V[X:Y]</td>
        <td>Bit field representation of a register, field, or value (V) covering Bit X to Bit Y.</td>
    </tr>
    <tr>
        <td>Bit N</td>
        <td>Bits are numbered in little-endian format; that is, the least significant bit of a number is referred to as bit 0.</td>
    </tr>
    <tr>
        <td>[0xNNNN]</td>
        <td>An address offset from a base address is shown in bracket form.</td>
    </tr>
</table>

### Register and Field Access Definitions
All the fields that are accessible by user software have distinct access capabilities. Each register table contained in this user guide has an access type defined for each field. The definition of each field access type is presented in [Table 1-1](#table1-1-field-access-definitions).

*Table 1-1: Field Access Definitions*
<a name= "table1-1-field-access-definitions"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td><strong>Access Type</strong></td>
        <td><strong>Description</strong></td>
    </tr>
    <tr>
        <td>RO</td>
        <td><strong>Reserved</strong><br>This access type is reserved for static fields. Reads of this field return the reset value. Writes are ignored.</td>
    </tr>
    <tr>
        <td>DNM</td>
        <td><strong>Reserved. Do Not Modify</strong><br>Software must first read this field and write the same value whenever writing to this register.</td>
    </tr>
    <tr>
        <td>R</td>
        <td><strong>Read Only</strong><br>Reads of this field return a value. Writes to the field do not affect device operation.</td>
    </tr>
    <tr>
        <td>W</td>
        <td><strong>Write Only</strong><br>Reads of this field return indeterminate values. Writes to the field change the field's state to the value written and can affect device operation.
        </td>
    </tr>
    <tr>
        <td>R/W</td>
        <td><strong>Unrestricted Read/Write</strong><br>Reads of this field return a value. Writes to the field change the field's state to the value written and can affect device operation.
        </td>
    </tr>
    <tr>
        <td>RC</td>
        <td><strong>Read to Clear</strong><br>Reading this field clears the field to 0. Writes to the field do not affect device operation.</td>
    </tr>
    <tr>
        <td>RS</td>
        <td><strong>Read to Set</strong><br>Reading this field sets the field to 1. Writes to the field do not affect device operation.</td>
    </tr>
    <tr>
        <td>R/W0</td>
        <td><strong>Read/Write 0 Only</strong><br>Writing 0 to this field sets the field to 0. Writing 1 to the field does not affect device operation.</td>
    </tr>
    <tr>
        <td>R/W1</td>
        <td><strong>Read/Write 1 Only</strong><br>Writing 1 to this field sets the field to 1. Writing 0 to the field does not affect device operation.</td>
    </tr>
    <tr>
        <td>R/W1C</td>
        <td><strong>Read/Write 1 to Clear</strong><br>Writing 1 to this field clears this field to 0. Writing 0 to the field does not affect device operation.</td>
    </tr>
    <tr>
        <td>R/W0S</td>
        <td><strong>Read/Write 0 to Set</strong><br>Writing 0 to this field sets this field to 1. Writing 1 to the field does not affect device operation.</td>
    </tr>
</table>

### Register Lists
Each peripheral includes a table listing all of the peripheral's registers. The register table includes the offset, register name, and description of each register. The offset shown in the table must be added to the peripheral's base address in [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) to get the register's absolute address.

*Table 1-2: Example Registers*
<a name= "table1-2-example-registers"></a>

<table border="1" cellpadding="5" cellspacing="0">
  <tr>
    <td><strong>Offset</strong></td>
    <td><strong>Register Name</strong></td>
    <td><strong>Description</strong></td>
  </tr>
  <tr>
    <td>[0x0000]</td>
    <td>REG_NAME0</td>
    <td>Name 0 Register</td>
  </tr>
</table>

### Register Detail Tables
Each register in a peripheral includes a detailed register table, as shown in [Table 1-3](#table1-3-example-name0-register). The first row of the register detail table includes the register's description, the register's name, and the register's offset from the base peripheral address. The second row of the table is the header for the bit fields represented in the register. The third and subsequent rows of the table include the bit or bit range, the field name, the bit's or field's access, the reset value, and a description of the field. All registers are 32-bits unless specified otherwise. Reserved bits and fields are shown as Reserved in the description column. See [Table 1-1](#table1-1-field-access-definitions) for a list of all access types for each bit and field.

*Table 1-3: Example Name 0 Register*
<a name= "table1-3-example-name0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
  <tr>
    <td colspan="3">Name 0</td>
    <td colspan="1">REG_NAME0</td>
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
    <td>-</td>
    <td><strong>Reserved</strong></td>
  </tr>
  <tr>
    <td>15:0</td>
    <td>field_name</td>
    <td>R/W</td>
    <td>0</td>
    <td><strong>Field name description</strong><br>
    Description of <em>field_name</em>.</td>
  </tr>
</table>