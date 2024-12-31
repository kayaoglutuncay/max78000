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
        <ul>
        <li>Added I2C slave address steps for operating as a slave device. See Slave Mode Operation for details.</li>
        <li> Updated WDT sequence to show 8-bit writes. See WDT Feed Sequence for details. </li>
        <li> Updated WDT window threshold fields and counter fields to indicate watchdog timer must be disabled for changing or reading. See WDTn_CTRL register for details. </li>
        <li> Updated PWM Mode (3) to show PWM diagram. </li>
        <li> Added Compare Mode (5) for wake-up timer. </li>
        <li> Updated DAP interface pins to P0.28 and P0.29 in Table 8-1. </li>
        <li> Marked flash controller registers as only reset on POR. See Flash Registers.
        <li> Removed GCR_RST0.smphr, use GCR_RST1.smphr instead. </li>
        <li> Updated Table 20-1 to show correct WUT_CTRL.pres values for prescalers of 512, 1024, 2048, and 4096. </li>
        <li> Removed package specific information from Table 17-1. </li>
        <li> Fixed conditions in Table 18-2 to show when the RTC_TODA and RTC_SSECA registers are writable. </li>
        <li> Removed GCR_SYSCTRL.flash_bank_flip field. </li>
        <li> Corrected Table 26-1 to match published data sheet. </li>
        </ul>
        </td>
    </tr>

</table>