## TRNG Engine

The Analog Devices-supplied Universal Cryptographic Library (UCL)
provides a function to generate random numbers intended to meet the
requirements of common security validations. The entropy from one or
more internal noise sources continually feeds a TRNG, the output of
which is then processed by software and hardware to generate the number
returned by the UCL function. Analog Devices works directly with the
customer\'s accredited testing laboratory to provide any information
regarding the TRNG needed to support the customer\'s validation
requirements.

The general information in this section is provided only for
completeness; customers are expected to use the Analog Devices UCL to
generate random numbers.

Software can use the TRNG engine to generate AES keys using a hardware
key derivation function (HKDF) and using the TRNG as input to the HKDF.

### Registers

See *Table 3‑3* for the base address of this peripheral/module. See
*Table 1‑1* for an explanation of the read and write access of each
field. Unless specified otherwise, all fields are reset on a system
reset, soft reset, POR, and the peripheral-specific resets.

  ------------------------------------------------------------------------
  Offset       Register        Name
  ------------ --------------- -------------------------------------------
  \[0x0000\]   *TRNG_CTRL*     TRNG Control Register

  \[0x0004\]   *TRNG_STATUS*   TRNG Status Register

  \[0x0008\]   *TRNG_DATA*     TRNG Data Register
  ------------------------------------------------------------------------

  : : TRNG Register Summary

#### Register Details

+----+--------+---+---+-----+----------------------+---------------------+
| C  |        |   | # |     |                      | \[0x0000\]          |
| ry |        |   | # |     |                      |                     |
| pt |        |   | # |     |                      |                     |
| og |        |   | # |     |                      |                     |
| ra |        |   | # |     |                      |                     |
| ph |        |   |   |     |                      |                     |
| ic |        |   | T |     |                      |                     |
| C  |        |   | R |     |                      |                     |
| on |        |   | N |     |                      |                     |
| tr |        |   | G |     |                      |                     |
| ol |        |   | _ |     |                      |                     |
|    |        |   | C |     |                      |                     |
|    |        |   | T |     |                      |                     |
|    |        |   | R |     |                      |                     |
|    |        |   | L |     |                      |                     |
+====+========+===+===+=====+======================+=====================+
| Bi | Name   | A |   | Re  | Description          |                     |
| ts |        | c |   | set |                      |                     |
|    |        | c |   |     |                      |                     |
|    |        | e |   |     |                      |                     |
|    |        | s |   |     |                      |                     |
|    |        | s |   |     |                      |                     |
+----+--------+---+---+-----+----------------------+---------------------+
| 3  | \-     | R |   | 0   | Reserved             |                     |
| 1: |        | O |   |     |                      |                     |
| 16 |        |   |   |     |                      |                     |
+----+--------+---+---+-----+----------------------+---------------------+
| 15 | k      | R |   | 0   | Wipe Key             |                     |
|    | eywipe | / |   |     |                      |                     |
|    |        | W |   |     | Write this field to  |                     |
|    |        |   |   |     | 1 to wipe the TRNG   |                     |
|    |        |   |   |     | key.                 |                     |
+----+--------+---+---+-----+----------------------+---------------------+
| 14 | \-     | R |   | 0   | Reserved             |                     |
| :4 |        | O |   |     |                      |                     |
+----+--------+---+---+-----+----------------------+---------------------+
| 3  | keygen | R |   | 0   | Generate Key         |                     |
|    |        | / |   |     |                      |                     |
|    |        | W |   |     | Write this field to  |                     |
|    |        |   |   |     | 1 to generate a key  |                     |
|    |        |   |   |     | using the TRNG.      |                     |
+----+--------+---+---+-----+----------------------+---------------------+
| 2  | \-     | R |   | 0   | Reserved             |                     |
|    |        | O |   |     |                      |                     |
+----+--------+---+---+-----+----------------------+---------------------+
| 1  | rnd_ie | R |   | 0   | Random Number        |                     |
|    |        | / |   |     | Interrupt Enable     |                     |
|    |        | W |   |     |                      |                     |
|    |        |   |   |     | This bit enables an  |                     |
|    |        |   |   |     | interrupt to be      |                     |
|    |        |   |   |     | generated when       |                     |
|    |        |   |   |     | *TRNG_STATUS*.*rdy*  |                     |
|    |        |   |   |     | = 1.                 |                     |
|    |        |   |   |     |                      |                     |
|    |        |   |   |     | 0: Disabled          |                     |
|    |        |   |   |     |                      |                     |
|    |        |   |   |     | 1: Enabled           |                     |
+----+--------+---+---+-----+----------------------+---------------------+
| 0  | \-     | R |   | 0   | Reserved             |                     |
|    |        | O |   |     |                      |                     |
+----+--------+---+---+-----+----------------------+---------------------+

: : TRNG Control Register

+----+---------+---+---+---+----------------------+---------------------+
| TR |         |   |   | # |                      | \[0x0004\]          |
| NG |         |   |   | # |                      |                     |
| St |         |   |   | # |                      |                     |
| at |         |   |   | # |                      |                     |
| us |         |   |   | # |                      |                     |
|    |         |   |   |   |                      |                     |
|    |         |   |   | T |                      |                     |
|    |         |   |   | R |                      |                     |
|    |         |   |   | N |                      |                     |
|    |         |   |   | G |                      |                     |
|    |         |   |   | _ |                      |                     |
|    |         |   |   | S |                      |                     |
|    |         |   |   | T |                      |                     |
|    |         |   |   | A |                      |                     |
|    |         |   |   | T |                      |                     |
|    |         |   |   | U |                      |                     |
|    |         |   |   | S |                      |                     |
+====+=========+===+===+===+======================+=====================+
| Bi | Name    | A | R |   | Description          |                     |
| ts |         | c | e |   |                      |                     |
|    |         | c | s |   |                      |                     |
|    |         | e | e |   |                      |                     |
|    |         | s | t |   |                      |                     |
|    |         | s |   |   |                      |                     |
+----+---------+---+---+---+----------------------+---------------------+
| 31 | \-      | R | 0 |   | Reserved             |                     |
| :1 |         | O |   |   |                      |                     |
+----+---------+---+---+---+----------------------+---------------------+
| 0  | rdy     | R | 0 |   | Random Number Ready  |                     |
|    |         |   |   |   |                      |                     |
|    |         |   |   |   | This bit is          |                     |
|    |         |   |   |   | automatically        |                     |
|    |         |   |   |   | cleared to 0, and a  |                     |
|    |         |   |   |   | new random number is |                     |
|    |         |   |   |   | generated when       |                     |
|    |         |   |   |   | *TRNG_DATA*.*data*   |                     |
|    |         |   |   |   | is read.             |                     |
|    |         |   |   |   |                      |                     |
|    |         |   |   |   | 0: Random number     |                     |
|    |         |   |   |   | generation in        |                     |
|    |         |   |   |   | progress. The        |                     |
|    |         |   |   |   | content of           |                     |
|    |         |   |   |   | *TRNG_DATA*.*data*   |                     |
|    |         |   |   |   | is invalid.          |                     |
|    |         |   |   |   |                      |                     |
|    |         |   |   |   | 1:                   |                     |
|    |         |   |   |   | *TRNG_DATA*.*data*   |                     |
|    |         |   |   |   | contains new 32-bit  |                     |
|    |         |   |   |   | random data. An      |                     |
|    |         |   |   |   | interrupt is         |                     |
|    |         |   |   |   | generated if         |                     |
|    |         |   |   |   | *TRNG_CTRL*.*rnd_ie* |                     |
|    |         |   |   |   | = 1.                 |                     |
+----+---------+---+---+---+----------------------+---------------------+

: : TRNG Status Register

+----+---------+---+---+---+----------------------+---------------------+
| TR |         |   |   | # |                      | \[0x0008\]          |
| NG |         |   |   | # |                      |                     |
| Da |         |   |   | # |                      |                     |
| ta |         |   |   | # |                      |                     |
|    |         |   |   | # |                      |                     |
|    |         |   |   |   |                      |                     |
|    |         |   |   | T |                      |                     |
|    |         |   |   | R |                      |                     |
|    |         |   |   | N |                      |                     |
|    |         |   |   | G |                      |                     |
|    |         |   |   | _ |                      |                     |
|    |         |   |   | D |                      |                     |
|    |         |   |   | A |                      |                     |
|    |         |   |   | T |                      |                     |
|    |         |   |   | A |                      |                     |
+====+=========+===+===+===+======================+=====================+
| Bi | Name    | A | R |   | Description          |                     |
| ts |         | c | e |   |                      |                     |
|    |         | c | s |   |                      |                     |
|    |         | e | e |   |                      |                     |
|    |         | s | t |   |                      |                     |
|    |         | s |   |   |                      |                     |
+----+---------+---+---+---+----------------------+---------------------+
| 31 | data    | R | 0 |   | TRNG Data            |                     |
| :0 |         | O |   |   |                      |                     |
|    |         |   |   |   | The 32-bit random    |                     |
|    |         |   |   |   | number generated is  |                     |
|    |         |   |   |   | available in this    |                     |
|    |         |   |   |   | field when           |                     |
|    |         |   |   |   | *TRN                 |                     |
|    |         |   |   |   | G_STATUS*.*rdy* = 1. |                     |
+----+---------+---+---+---+----------------------+---------------------+

: : TRNG Data Register
