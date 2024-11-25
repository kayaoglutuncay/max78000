The semaphore peripheral allows multiple cores in a system to cooperate when accessing shared resources. The peripheral contains eight semaphore registers that can be atomically set and cleared. Reading the status field of a semaphore register returns the current state of the status field, and if the field is 0 automatically sets the status to 1. The semaphore status register reflects the state of each of the semaphore’s statuses. The status register enables checking each of the semaphore’s states, but it is not guaranteed that the semaphore status fields cannot change after checking the status register’s value.

It is left to the discretion of the software architect to decide how and when the semaphores are used and how they are allocated. Existing hardware does not have to be modified for this type of cooperative sharing, and the use of semaphores is exclusively within the software domain.

The semaphore peripheral includes two general-purpose mailbox registers which enable communication between the RV32 and CM4 cores. Additionally, either core can generate a semaphore interrupt for either the CM4 or the RV32 providing immediate communication notification through the mailbox registers.

## Instances
There is one instance of the semaphore peripheral, as shown in [Table 9-1](#table9-1-max78000-semaphore-instances).

*Table 9-1: MAX78000 Semaphore Instances*
<a name="table9-1-max78000-semaphore-instances"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
<tr>
    <th>Instance</th>
    <th>Number of Semaphores</th>
</tr>
</thead>
<tbody>
    <tr>
        <td align="center">SEMA</td>
        <td align="center">8</td>
    </tr>
</tbody>
</table>


## Multiprocessor Communications
The semaphore includes support for multicore communications through two mailbox registers and provides the ability to generate an RV32 semaphore interrupt and a CM4 semaphore interrupt.

The mailbox registers, <a href="#semaphore-mailbox0-register">SEMA_MAIL0</a> and <a href="#semaphore-mailbox1-register">SEMA_MAIL1</a>, are general-purpose 32-bit registers. The CM4 and RV32 have read and write access to both registers. The application software should manage how these registers are used to prevent collisions from occurring if both cores attempt to modify the registers simultaneously.

### Reset
Globally reset the semaphore peripheral by setting [GCR_RST1](system-power-clocks-reset.md#reset-register1).*smphr* to 1.

### CM4 Semaphore Interrupt Generation
The <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a> register provides the ability to generate a CM4 semaphore interrupt. Setting the <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a>.<em>cm4_irq</em> bit to 1 and then setting the <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a>.<em>en</em> bit to 1 generates a CM4 semaphore interrupt. The CM4 interrupt handler should write the <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a>.<em>en</em> or the <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a>.<em>cm4_irq</em> field to 0 to clear the interrupt condition.

### RV32 Semaphore Interrupt Generation
The <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a> register provides the ability to generate an RV32 semaphore interrupt. Setting the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a>.<em>rv32_irq</em> bit to 1 and then setting the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a>.<em>en</em> bit to 1 generates an RV32 semaphore interrupt. The RV32 interrupt handler should write the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a>.<em>en</em> or the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a>.<em>rv32_irq</em> field to 0 to clear the interrupt condition.

## Registers
See [Table 3-3](memory-register-mapping-access.md#apb-peripheral-base-address-map) for the base address of this peripheral/module. See [Table 1-1](index.md#table1-1-field-access-definitions) for an explanation of the read and write access of each field. Unless specified otherwise, all fields are reset on a system reset, soft reset, POR, and the peripheral-specific resets.

*Table 9-2: Semaphore Register Summary*
<a name="table9-2-semaphore-register-summary"></a>

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
    <td><a href="#semaphore0-register">SEMA_SEMAPHORES0<a></td>
    <td>Semaphore 0 Register</td>
</tr>
<tr>
    <td>[0x0004]</td>
    <td><a href="#semaphore1-register">SEMA_SEMAPHORES1</a></td>
    <td>Semaphore 1 Register</td>
</tr>
<tr>
    <td>[0x0008]</td>
    <td><a href="#semaphore2-register">SEMA_SEMAPHORES2</a></td>
    <td>Semaphore 2 Register</td>
</tr>
<tr>
    <td>[0x000C]</td>
    <td><a href="#semaphore3-register">SEMA_SEMAPHORES3</a></td>
    <td>Semaphore 3 Register</td>
</tr>
<tr>
    <td>[0x0010]</td>
    <td><a href="#semaphore4-register">SEMA_SEMAPHORES4</a></td>
    <td>Semaphore 4 Register</td>
</tr>
<tr>
    <td>[0x0014]</td>
    <td><a href="#semaphore5-register">SEMA_SEMAPHORES5</a></td>
    <td>Semaphore 5 Register</td>
</tr>
<tr>
    <td>[0x0018]</td>
    <td><a href="#semaphore6-register">SEMA_SEMAPHORES6</a></td>
    <td>Semaphore 6 Register</td>
</tr>
<tr>
    <td>[0x0020]</td>
    <td><a href="#semaphore7-register">SEMA_SEMAPHORES7</a></td>
    <td>Semaphore 7 Register</td>
</tr>
<tr>
    <td>[0x0040]</td>
    <td><a href="#semaphore-interrupt0-register">SEMA_IRQ0</a></td>
    <td>Semaphore Interrupt 0 Register</td>
</tr>
<tr>
    <td>[0x0044]</td>
    <td><a href="#semaphore-mailbox0-register">SEMA_MAIL0</a></td>
    <td>Semaphore Mailbox 0 Register</td>
</tr>
<tr>
    <td>[0x0048]</td>
    <td><a href="#semaphore-interrupt1-register">SEMA_IRQ1</a></td>
    <td>Semaphore Interrupt 1 Register</td>
</tr>
<tr>
    <td>[0x004C]</td>
    <td><a href="#semaphore-mailbox1-register">SEMA_MAIL1</a></td>
    <td>Semaphore Mailbox 1 Register</td>
</tr>
<tr>
    <td>[0x0100]</td>
    <td><a href="#semaphore-status-register">SEMA_STATUS</a></td>
    <td>Semaphore Status Register</td>
</tr>
</tbody>
</table>

### Register Details

*Table 9-3: Semaphore 0 Register*
<a name="semaphore0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 0</td>
       <td colspan="1">SEMA_SEMAPHORES0</td>
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
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status0</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-4: Semaphore 1 Register*
<a name="semaphore1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 1</td>
       <td colspan="1">SEMA_SEMAPHORES1</td>
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
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status1</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-5: Semaphore 2 Register*
<a name="semaphore2-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 2</td>
       <td colspan="1">SEMA_SEMAPHORES2</td>
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
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status2</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-6: Semaphore 3 Register*
<a name="semaphore3-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 3</td>
       <td colspan="1">SEMA_SEMAPHORES3</td>
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
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status3</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-7: Semaphore 4 Register*
<a name="semaphore4-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 4</td>
       <td colspan="1">SEMA_SEMAPHORES4</td>
       <td>[0x0010]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status4</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-8: Semaphore 5 Register*
<a name="semaphore5-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 5</td>
       <td colspan="1">SEMA_SEMAPHORES5</td>
       <td>[0x0014]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status5</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-9: Semaphore 6 Register*
<a name="semaphore6-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 6</td>
       <td colspan="1">SEMA_SEMAPHORES6</td>
       <td>[0x0018]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status6</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-10: Semaphore 7 Register*
<a name="semaphore7-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore 7</td>
       <td colspan="1">SEMA_SEMAPHORES7</td>
       <td>[0x001C]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>status</td>
        <td>*</td>
        <td>0</td>
        <td><strong>Semaphore Status</strong><br>
        Reading this field returns its current value, and if 0, it automatically sets the field to 1. Write 0 to clear this field. Modifications to this field are mirrored in the <a href="#semaphore-status-register">SEMA_STATUS</a>.<em>status7</em> field.
        <div style="margin-left: 20px">
        0: Semaphore is available <br>
        1: Semaphore is taken
        </div>
        </td>
    </tr>
</table>

*Table 9-11: Semaphore Interrupt 0 Register*
<a name="semaphore-interrupt0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore Interrupt 0</td>
       <td colspan="1">SEMA_IRQ0</td>
       <td>[0x0040]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:17</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
   <tr>
       <td>16</td>
       <td>cm4_irq</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>CM4 Interrupt</strong><br>
       The RV32 can use this bit to communicate with the CM4 through the semaphore interrupt. The RV32 generates a semaphore interrupt for the CM4 by setting this field to 1 and also setting the <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a>.<em>en</em> bit to 1. </td>
    </tr>
       <tr>
       <td>15:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Interrupt Enable</strong><br>
        Set this field to enable interrupt generation on semaphore events. <br>
        <div style="margin-left: 20px">
        0: Interrupt disabled <br>
        1: Interrupt enabled
        </div>
        </td>
    </tr>
</table>

*Table 9-12: Semaphore Mailbox 0 Register*
<a name="semaphore-mailbox0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore Mailbox 0</td>
       <td colspan="1">SEMA_MAIL0</td>
       <td>[0x0044]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>data</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Data</strong><br>
       This register is readable and writable by both the CM4 and RV32 cores allowing communication between the two cores. In conjunction with the <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a> register, the RV32 can write data to this register and then notify the CM4 by generating a semaphore interrupt. Alternately, the CM4 can write to this register and then notify the RV32 using the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a> register to generate an RV32 semaphore interrupt event.
       <p><em>Note: The management of the <a href="#semaphore-mailbox0-register">SEMA_MAIL0</a> and <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a> registers is left to application software. It is recommended that one mailbox is used for communication from the CM4 to the RV32, and the other mailbox register is used for communication from the RV32 to the CM4. However, there are no hardware read/write restrictions on the mailbox registers.</em></p>
       </td>
    </tr>
</table>

*Table 9-13: Semaphore Interrupt 1 Register*
<a name="semaphore-interrupt1-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore Interrupt 1</td>
       <td colspan="1">SEMA_IRQ1</td>
       <td>[0x0048]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:17</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
   <tr>
       <td>16</td>
       <td>rv32_irq</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>RV32 Interrupt</strong><br>
       The CM4 can use this bit to communicate with the RV32 through the semaphore interrupt. The CM4 generates a semaphore interrupt for the RV32 by setting this field to 1 and also setting the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a>.<em>en</em> bit to 1. <br>
      <div style="margin-left: 20px">
       0: RV32 interrupt event not active or received by RV32. <br>
       1: RV32 interrupt event is generated when the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a>.<em>en</em> bit is also set to 1.
       </div>
       </td>
    </tr>
       <tr>
       <td>15:1</td>
       <td>-</td>
       <td>RO</td>
       <td>0</td>
       <td><strong>Reserved</strong></td>
    </tr>
    <tr>
        <td>0</td>
        <td>en</td>
        <td>R/W</td>
        <td>0</td>
        <td><strong>Interrupt Enable</strong><br>
        Set this field to generate an RV32 semaphore interrupt when the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a>.<em>rv32_irq</em> is also set to 1. The RV32 should write this bit to 0 when a semaphore interrupt is generated to prevent repeat interrupt generation. <br>
        <div style="margin-left: 20px">
        0: Interrupt disabled <br>
        1: Interrupt enabled
        </div>
        </td>
    </tr>
</table>

*Table 9-14: Semaphore Mailbox 1 Register*
<a name="semaphore-mailbox0-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
   <tr>
       <td colspan="3">Semaphore Mailbox 1</td>
       <td colspan="1">SEMA_MAIL1</td>
       <td>[0x004C]</td>
   </tr>
   <tr>
       <th>Bits</th>
       <th>Name</th>
       <th>Access</th>
       <th>Reset</th>
       <th>Description</th>
   </tr>
   <tr>
       <td>31:0</td>
       <td>data</td>
       <td>R/W</td>
       <td>0</td>
       <td><strong>Data</strong><br>
       This register is readable and writable by both the CM4 and RV32 cores allowing communication between the two cores. In conjunction with the <a href="#semaphore-interrupt0-register">SEMA_IRQ0</a> register, the RV32 can write data to this register and then notify the CM4 by generating a semaphore interrupt. Alternately, the CM4 can write to this register and then notify the RV32 using the <a href="#semaphore-interrupt1-register">SEMA_IRQ1</a> register to generate an RV32 semaphore interrupt event.
       <p><em>Note: The management of the <a href="#semaphore-mailbox0-register">SEMA_MAIL0</a> and <a href="#semaphore-mailbox1-register">SEMA_MAIL1</a> registers is left to application software. It is recommended that one mailbox is used for communication from the CM4 to the RV32, and the other mailbox register is used for communication from the RV32 to the CM4. However, there are no hardware read/write restrictions on the mailbox registers.</em></p>
       </td>
    </tr>
</table>


*Table 9-15: Semaphore Status Register*
<a name="semaphore-status-register"></a>

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td colspan="3">Semaphore Status</td>
        <td>SEMA_STATUS</td>
        <td>[0x0100]</td>
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
        <td>Reserved</td>
    </tr>
    <tr>
        <td>7</td>
        <td>status7</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 7 Status</strong><br>
            This field mirrors the semaphore 7 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore7-register">SEMA_SEMAPHORES7</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore7-register">SEMA_SEMAPHORES7</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
    <tr>
        <td>6</td>
        <td>status6</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 6 Status</strong><br>
            This field mirrors the semaphore 6 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore6-register">SEMA_SEMAPHORES6</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore6-register">SEMA_SEMAPHORES6</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
    <tr>
        <td>5</td>
        <td>status5</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 5 Status</strong><br>
            This field mirrors the semaphore 5 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore5-register">SEMA_SEMAPHORES5</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore5-register">SEMA_SEMAPHORES5</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
    <tr>
        <td>4</td>
        <td>status4</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 4 Status</strong><br>
            This field mirrors the semaphore 4 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore4-register">SEMA_SEMAPHORES4</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore4-register">SEMA_SEMAPHORES4</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>status3</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 3 Status</strong><br>
            This field mirrors the semaphore 3 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore3-register">SEMA_SEMAPHORES3</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore3-register">SEMA_SEMAPHORES3</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>status2</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 2 Status</strong><br>
            This field mirrors the semaphore 2 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore2-register">SEMA_SEMAPHORES2</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore2-register">SEMA_SEMAPHORES2</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>status1</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 1 Status</strong><br>
            This field mirrors the semaphore 1 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore1-register">SEMA_SEMAPHORES1</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore1-register">SEMA_SEMAPHORES1</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
    <tr>
        <td>0</td>
        <td>status0</td>
        <td>R</td>
        <td>0</td>
        <td>
            <strong>Semaphore 0 Status</strong><br>
            This field mirrors the semaphore 0 status field. Reads from this field do not affect the corresponding semaphore’s status field.<br>
            <div style="margin-left: 20px;">
                0: <a href="#semaphore0-register">SEMA_SEMAPHORES0</a>.<em>status</em> is 0<br>
                1: <a href="#semaphore0-register">SEMA_SEMAPHORES0</a>.<em>status</em> is 1
            </div>
        </td>
    </tr>
</table>

