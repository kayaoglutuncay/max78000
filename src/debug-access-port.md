The device provides an Arm DAP that supports debugging during application development. The DAP enables an external debugger to access the device. The DAP is a standard Arm CoreSight™ serial wire debug port and uses a two-pin serial interface (SWDCLK and SWDIO) to communicate.

## Instances
The DAP interface communicates through the serial wire debug (SWD), shown in <a href="#table8-1-max78000-dap-instances">Table 8-1</a>.

*Table 8-1: MAX78000 DAP Instances*
<a name="table8-1-max78000-dap-instances"></a>

<table border="1" cellpadding="5" cellspacing="0">
  <tr>
    <th>Instance</th>
    <th>Pin</th>
    <th>Alternate Function</th>
    <th>SWD Signal</th>
  </tr>
  <tr>
    <td rowspan="2">DAP</td>
    <td>P0.28</td>
    <td>AF1</td>
    <td>SWDIO</td>
  </tr>
  <tr>
    <td>P0.29</td>
    <td>AF1</td>
    <td>SWDCLK</td>
  </tr>
</table>

## Access Control
### Factory Disabled DAP
Device versions that do not provide a DAP interface have [GCR_SYSST](system-power-clocks-reset.md#system-status-flags).*icelock* = 1 set at the factory, permanently disabling the DAP interface. No software action is needed to disable the DAP on these devices.

### Software Accessible DAP
Device versions that provide a DAP ([GCR_SYSST](system-power-clocks-reset.md#system-status-flags).*icelock* = 0) always have their interface(s) enabled and running unless the software explicitly sets the [GCR_SYSST](system-power-clocks-reset.md#system-status-flags).*swd_dis* field to 1. The read-only field [GCR_SYSST](system-power-clocks-reset.md#system-status-flags).*icelock* is cleared to 0, and the software has read and write access to the [GCR_SYSST](system-power-clocks-reset.md#system-status-flags).*swd_dis* field. The [GCR_SYSST](system-power-clocks-reset.md#system-status-flags).*swd_dis* field resets to 0 after every POR to allow access to the DAP during development.

The software can disable the DAP by setting the [GCR_SYSST](system-power-clocks-reset.md#system-status-flags).*swd_dis* field to 1. The only practical application for disabling the DAP is to release the interface pins to operate as standard GPIO or in one of the supported alternate function modes in a development environment. Customers can use device versions with the DAP enabled for development but should only use device versions with the factory disabled DAP in a final product.

## Pin Configuration
SWD signals in GPIO and alternate function matrices determine which GPIO pins are associated with a signal. It is unnecessary to configure a pin for an alternate function to use the DAP following a POR.

By default, the pin associated with the bidirectional SWDIO signal is configured as a GPIO high-impedance input after any POR. While the DAP is in use, a pullup resistor should be connected to the SWDIO pin, as shown in <a href="#table8-1-max78000-dap-instances">Table 8-1</a>. The pullup ensures the signal is in a known state when control of the SWDIO pin is transferred between the host and target. The pullup resistor should be removed if the associated pin is used as a GPIO to avoid unnecessary current consumption.