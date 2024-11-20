Interrupts and exceptions are managed by either the Arm Cortex-M4 with FPU NVIC or the RV32 interrupt controller. The NVIC manages the interrupts, exceptions, priorities, and masking. <a href="#table5-1-max78000-cm4-interrupt-vector-table">Table 5-1</a> and <a href="#table5-2-max78000-rv32-interrupt-vector-table">Table 5‑2</a> detail the MAX78000’s interrupt vector tables for the CM4 and RV32 processors, respectively, and describe each exception and interrupt.

## CM4 Interrupt and Exception Features
- 8 programmable priority levels
- Nested exception and interrupt support
- Interrupt masking

## CM4 Interrupt Vector Table
<a href="#table5-1-max78000-cm4-interrupt-vector-table">Table 5-1</a> lists the interrupt and exception table for the MAX78000’s CM4 core. There are 119 interrupt entries for the MAX78000, including reserved for future use interrupt placeholders. Including the 15 system exceptions for the Arm Cortex-M4 with FPU, the total number of entries is 134.

*Table 5-1: MAX78000 CM4 Interrupt Vector Table*
<a name="table5-1-max78000-cm4-interrupt-vector-table"></a>

<table border="1" cellpadding="5" cellspacing="0">
  <tr>
    <th>Exception <br>(Interrupt) Number</th>
    <th>Offset</th>
    <th>Name</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>1</td>
    <td>[0x0004]</td>
    <td>Reset_IRQn</td>
    <td>Reset</td>
  </tr>
  <tr>
    <td>2</td>
    <td>[0x0008]</td>
    <td>NonMaskableInt_IRQn</td>
    <td>Non-Maskable Interrupt</td>
  </tr>
  <tr>
    <td>3</td>
    <td>[0x000C]</td>
    <td>HardFault_IRQn</td>
    <td>Hard Fault</td>
  </tr>
  <tr>
    <td>4</td>
    <td>[0x0010]</td>
    <td>MemoryManagement_IRQn</td>
    <td>Memory Management Fault</td>
  </tr>
  <tr>
    <td>5</td>
    <td>[0x0014]</td>
    <td>BusFault_IRQn</td>
    <td>Bus Fault</td>
  </tr>
  <tr>
    <td>6</td>
    <td>[0x0018]</td>
    <td>UsageFault_IRQn</td>
    <td>Usage Fault</td>
  </tr>
  <tr>
    <td>7:10</td>
    <td>[0x001C]-[0x0028]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>11</td>
    <td>[0x002C]</td>
    <td>SVCall_IRQn</td>
    <td>Supervisor Call Exception</td>
  </tr>
  <tr>
    <td>12</td>
    <td>[0x0030]</td>
    <td>DebugMonitor_IRQn</td>
    <td>Debug Monitor Exception</td>
  </tr>
  <tr>
    <td>13</td>
    <td>[0x0034]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>14</td>
    <td>[0x0038]</td>
    <td>PendSV_IRQn</td>
    <td>Request Pending for System Service</td>
  </tr>
  <tr>
    <td>15</td>
    <td>[0x003C]</td>
    <td>SysTick_IRQn</td>
    <td>System Tick Timer</td>
  </tr>
  <tr>
    <td>16</td>
    <td>[0x0040]</td>
    <td>PF_IRQn</td>
    <td>Power Fail interrupt</td>
  </tr>
  <tr>
    <td>17</td>
    <td>[0x0044]</td>
    <td>WDT0_IRQn</td>
    <td>Windowed Watchdog Timer 0 Interrupt</td>
  </tr>
  <tr>
    <td>18</td>
    <td>[0x0048]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>19</td>
    <td>[0x004C]</td>
    <td>RTC_IRQn</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>20</td>
    <td>[0x0050]</td>
    <td>TRNG_IRQn</td>
    <td>True Random Number Generator Interrupt</td>
  </tr>
  <tr>
    <td>21</td>
    <td>[0x0054]</td>
    <td>TMR0_IRQn</td>
    <td>Timer 0 Interrupt</td>
  </tr>
  <tr>
    <td>22</td>
    <td>[0x0058]</td>
    <td>TMR1_IRQn</td>
    <td>Timer 1 Interrupt</td>
  </tr>
  <tr>
    <td>23</td>
    <td>[0x005C]</td>
    <td>TMR2_IRQn</td>
    <td>Timer 2 Interrupt</td>
  </tr>
  <tr>
    <td>24</td>
    <td>[0x0060]</td>
    <td>TMR3_IRQn</td>
    <td>Timer 3 Interrupt</td>
  </tr>
  <tr>
    <td>25</td>
    <td>[0x0064]</td>
    <td>TMR4_IRQn</td>
    <td>Timer 4 (LPTMR0) Interrupt</td>
  </tr>
  <tr>
    <td>26</td>
    <td>[0x0068]</td>
    <td>TMR5_IRQn</td>
    <td>Timer 5 (LPTMR1) Interrupt</td>
  </tr>
  <tr>
    <td>27:28</td>
    <td>[0x006C]:[0x0070]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>29</td>
    <td>[0x0074]</td>
    <td>I2C0_IRQn</td>
    <td>I<sup>2</sup>C Port 0 Interrupt</td>
  </tr>
  <tr>
    <td>30</td>
    <td>[0x0078]</td>
    <td>UART0_IRQn</td>
    <td>UART Port 0 Interrupt</td>
  </tr>
  <tr>
    <td>31</td>
    <td>[0x007C]</td>
    <td>UART1_IRQn</td>
    <td>UART Port 1 Interrupt</td>
  </tr>
  <tr>
    <td>32</td>
    <td>[0x0080]</td>
    <td>SPI1_IRQn</td>
    <td>SPI Port 1 Interrupt</td>
  </tr>
  <tr>
    <td>33:35</td>
    <td>[0x0084]:[0x008C]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>36</td>
    <td>[0x90]</td>
    <td>ADC_IRQn</td>
    <td>ADC Interrupt</td>
  </tr>
  <tr>
    <td>37:38</td>
    <td>[0x0094]:[0x0098]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>39</td>
    <td>[0x009C]</td>
    <td>FLC0_IRQn</td>
    <td>Flash Controller 0 Interrupt</td>
  </tr>
  <tr>
    <td>40</td>
    <td>[0x00A0]</td>
    <td>GPIO0_IRQn</td>
    <td>GPIO Port 0 Interrupt</td>
  </tr>
  <tr>
    <td>41</td>
    <td>[0x00A4]</td>
    <td>GPIO1_IRQn</td>
    <td>GPIO Port 1 Interrupt</td>
  </tr>
  <tr>
    <td>42</td>
    <td>[0x00A8]</td>
    <td>GPIO2_IRQn</td>
    <td>GPIO Port 2 Interrupt</td>
  </tr>
  <tr>
    <td>43</td>
    <td>[0x00AC]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>44</td>
    <td>[0x00B0]</td>
    <td>DMA0_IRQn</td>
    <td>DMA0 Interrupt</td>
  </tr>
  <tr>
    <td>45</td>
    <td>[0x00B4]</td>
    <td>DMA1_IRQn</td>
    <td>DMA1 Interrupt</td>
  </tr>
  <tr>
    <td>46</td>
    <td>[0x00B8]</td>
    <td>DMA2_IRQn</td>
    <td>DMA2 Interrupt</td>
  </tr>
  <tr>
    <td>47</td>
    <td>[0x00BC]</td>
    <td>DMA3_IRQn</td>
    <td>DMA3 Interrupt</td>
  </tr>
  <tr>
    <td>48:49</td>
    <td>[0x00C0 : 0x00C4]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
  <tr>
    <td>50</td>
    <td>[0x00C8]</td>
    <td>UART2_IRQn</td>
    <td>UART Port 2 Interrupt</td>
  </tr>
  <tr>
    <td>51</td>
    <td>[0x00CC]</td>
    <td>-</td>
    <td>Reserved</td>
  </tr>
<tr>
<td>52</td>
<td>[0x00D0]</td>
<td>I2C1_IRQn</td>
<td>I<sup>2</sup>C Port 1 Interrupt</td>
</tr>
<tr>
<td>53:68</td>
<td>[0x00D4]: [0x0110]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>69</td>
<td>[0x0114]</td>
<td>WUT_IRQn</td>
<td>Wakeup Timer Interrupt</td>
</tr>
<tr>
<td>70</td>
<td>[0x0118]</td>
<td>GPIOWAKE_IRQn</td>
<td>GPIO Wakeup Interrupt</td>
</tr>
<tr>
<td>71</td>
<td>[0x011C]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>72</td>
<td>[0x0120]</td>
<td>SPI0_IRQn</td>
<td>SPI Port 0 Interrupt</td>
</tr>
<tr>
<td>73</td>
<td>[0x0124]</td>
<td>WDT1_IRQn</td>
<td>Low Power Watchdog Timer 0 (WDT1) Interrupt</td>
</tr>
<tr>
<td>74</td>
<td>[0x0128]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>75</td>
<td>[0x012C]</td>
<td>PT_IRQn</td>
<td>Pulse Train Interrupt</td>
</tr>
<tr>
<td>76:77</td>
<td>[0x0130]:[0x0134]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>78</td>
<td>[0x0138]</td>
<td>I2C2_IRQn</td>
<td>I<sup>2</sup>C Port 2 Interrupt</td>
</tr>
<tr>
<td>79</td>
<td>[0x013C]</td>
<td>RISCV_IRQn</td>
<td>CPU1 (RV32) Interrupt</td>
</tr>
<tr>
<td>80:82</td>
<td>[0x0140]:[0x0148]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>83</td>
<td>[0x014C]</td>
<td>OWM_IRQn</td>
<td>1-Wire Master Interrupt</td>
</tr>
<tr>
<td>84:97</td>
<td>[0x0150]:[0x0184]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>98</td>
<td>[0x0188]</td>
<td>ECC_IRQn</td>
<td>Error Correction Coding Block Interrupt</td>
</tr>
<tr>
<td>99</td>
<td>[0x018C]</td>
<td>DVS_IRQn</td>
<td>Digital Voltage Scaling Interrupt</td>
</tr>
<tr>
<td>100</td>
<td>[0x0190]</td>
<td>SIMO_IRQn</td>
<td>Single Input Multiple Output Interrupt</td>
</tr>
<tr>
<td>101:103</td>
<td>[0x0194]:[0x019C]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>104</td>
<td>[0x01A0}</td>
<td>UART3_IRQn</td>
<td>UART3 (LPUART0) Interrupt</td>
</tr>
<tr>
<td>105:106</td>
<td>[0x01A4]:[0x01A8]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>107</td>
<td>[0x01AC]</td>
<td>PCIF_IRQn</td>
<td>Parallel Camera Interface Interrupt</td>
</tr>
<tr>
<td>108:112</td>
<td>[0x01B0]:[0x01C0]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>113</td>
<td>[0x01C4]</td>
<td>AES_IRQn</td>
<td>AES Interrupt</td>
</tr>
<tr>
<td>114</td>
<td>[0x01C8]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>115</td>
<td>[0x01CC]</td>
<td>I2S_IRQn</td>
<td>I<sup>2</sup>S Interrupt</td>
</tr>
<tr>
<td>116</td>
<td>[0x01D0]</td>
<td>CNN_FIFO_IRQn</td>
<td>CNN FIFO Interrupt</td>
</tr>
<tr>
<td>117</td>
<td>[0x01D4]</td>
<td>CNN_IRQn</td>
<td>CNN Interrupt</td>
</tr>
<tr>
<td>118</td>
<td>[0x01D8]</td>
<td>-</td>
<td>Reserved</td>
</tr>
<tr>
<td>119</td>
<td>[0x01DC]</td>
<td>LPCMP_IRQn</td>
<td>Low Power Comparator Interrupt</td>
</tr>
</tbody>
</table>

## RV32 Interrupt Vector Table
<a href="#table5-2-max78000-rv32-interrupt-vector-table">Table 5‑2</a> lists the interrupt and exception table for the MAX78000’s RV32 core.

*Table 5-2: MAX78000 RV32 Interrupt Vector Table*
<a name="table5-2-max78000-rv32-interrupt-vector-table"></a>

<table border="1" cellpadding="5" cellspacing="0">
<thead>
  <tr>
    <th>Exception<br /> (Interrupt) Number</th>
    <th>Name</th>
    <th>Description</th>
  </tr>
</thead>

<tbody>
<tr>
  <td>4</td>
  <td>PF_IRQn</td>
  <td>Power Fail/System Fault/CM4/Bus Fault</td>
</tr>
<tr>
  <td>5</td>
  <td>WDT0_IRQn</td>
  <td>Windowed Watchdog Timer 0 Interrupt</td>
</tr>
<tr>
  <td>6</td>
  <td>GPIOWAKE_IRQn</td>
  <td>GPIO Wakeup Interrupt</td>
</tr>
<tr>
  <td>7</td>
  <td>RTC_IRQn</td>
  <td>RTC Interrupt</td>
</tr>
<tr>
  <td>8</td>
  <td>TMR0_IRQn</td>
  <td>Timer 0 Interrupt</td>
</tr>
<tr>
  <td>9</td>
  <td>TMR1_IRQn</td>
  <td>Timer 1 Interrupt</td>
</tr>
<tr>
  <td>10</td>
  <td>TMR2_IRQn</td>
  <td>Timer 2 Interrupt</td>
</tr>
<tr>
  <td>11</td>
  <td>TMR3_IRQn</td>
  <td>Timer 3 Interrupt</td>
</tr>
<tr>
  <td>12</td>
  <td>TMR4_IRQn</td>
  <td>Timer 4 (LPTMR0) Interrupt</td>
</tr>
<tr>
  <td>13</td>
  <td>TMR5_IRQn</td>
  <td>Timer 5 (LPTMR1) Interrupt</td>
</tr>
<tr>
  <td>14</td>
  <td>I2C0_IRQn</td>
  <td>I<sup>2</sup>C Port 0 Interrupt</td>
</tr>
<tr>
  <td>15</td>
  <td>UART0_IRQn</td>
  <td>UART Port 0 Interrupt</td>
</tr>
<tr>
  <td>16</td>
  <td>-</td>
  <td>Reserved</td>
</tr>
<tr>
  <td>17</td>
  <td>I2C1_IRQn</td>
  <td>I<sup>2</sup>C Port 1 Interrupt</td>
</tr>
<tr>
  <td>18</td>
  <td>UART1_IRQn</td>
  <td>UART Port 1 Interrupt</td>
</tr>
<tr>
  <td>19</td>
  <td>UART2_IRQn</td>
  <td>UART Port 2 Interrupt</td>
</tr>
<tr>
  <td>20</td>
  <td>I2C2_IRQn</td>
  <td>I<sup>2</sup>C Port 2 Interrupt</td>
</tr>
<tr>
  <td>21</td>
  <td>UART3_IRQn</td>
  <td>UART3 (LPUART0) Interrupt</td>
</tr>
<tr>
  <td>22</td>
  <td>SPI1_IRQn</td>
  <td>SPI Port 1 Interrupt</td>
</tr>
<tr>
  <td>23</td>
  <td>WUT_IRQn</td>
  <td>Wakeup Timer Interrupt</td>
</tr>
<tr>
  <td>24</td>
  <td>FLC0_IRQn</td>
  <td>Flash Controller 0 Interrupt</td>
</tr>
<tr>
  <td>25</td>
  <td>GPIO0_IRQn</td>
  <td>GPIO Port 0 Interrupt</td>
</tr>
<tr>
  <td>26</td>
  <td>GPIO1_IRQn</td>
  <td>GPIO Port 1 Interrupt</td>
</tr>
<tr>
  <td>27</td>
  <td>GPIO2_IRQn</td>
  <td>GPIO Port 2 Interrupt</td>
</tr>
<tr>
  <td>28</td>
  <td>DMA0_IRQn</td>
  <td>DMA0 Interrupt</td>
</tr>
<tr>
  <td>29</td>
  <td>DMA1_IRQn</td>
  <td>DMA1 Interrupt</td>
</tr>
<tr>
  <td>30</td>
  <td>DMA2_IRQn</td>
  <td>DMA2 Interrupt</td>
</tr>
<tr>
  <td>31</td>
  <td>DMA3_IRQn</td>
  <td>DMA3 Interrupt</td>
</tr>
<tr>
  <td>32:45</td>
  <td>-</td>
  <td>Reserved</td>
</tr>
<tr>
  <td>46</td>
  <td>AES_IRQn</td>
  <td>AES Interrupt</td>
</tr>
<tr>
  <td>47</td>
  <td>TRNG_IRQn</td>
  <td>TRNG Interrupt</td>
</tr>
<tr>
  <td>48</td>
  <td>WDT1_IRQn</td>
  <td>Watchdog Timer 1 (LPWDT0) Interrupt</td>
</tr>
<tr>
  <td>49</td>
  <td>DVS_IRQn</td>
  <td>Digital Voltage Scaling Interrupt</td>
</tr>
<tr>
  <td>50</td>
  <td>SIMO_IRQn</td>
  <td>Single Input Multiple Output Interrupt</td>
</tr>
<tr>
  <td>51</td>
  <td>-</td>
  <td>Reserved</td>
</tr>
<tr>
  <td>52</td>
  <td>PT_IRQn</td>
  <td>Pulse Train Interrupt</td>
</tr>
<tr>
  <td>53</td>
  <td>ADC_IRQn</td>
  <td>ADC Interrupt</td>
</tr>
<tr>
  <td>54</td>
  <td>OWM_IRQn</td>
  <td>1-Wire Master Interrupt</td>
</tr>
<tr>
  <td>55</td>
  <td>I2S_IRQn</td>
  <td>I<sup>2</sup>S Interrupt</td>
</tr>
<tr>
  <td>56</td>
  <td>CNN_FIFO_IRQn</td>
  <td>CNN TX FIFO Interrupt</td>
</tr>
<tr>
  <td>57</td>
  <td>CNN_IRQn</td>
  <td>CNN Interrupt</td>
</tr>
<tr>
  <td>58</td>
  <td>-</td>
  <td>Reserved</td>
</tr>
<tr>
  <td>59</td>
  <td>PCIF_IRQn</td>
  <td>Parallel Camera Interface Interrupt</td>
</tr>
</tbody>
</table>
