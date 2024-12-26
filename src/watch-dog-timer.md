The watchdog timer (WDT) protects against corrupt or unreliable
software, power faults, and other system-level problems which may place
the IC into an improper operating state. The software must periodically
write a special sequence to a dedicated register to confirm the software
is operating correctly. Failure to reset the watchdog timer within a
user-specified time frame can first generate an interrupt allowing the
software the opportunity to identify and correct the problem. If the
software cannot regain normal operation, the watchdog timer can generate
a system reset as a last resort.

Some instances provide a windowed timer function. These instances
support an additional feature that can detect watchdog timer resets that
occur too early and too late (or never). This could happen if program
execution is corrupted and is accidentally forced into a tight loop of
code that contains a watchdog sequence. This would not be detected with
a traditional WDT because the end of the timeout periods would never be
reached. A new set of \"watchdog timer early\" fields are available to
support the lower limits required for windowing. Traditional watchdog
timers can only detect a loss of program control that fails to reset the
watchdog timer.

Each time the software performs a reset, as early as possible in the
software, the peripheral control register should be examined to
determine if the reset was caused by at WDT late reset event (or WDT
early reset event if the window function is supported). If so, the
software should take the desired action as part of its restart sequence.

The WDT is a critical safety feature, and most fields are reset on POR
or system reset events only.

Features:

-   Single-ended (legacy) watchdog timeout.

-   Windowed mode adds lower-limit timeout settings to detect loss of
    control in tight code loops.

-   Configurable clock source.

-   Configurable time-base.

-   Programmable upper and lower limits for reset and interrupts from
    2^16^ to 2^32^ time-base ticks.

-   A register is provided to read the WDT counter register, simplifying
    code development.

*Figure 21‑1* shows the block diagram of the WDT.

<figure>
<img src="media/image1.emf" />
<figcaption><p>: Windowed Watchdog Timer Block Diagram</p></figcaption>
</figure>

##  Instances

*Table 21‑1* shows the peripheral instances, available clock sources,
and indicates which instances support the windowed watchdog
functionality.

  ----------------------------------------------------------------------------
  Instance   Register     Window        External    CLK0     CLK1     CLK2
             Access\      Support       Clock                         
             Name                                                     
  ---------- ------------ ------------- ----------- -------- -------- --------
  WDT0       WDT0         Yes           N/A         PCLK     IBRO     \-

  LPWDT0     WDT1         Yes           N/A         IBRO     ERTC0    INRO
  ----------------------------------------------------------------------------

  : : MAX78000 WDT Instances Summary

### Usage

When enabled, the *WDTn_CNT*.*count* increments once every *t~WDTCLK~*
period. The software periodically executes the feed sequence during
correct operation and resets *WDTn_CNT*.*count* to 0x0000 0000 within
the target window.

The upper and lower limits of the target window are user-configurable to
accommodate different applications and non-deterministic execution times
within an application.

The WDT can generate an interrupt and a reset event in response to the
WDT activity. Interrupts are typically configured to respond first to an
event outside the target window. The approach is that a minor system
event may have temporarily delayed the execution of the feed sequence,
so the event can be diagnosed in an interrupt routine and control
returned to the system. When the WDT feed sequence occurs much earlier
than expected or not at all, a reset event can be generated that forces
the system to a known good state before continuing.

Traditional WDTs only detect execution errors that fail to perform the
WDT feed sequence. If the counter reaches the WDT late interrupt
threshold, the device attempts to regain program control by vectoring to
the dedicated WDT ISR. The ISR should reset the WDT counter, perform the
desired recovery activity, and return execution to a known good address.

If the execution error prevents the successful execution of the ISR, the
WDT continues to increment until the count reaches the WDT late reset
threshold. The WDT generates a late reset event which sets the WDT late
reset flag and generates a system interrupt.

Instances that support the window feature (*WDTn_CTRL*.*win_en* = 1) can
generate a WDT early interrupt event if the WDT feed sequence occurs
earlier than expected. Analogously, the device attempts to regain
program control by vectoring to the dedicated WDT interrupt. The WDT
interrupt should reset the WDT counter, perform the desired recovery
activity, and return execution to a known good address.

A WDT feed sequence that occurs earlier than the WDT early reset
threshold indicates the execution error is significant enough to
initiate a reset of the device to correct the problem. The WDT generates
an early reset event that sets the WDT late reset flag and generates a
system interrupt.

The event flags are set regardless of the corresponding interrupt or
reset enable. This includes the early interrupt and early event flags,
even if the WDT is disabled (*WDTn_CTRL.win_en* = 0).

#### Using the WDT as a Long-Interval Timer

One use case of the WDT is as a very long interval timer in *ACTIVE*.
The timer can be configured to generate a WDT late interrupt event for
as long as 2^32^ periods of the selected watchdog clock source. The WDT
should not be enabled to generate WDT reset events in this use case.

#### Using the WDT as a Long-Interval Wake-Up Timer

The WDT can be used as a very long internal wake-up source from *SLEEP*.
Additionally, the low power WDT can be used as a wake-up source from
*SLEEP*, *LPM*, and *UPM*. The timer can be configured to generate a WDT
wake-up event for as long as 2^32^ periods of the selected watchdog
clock source.

### WDT Feed Sequence

The WDT feed sequence protects the system against unintentional altering
of the WDT count and unintentionally enabling or disabling the timer.

Two consecutive write instructions to the *WDTn_RST*.*reset* field are
required to reset the *WDTn_CNT*.*count* = 0. Global interrupts should
be disabled immediately before and re-enabled after the writes to ensure
both writes to the *WDTn_RST*.*reset* field complete without
interruption.

The feed sequence must also be performed immediately before enabling the
WDT to prevent accidental triggering of the reset or interrupt as soon
as the timer is enabled. There is no timed access window for these write
operations; the operations can be separated by any length of time as
long as they occur in the required sequence

1.  1\. Disable interrupts.

2.  2\. In consecutive write operations:

    1.  a\. Write *WDTn_RST*.*reset*: 0xA5.

        b\. Write *WDTn_RST*.*reset*: 0x5A.

3.  3\. If desired, enable or disable the timer.

4.  4\. Re-enable interrupts.

### WDT Events

Multiple events are supported, as shown in *Table 21‑2*. The
corresponding event flag is set when the event occurs.

Typically the system is configured such that the late interrupt events
occur before the late reset events, and early interrupts occur when the
feed sequence has the least error from the target time, before the early
reset events.

The event flags are set even if the corresponding interrupt or reset
enable flags regardless of the state of the corresponding enable fields.
This includes the early interrupt and early event flags, even if
*WDTn_CTRL.win_en* = 0.

Software must clear all event flags before enabling the timers.

+----------+-----------------------------+--------------+--------------+
| Event    | Condition                   | Local        | Local        |
|          |                             | Interrupt    | Interrupt    |
|          |                             | Event Flag   | Event Enable |
+==========+=============================+==============+==============+
| Early    | Feed sequence occurs while  | *WDTn_CTR    | *WDTn_CTRL   |
| I        |                             | L.int_early* | .wdt_int_en* |
| nterrupt | *WDTn_CTRL.rst_early_val* ≤ |              |              |
|          | *WDTn_CNT*.*count* \<       |              |              |
|          | *WDTn_CTRL.int_early_val*   |              |              |
|          |                             |              |              |
|          | *WDTn_CTRL.win_en = 1*      |              |              |
+----------+-----------------------------+--------------+--------------+
| Early    | Feed sequence occurs while  | *WDTn_CTR    | *WDTn_CTRL   |
| Reset    |                             | L.rst_early* | .wdt_rst_en* |
|          | *WDTn_CNT*.*count* \<       |              |              |
|          | *WDTn_CTRL.rst_early_val*   |              |              |
|          |                             |              |              |
|          | *WDTn_CTRL.win_en = 1*      |              |              |
+----------+-----------------------------+--------------+--------------+
| I        | *WDTn_CNT*.*count* =        | *WDTn_CT     | *WDTn_CTRL   |
| nterrupt | *WDTn_CTRL.int_late_val*    | RL.int_late* | .wdt_int_en* |
| Late     |                             |              |              |
+----------+-----------------------------+--------------+--------------+
| Reset    | *WDTn_CNT*.*count* =        | *WDTn_CT     | *WDTn_CTRL   |
| Late     | *WDTn_CTRL.rst_late_val*    | RL.rst_late* | .wdt_rst_en* |
+----------+-----------------------------+--------------+--------------+
| Timer    | *WDTn_CTRL.**clkrdy* 0 → 1  | No event     |              |
| Enabled  |                             | flags are    |              |
|          |                             | set by a     |              |
|          |                             | timer        |              |
|          |                             | enabled      |              |
|          |                             | event        |              |
+----------+-----------------------------+--------------+--------------+

: : MAX78000 WDT Event Summary

#### WDT Early Reset

The early reset event occurs if the software performs the WDT feed
sequence while *WDTn_CNT*.*count* \< *WDTn_CTRL.rst_late_val* threshold
as shown in *Table 21‑2*. *Figure 21‑2* shows the sequencing details
associated with an early reset event.

: WDT Early Interrupt and Reset Event Sequencing Details

![](media/image2.emf)

The following occurs when a WDT early reset event occurs:

5.  1\. The hardware sets *WDTn_CTRL.rst_early* to 1.

6.  2\. The hardware initiates a system reset:

7.  3\. The hardware resets *WDTn_CNT*.*count* to 0x0000 0000 during the
    reset event.

8.  4\. The *WDTn_CTRL.en* field is unaffected by a system reset. The
    WDT continues incrementing.

9.  5\. The *WDTn_CTRL.rst_early* field is unaffected by a system reset,
    allowing the software to determine an early reset event occurred.

#### WDT Early Interrupt

The early interrupt event occurs if the software performs the WDT feed
sequence while
*WDTn_CTRL.rst_early_val* ≤ *WDTn_CNT*.*count* \< *WDTn_CTRL.int_early_val*
as shown in *Table 21‑2*. *Figure 21‑2* shows the sequencing details
associated with an early interrupt event, including the required
functions performed by the WDT ISR.

The hardware performs the following when a WDT early interrupt event
occurs:

10. 1\. The *WDTn_CTRL.int_early* field is set to 1.

11. 2\. An interrupt occurs if enabled.

#### WDT Late Reset

The late reset event occurs if the counter increments where
*WDTn_CNT*.*count* is equal to the *WDTn_CTRL.rst_late_val* threshold,
as shown in *Table 21‑2*. *Figure 21‑3* shows the sequencing details
associated with a late reset event.

<figure>
<img src="media/image3.emf" />
<figcaption><p>: WDT Late Interrupt and Reset Event Sequencing
Details</p></figcaption>
</figure>

The following occurs when a WDT late reset event occurs:

12. 1\. The hardware sets *WDTn_CTRL.rst_late* to 1.

13. 2\. The hardware initiates a system reset:

14. 3\. The hardware resets *WDTn_CNT*.*count* to 0x0000 0000 during the
    reset event.

15. 4\. The *WDTn_CTRL.en* field is unaffected by a system reset. The
    WDT continues incrementing.

16. 5\. The *WDTn_CTRL.rst_late* field is unaffected by a system reset.

#### WDT Late Interrupt

The late reset event occurs if the counter increments to the point where
*WDTn_CNT*.*count* = *WDTn_CTRL.int_late_val* threshold, as shown in
*Table 21‑2*. *Figure 21‑3* shows the sequencing details associated with
a late interrupt event, including the required functions performed by
the WDT interrupt handler.

The following occurs when WDT late interrupt event occurs:

17. 1\. The hardware sets *WDTn_CTRL.int_late* to 1.

18. 2\. The hardware initiates an interrupt if enabled.

### Initializing the WDT

The full procedure for configuring the WDT is shown below:

19. 1\. Execute the WDT feed sequence and disable the WDT:

    1.  a\. Disable global interrupts

        b\. Write *WDTn_RST.reset* to 0xA5.

        c\. Write *WDTn_RST.reset* to 0x5A. Hardware will reset
        *WDTn_CNT*.*count* = 0x0000 0000.

        d\. Set *WDTn_CTRL.en* to 0 to disable the WDT.

20. 2\. Verify the peripheral is disabled before proceeding:

    1.  a\. Poll *WDTn_CTRL*.*clkrdy* until it reads 1, or

        b\. Set *WDTn_CTRL*.*clkrdy_ie =* 1 to generate a WDT enabled
        interrupt event.

21. 3\. Re-enable global interrupts.

22. 4\. Configure *WDTn_CLKSEL.source* to select the clock source.

23. 5\. Configure the traditional/legacy thresholds:

    1.  a\. Configure *WDTn_CTRL.int_late_val* to the desired threshold
        for the WDT late interrupt event.

        b\. Configure *WDTn_CTRL.rst_late_val* to the desired threshold
        for the WDT late reset event.

24. 6\. If using the optional windowed WDT feature:

    1.  a\. Set *WDTn_CTRL.win_en* = 1 to enable the windowed WDT
        feature.

        b\. Configure *WDTn_CTRL.int_early_val* to the desired threshold
        for the WDT early interrupt event.

        c\. Configure *WDTn_CTRL.rst_early_val* to the desired threshold
        for the WDT early reset event.

        d\. Set *WDTn_CTRL.wdt_int_en* to generate an interrupt when a
        WDT late interrupt event occurs. If *WDTn_CTRL.win_en* = 1, an
        interrupt is generated by a WDT late interrupt event and a WDT
        early interrupt event.

        e\. Set *WDTn_CTRL.wdt_rst_en* to generate an interrupt when a
        WDT late reset event occurs. If *WDTn_CTRL.win_en* = 1, an
        interrupt is generated by a WDT late reset event and a WDT early
        reset event.

25. 7\. Execute the WDT feed sequence and enable the WDT:

    1.  a\. Disable global interrupts

        b\. Write *WDTn_RST.reset* to 0xA5.

        c\. Write *WDTn_RST.reset* to 0x5A. Hardware will reset
        *WDTn_CNT*.*count* = 0x0000 0000.

        d\. Set *WDTn_CTRL.en* to 1 to enable the WDT.

26. 8\. Verify the peripheral is enabled before proceeding:

    1.  a\. Poll *WDTn_CTRL*.*clkrdy* until it reads 1, or

        b\. Set *WDTn_CTRL*.*clkrdy_ie =* 1 to generate a WDT enabled
        event interrupt.

27. 9\. Re-enable global interrupts.

### Resets

The WDT is a critical safety feature, and most of the fields are set on
POR or system reset events only.

### Registers

See *Table 3‑3* for the base address of this peripheral/module. If
multiple instances of the peripheral are provided, each instance has its
own independent set of the registers shown in *Table 21‑3*. Register
names for a specific instance are defined by replacing \"n\" with the
instance number. As an example, a register PERIPHERALn_CTRL resolves to
PERIPHERAL0_CTRL and PERIPHERAL1_CTRL for instances 0 and 1,
respectively.

See *Table 1‑1* for an explanation of the read and write access of each
field. Unless specified otherwise, all fields are reset on a system
reset, soft reset, POR, and the peripheral-specific resets.

  ------------------------------------------------------------------------
  Offset       Register        Name
  ------------ --------------- -------------------------------------------
  \[0x0000\]   *WDTn_CTRL*     WDT Control Register

  \[0x0004\]   *WDTn_RST*      WDT Reset Register

  \[0x0008\]   *WDTn_CLKSEL*   WDT Clock Select Register

  \[0x000C\]   *WDTn_CNT*      WDT Count Register
  ------------------------------------------------------------------------

  : : WDT Register Summary

#### Register Details

+---+----------+----+----+----------------------+---------------------+
| W |          |    |    | ##### WDTn_CTRL      | \[0x0000\]          |
| D |          |    |    |                      |                     |
| T |          |    |    |                      |                     |
| C |          |    |    |                      |                     |
| o |          |    |    |                      |                     |
| n |          |    |    |                      |                     |
| t |          |    |    |                      |                     |
| r |          |    |    |                      |                     |
| o |          |    |    |                      |                     |
| l |          |    |    |                      |                     |
+===+==========+====+====+======================+=====================+
| B | Name     | Ac | R  | Description          |                     |
| i |          | ce | es |                      |                     |
| t |          | ss | et |                      |                     |
| s |          |    |    |                      |                     |
+---+----------+----+----+----------------------+---------------------+
| 3 | rst_late | R  | 0  | Reset Late Event     |                     |
| 1 |          | /W |    |                      |                     |
|   |          |    |    | A watchdog reset     |                     |
|   |          |    |    | event occurred after |                     |
|   |          |    |    | the time specified   |                     |
|   |          |    |    | in                   |                     |
|   |          |    |    | *WDTn_C              |                     |
|   |          |    |    | TRL*.*rst_late_val*. |                     |
|   |          |    |    | This flag is set     |                     |
|   |          |    |    | even if              |                     |
|   |          |    |    | *WDTn_CTRL*.*win_en* |                     |
|   |          |    |    | = 0 or               |                     |
|   |          |    |    | *WDT                 |                     |
|   |          |    |    | n_CTRL*.*wdt_rst_en* |                     |
|   |          |    |    | = 0. The software    |                     |
|   |          |    |    | has to clear the     |                     |
|   |          |    |    | flag appropriately   |                     |
|   |          |    |    | in case of           |                     |
|   |          |    |    | carried-over flags   |                     |
|   |          |    |    | from prior           |                     |
|   |          |    |    | operations.          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: Normal operation  |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Watchdog reset    |                     |
|   |          |    |    | occurred after       |                     |
|   |          |    |    | *WDTn_CT             |                     |
|   |          |    |    | RL*.*rst_early_val*. |                     |
+---+----------+----+----+----------------------+---------------------+
| 3 | r        | R  | 0  | Reset Early Event    |                     |
| 0 | st_early | /W |    |                      |                     |
|   |          |    |    | A watchdog reset     |                     |
|   |          |    |    | event occurred       |                     |
|   |          |    |    | before the time      |                     |
|   |          |    |    | specified in         |                     |
|   |          |    |    | *WDTn_CT             |                     |
|   |          |    |    | RL*.*rst_early_val*. |                     |
|   |          |    |    | This flag is set     |                     |
|   |          |    |    | even if              |                     |
|   |          |    |    | *WDTn_CTRL*.*win_en* |                     |
|   |          |    |    | = 0 or               |                     |
|   |          |    |    | *WDT                 |                     |
|   |          |    |    | n_CTRL*.*wdt_rst_en* |                     |
|   |          |    |    | = 0. The software    |                     |
|   |          |    |    | has to clear the     |                     |
|   |          |    |    | flag appropriately   |                     |
|   |          |    |    | in case of           |                     |
|   |          |    |    | carried-over flags   |                     |
|   |          |    |    | from prior           |                     |
|   |          |    |    | operations.          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: Normal operation  |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Watchdog reset    |                     |
|   |          |    |    | occurred before      |                     |
|   |          |    |    | *WDTn_CT             |                     |
|   |          |    |    | RL*.*rst_early_val*. |                     |
+---+----------+----+----+----------------------+---------------------+
| 2 | win_en   | R  | 0  | Window Function      |                     |
| 9 |          | /W |    | Enable               |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: Disabled. WDT     |                     |
|   |          |    |    | recognizes interrupt |                     |
|   |          |    |    | late and reset late  |                     |
|   |          |    |    | events, supporting   |                     |
|   |          |    |    | legacy               |                     |
|   |          |    |    | implementations.     |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Enabled           |                     |
+---+----------+----+----+----------------------+---------------------+
| 2 | clkrdy   | R  | 0  | Clock Status         |                     |
| 8 |          |    |    |                      |                     |
|   |          |    |    | This field is        |                     |
|   |          |    |    | cleared by the       |                     |
|   |          |    |    | hardware when        |                     |
|   |          |    |    | software changes the |                     |
|   |          |    |    | state of             |                     |
|   |          |    |    | *WDTn_CTRL.en*. The  |                     |
|   |          |    |    | hardware sets the    |                     |
|   |          |    |    | field to 1 when the  |                     |
|   |          |    |    | change to the        |                     |
|   |          |    |    | requested enable or  |                     |
|   |          |    |    | disable state        |                     |
|   |          |    |    | completes.           |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: WDT clock is off. |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: WDT clock is on.  |                     |
+---+----------+----+----+----------------------+---------------------+
| 2 | c        | R  | 0  | Clock Switch Ready   |                     |
| 7 | lkrdy_ie | /W |    | Interrupt Enable     |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | This interrupt       |                     |
|   |          |    |    | prevents the         |                     |
|   |          |    |    | software from        |                     |
|   |          |    |    | needing to poll the  |                     |
|   |          |    |    | *WDTn_CTRL*.*clkrdy* |                     |
|   |          |    |    | all the time. When   |                     |
|   |          |    |    | *WDTn_CTRL*.*clkrdy* |                     |
|   |          |    |    | goes from high to    |                     |
|   |          |    |    | low, the interrupt   |                     |
|   |          |    |    | signals that the     |                     |
|   |          |    |    | transition is        |                     |
|   |          |    |    | complete.            |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: Disabled          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Enabled           |                     |
+---+----------+----+----+----------------------+---------------------+
| 2 | \-       | RO | 0  | Reserved             |                     |
| 6 |          |    |    |                      |                     |
| : |          |    |    |                      |                     |
| 2 |          |    |    |                      |                     |
| 4 |          |    |    |                      |                     |
+---+----------+----+----+----------------------+---------------------+
| 2 | rst_e    | R  | 0  | Reset Early Event    |                     |
| 3 | arly_val | /W |    | Threshold            |                     |
| : |          |    |    |                      |                     |
| 2 |          |    |    | 0x0: 2^31^ ×         |                     |
| 0 |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x1: 2^30^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x2: 2^29^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x3: 2^28^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x4: 2^27^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x5: 2^26^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x6: 2^25^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x7: 2^24^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x8: 2^23^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x9: 2^22^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xA: 2^21^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xB: 2^20^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xC: 2^19^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xD: 2^18^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xE: 2^17^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xF: 2^16^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | Note: The watchdog   |                     |
|   |          |    |    | timer must be        |                     |
|   |          |    |    | disabled             |                     |
|   |          |    |    | (WDTn_CTRL.en = 0)   |                     |
|   |          |    |    | before changing this |                     |
|   |          |    |    | field.               |                     |
+---+----------+----+----+----------------------+---------------------+
| 1 | int_e    | R  | 0  | Interrupt Early      |                     |
| 9 | arly_val | /W |    | Event Threshold      |                     |
| : |          |    |    |                      |                     |
| 1 |          |    |    | 0x0: 2^31^ ×         |                     |
| 6 |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x1: 2^30^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x2: 2^29^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x3: 2^28^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x4: 2^27^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x5: 2^26^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x6: 2^25^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x7: 2^24^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x8: 2^23^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x9: 2^22^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xA: 2^21^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xB: 2^20^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xC: 2^19^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xD: 2^18^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xE: 2^17^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xF: 2^16^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | Note: The watchdog   |                     |
|   |          |    |    | timer must be        |                     |
|   |          |    |    | disabled             |                     |
|   |          |    |    | (WDTn_CTRL.en = 0)   |                     |
|   |          |    |    | before changing this |                     |
|   |          |    |    | field.               |                     |
+---+----------+----+----+----------------------+---------------------+
| 1 | \-       | RO | 0  | Reserved             |                     |
| 5 |          |    |    |                      |                     |
| : |          |    |    |                      |                     |
| 1 |          |    |    |                      |                     |
| 3 |          |    |    |                      |                     |
+---+----------+----+----+----------------------+---------------------+
| 1 | i        | R  | 0  | Interrupt Early Flag |                     |
| 2 | nt_early | /W |    |                      |                     |
|   |          |    |    | A feed sequence      |                     |
|   |          |    |    | occurred earlier     |                     |
|   |          |    |    | than the time        |                     |
|   |          |    |    | determined by the    |                     |
|   |          |    |    | *WDTn_C              |                     |
|   |          |    |    | TRL*.*int_early_val* |                     |
|   |          |    |    | field. This flag     |                     |
|   |          |    |    | will be set even if  |                     |
|   |          |    |    | *WDTn_CTRL*.*win_en* |                     |
|   |          |    |    | = 0.                 |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: No interrupt      |                     |
|   |          |    |    | event                |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Interrupt event   |                     |
|   |          |    |    | occurred. Generates  |                     |
|   |          |    |    | a WDT interrupt if   |                     |
|   |          |    |    | *WDTn_CTR            |                     |
|   |          |    |    | L*.*wdt_int_en* = 1. |                     |
+---+----------+----+----+----------------------+---------------------+
| 1 | wd       | R  | 0  | WDT Reset Enable     |                     |
| 1 | t_rst_en | /W |    |                      |                     |
|   |          |    |    | 0: Disabled          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Enabled           |                     |
+---+----------+----+----+----------------------+---------------------+
| 1 | wd       | R  | 0  | WDT Interrupt Enable |                     |
| 0 | t_int_en | /W |    |                      |                     |
|   |          |    |    | 0: Disabled          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Enabled           |                     |
+---+----------+----+----+----------------------+---------------------+
| 9 | int_late | R  | 0  | Interrupt Late Flag  |                     |
|   |          | /W |    |                      |                     |
|   |          |    |    | A watchdog feed      |                     |
|   |          |    |    | sequence did not     |                     |
|   |          |    |    | occur before the     |                     |
|   |          |    |    | time determined by   |                     |
|   |          |    |    | the                  |                     |
|   |          |    |    | *WDTn_               |                     |
|   |          |    |    | CTRL*.*int_late_val* |                     |
|   |          |    |    | field.               |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: No interrupt      |                     |
|   |          |    |    | event.               |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Interrupt event   |                     |
|   |          |    |    | occurred.            |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | Note: A WDT          |                     |
|   |          |    |    | interrupt is         |                     |
|   |          |    |    | generated if the WDT |                     |
|   |          |    |    | interrupt is enabled |                     |
|   |          |    |    | (                    |                     |
|   |          |    |    | WDTn_CTRL.wdt_int_en |                     |
|   |          |    |    | = 1).                |                     |
+---+----------+----+----+----------------------+---------------------+
| 8 | en       | R  | 0  | WDT Enable           |                     |
|   |          | /W |    |                      |                     |
|   |          |    |    | This field           |                     |
|   |          |    |    | enables/disables the |                     |
|   |          |    |    | WDT peripheral       |                     |
|   |          |    |    | clock.               |                     |
|   |          |    |    | *WDTn_CNT*.*count*   |                     |
|   |          |    |    | holds its value      |                     |
|   |          |    |    | while the WDT is     |                     |
|   |          |    |    | disabled. The WDT    |                     |
|   |          |    |    | feed sequence must   |                     |
|   |          |    |    | be performed         |                     |
|   |          |    |    | immediately before   |                     |
|   |          |    |    | any change to this   |                     |
|   |          |    |    | field.               |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0: Disabled          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 1: Enabled           |                     |
+---+----------+----+----+----------------------+---------------------+
| 7 | rst_     | R  | 0  | Reset Late Event     |                     |
| : | late_val | /W |    | Threshold            |                     |
| 4 |          |    |    |                      |                     |
|   |          |    |    | 0x0: 2^31^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x1: 2^30^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x2: 2^29^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x3: 2^28^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x4: 2^27^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x5: 2^26^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x6: 2^25^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x7: 2^24^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x8: 2^23^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x9: 2^22^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xA: 2^21^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xB: 2^20^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xC: 2^19^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xD: 2^18^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xE: 2^17^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xF: 2^16^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | Note: The watchdog   |                     |
|   |          |    |    | timer must be        |                     |
|   |          |    |    | disabled             |                     |
|   |          |    |    | (WDTn_CTRL.en = 0)   |                     |
|   |          |    |    | before changing this |                     |
|   |          |    |    | field.               |                     |
+---+----------+----+----+----------------------+---------------------+
| 3 | int_     | R  | 0  | Interrupt Late Event |                     |
| : | late_val | /W |    | Threshold            |                     |
| 0 |          |    |    |                      |                     |
|   |          |    |    | 0x0: 2^31^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x1: 2^30^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x2: 2^29^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x3: 2^28^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x4: 2^27^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x5: 2^26^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x6: 2^25^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x7: 2^24^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x8: 2^23^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0x9: 2^22^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xA: 2^21^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xB: 2^20^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xC: 2^19^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xD: 2^18^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xE: 2^17^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | 0xF: 2^16^ ×         |                     |
|   |          |    |    | *t~WDTCLK~*          |                     |
|   |          |    |    |                      |                     |
|   |          |    |    | Note: The watchdog   |                     |
|   |          |    |    | timer must be        |                     |
|   |          |    |    | disabled             |                     |
|   |          |    |    | (WDTn_CTRL.en = 0)   |                     |
|   |          |    |    | before changing this |                     |
|   |          |    |    | field.               |                     |
+---+----------+----+----+----------------------+---------------------+

: : WDT Control Register

+----+------+-----+-----+----------------------+---------------------+
| W  |      |     |     | ##### WDTn_RST       | \[0x0004\]          |
| DT |      |     |     |                      |                     |
| R  |      |     |     |                      |                     |
| es |      |     |     |                      |                     |
| et |      |     |     |                      |                     |
+====+======+=====+=====+======================+=====================+
| Bi | Name | Acc | Re  | Description          |                     |
| ts |      | ess | set |                      |                     |
+----+------+-----+-----+----------------------+---------------------+
| 31 | \-   | RO  | 0   | Reserved             |                     |
| :8 |      |     |     |                      |                     |
+----+------+-----+-----+----------------------+---------------------+
| 7  | r    | R/W | 0*~ | Reset Watchdog Timer |                     |
| :0 | eset |     | ✝~* | Count                |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | Writing the WDT feed |                     |
|    |      |     |     | sequence, in two     |                     |
|    |      |     |     | consecutive write    |                     |
|    |      |     |     | instructions, to     |                     |
|    |      |     |     | this register resets |                     |
|    |      |     |     | the internal counter |                     |
|    |      |     |     | to 0x0000 0000.      |                     |
|    |      |     |     | Perform the          |                     |
|    |      |     |     | following steps to   |                     |
|    |      |     |     | perform a WDT feed   |                     |
|    |      |     |     | sequence:            |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 1\. Write            |                     |
|    |      |     |     | *WDTn_RST.reset* =   |                     |
|    |      |     |     | 0xA5                 |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 2\. Write            |                     |
|    |      |     |     | *WDTn_RST.reset* =   |                     |
|    |      |     |     | 0x5A                 |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | Writes to the        |                     |
|    |      |     |     | *WDTn_CTRL*.*en*     |                     |
|    |      |     |     | field, which enable  |                     |
|    |      |     |     | or disable the WDT,  |                     |
|    |      |     |     | must be the next     |                     |
|    |      |     |     | instruction          |                     |
|    |      |     |     | following the WDT    |                     |
|    |      |     |     | feed sequence.       |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | ^✝^Note: This field  |                     |
|    |      |     |     | is set to 0 on a POR |                     |
|    |      |     |     | and is not affected  |                     |
|    |      |     |     | by any other form of |                     |
|    |      |     |     | reset.               |                     |
+----+------+-----+-----+----------------------+---------------------+

: : WDT Reset Register

+----+------+-----+-----+----------------------+---------------------+
| W  |      |     |     | ##### WDTn_CLKSEL    | \[0x0008\]          |
| DT |      |     |     |                      |                     |
| C  |      |     |     |                      |                     |
| lo |      |     |     |                      |                     |
| ck |      |     |     |                      |                     |
| So |      |     |     |                      |                     |
| ur |      |     |     |                      |                     |
| ce |      |     |     |                      |                     |
| Se |      |     |     |                      |                     |
| le |      |     |     |                      |                     |
| ct |      |     |     |                      |                     |
+====+======+=====+=====+======================+=====================+
| Bi | Name | Acc | Re  | Description          |                     |
| ts |      | ess | set |                      |                     |
+----+------+-----+-----+----------------------+---------------------+
| 31 | \-   | RO  | 0   | Reserved             |                     |
| :3 |      |     |     |                      |                     |
+----+------+-----+-----+----------------------+---------------------+
| 2  | so   | R/W | 0*~ | Clock Source Select  |                     |
| :0 | urce |     | ✝~* |                      |                     |
|    |      |     |     | See *Table 21‑1* for |                     |
|    |      |     |     | available clock      |                     |
|    |      |     |     | options.             |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 0: CLK0              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 1: CLK1              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 2: CLK2              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 3: CLK3              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 4: CLK4              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 5: CLK5              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 6: CLK6              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | 7: CLK7              |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | ^✝^Note: This field  |                     |
|    |      |     |     | is only reset on a   |                     |
|    |      |     |     | POR and unaffected   |                     |
|    |      |     |     | by other resets.     |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | Note: The watchdog   |                     |
|    |      |     |     | timer must be        |                     |
|    |      |     |     | disabled             |                     |
|    |      |     |     | (WDTn_CTRL.en = 0)   |                     |
|    |      |     |     | before changing this |                     |
|    |      |     |     | field.               |                     |
+----+------+-----+-----+----------------------+---------------------+

: : WDT Clock Source Select Register

+----+------+-----+-----+----------------------+---------------------+
| W  |      |     |     | ##### WDTn_CNT       | \[0x000C\]          |
| DT |      |     |     |                      |                     |
| C  |      |     |     |                      |                     |
| ou |      |     |     |                      |                     |
| nt |      |     |     |                      |                     |
+====+======+=====+=====+======================+=====================+
| Bi | Name | Acc | Re  | Description          |                     |
| ts |      | ess | set |                      |                     |
+----+------+-----+-----+----------------------+---------------------+
| 31 | c    | R   | 0   | WDT Counter          |                     |
| :0 | ount |     |     |                      |                     |
|    |      |     |     | This field is a      |                     |
|    |      |     |     | mirror of the watch  |                     |
|    |      |     |     | dog timer\'s counter |                     |
|    |      |     |     | value.               |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | This register is     |                     |
|    |      |     |     | reset by a system    |                     |
|    |      |     |     | reset, as well as    |                     |
|    |      |     |     | the watchdog feeding |                     |
|    |      |     |     | sequence.            |                     |
|    |      |     |     |                      |                     |
|    |      |     |     | Note: The watchdog   |                     |
|    |      |     |     | timer must be        |                     |
|    |      |     |     | disabled             |                     |
|    |      |     |     | (WDTn_CTRL.en = 0)   |                     |
|    |      |     |     | before reading this  |                     |
|    |      |     |     | field.               |                     |
+----+------+-----+-----+----------------------+---------------------+

: : WDT Count Register