# Revision History

## Online Document Revision History

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td>REVISION NUMBER</td>
        <td>REVISION DATE</td>
        <td>DESCRIPTION</td>
    </tr>
</table>

## Offline Document Revision History

<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td>REVISION NUMBER</td>
        <td>REVISION DATE</td>
        <td>DESCRIPTION</td>
    </tr>
    <tr>
        <td>0</td>
        <td>07/2021</td>
        <td>Initial release</td>
    </tr>
    <tr>
        <td>1</td>
        <td>03/2024</td>
        <td>
        Added I2C slave address steps for operating as a slave device. See Slave Mode Operation for details. <br>
        Updated WDT sequence to show 8-bit writes. See WDT Feed Sequence for details. <br>
        Updated WDT window threshold fields and counter fields to indicate watchdog timer must be disabled for changing or reading. See WDTn_CTRL register for details. <br>
        Updated PWM Mode (3) to show PWM diagram. <br>
        Added Compare Mode (5) for wake-up timer. <br>
        Updated DAP interface pins to P0.28 and P0.29 in Table 8-1. <br>
        Marked flash controller registers as only reset on POR. See Flash Registers. <br>
        Removed GCR_RST0.smphr, use GCR_RST1.smphr instead. <br>
        Updated Table 20-1 to show correct WUT_CTRL.pres values for prescalers of 512, 1024, 2048, and 4096. <br>
        Removed package specific information from Table 17-1. <br>
        Fixed conditions in Table 18-2 to show when the RTC_TODA and RTC_SSECA registers are writable. <br>
        Removed GCR_SYSCTRL.flash_bank_flip field. <br>
        Corrected Table 26-1 to match published data sheet.
        </td>
    </tr>

</table>