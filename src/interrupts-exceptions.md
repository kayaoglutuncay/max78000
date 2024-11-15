Interrupts and exceptions are managed by either the Arm Cortex-M4 with FPU NVIC or the RV32 interrupt controller. The NVIC manages the interrupts, exceptions, priorities, and masking. Table 5-1 and Table 5-2 detail the MAX78000’s interrupt vector tables for the CM4 and RV32 processors, respectively, and describe each exception and interrupt.

## CM4 Interrupt and Exception Features
- 8 programmable priority levels
- Nested exception and interrupt support
- Interrupt masking

## CM4 Interrupt Vector Table
Table 5-1 lists the interrupt and exception table for the MAX78000’s CM4 core. There are 119 interrupt entries for the MAX78000, including reserved for future use interrupt placeholders. Including the 15 system exceptions for the Arm Cortex-M4 with FPU, the total number of entries is 134.

*Table 5-1: MAX78000 CM4 Interrupt Vector Table*
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

</table>
