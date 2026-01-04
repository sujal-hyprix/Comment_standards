# Coding Comment Standards  
*(NASA C Style, Doxygen-Compatible)*

---

## 0. Scope and Purpose

This document defines **mandatory commenting standards** for C code written within
the organization.

The goals of this standard are to:
- Improve safety, clarity, and maintainability
- Enable automatic documentation generation using **Doxygen**
- Support integration with **Sphinx / ReadTheDocs**
- Align with **NASA / aerospace software practices**

This document uses **normative language**:
- **MUST** – mandatory requirement
- **SHOULD** – strongly recommended
- **MUST NOT** – prohibited
- **MAY** – optional

---

## 0.1 Definitions

- **File**: A single `.c` or `.h` compilation unit  
- **Module**: A self-contained unit of responsibility with a public interface  
- **Public Header**: A header file intended to be included by other code  
- **Implementation File**: A source file containing internal logic  

---

# Phase 1 — File-Level Documentation

---

## 1. Purpose of File-Level Documentation

File-level documentation describes the **responsibility of an individual file**.

Every file must be understandable **in isolation**, without requiring inspection
of other files.

File-level documentation applies to:
- All `.c` files
- All `.h` files
- Without exception

---

## 2. Mandatory File Header

### Rule F-1.1 — File Header Requirement

Every `.c` and `.h` file **MUST** begin with a Doxygen file-level comment block.

---

## 3. File Header Format

### Rule F-2.1 — Doxygen Block Style

File headers **MUST** use a Doxygen block comment beginning with `/**`.

#### Required File Header Template

```c
/**
 * @file    imu_driver.c
 * @brief   IMU driver register access implementation.
 *
 * @details
 * This file implements low-level register access routines for the
 * XYZ inertial measurement unit.
 *
 * This file does not expose public APIs and must not be included
 * directly by application code.
 *
 * @note
 * This file assumes the I2C peripheral has already been initialized.
 *
 * @warning
 * This file MUST NOT be accessed from ISR context.
 */
```

---

## 4. Required Doxygen Tags (File-Level)

### Rule F-3.1 — Mandatory Tags

Every file header **MUST** include:

| Tag        | Requirement                           |
| ---------- | ------------------------------------- |
| `@file`    | MUST match the file name exactly      |
| `@brief`   | MUST be one concise sentence          |
| `@details` | MUST describe file responsibility     |
| `@note`    | MUST be present if assumptions exist  |
| `@warning` | MUST be present if misuse is possible |

---

## 5. Content Rules

### Rule F-4.1 — Responsibility Over Implementation

File-level documentation **MUST** describe:

* What the file is responsible for
* What it explicitly does not do
* File-specific assumptions

File-level documentation **MUST NOT** describe:

* Algorithms
* Control flow
* Register sequences
* Function behavior

---

## 6. Header vs Source File Rules

### Rule F-5.1 — Header Files (`.h`)

Header file documentation **MUST** describe:

* Public declarations contained in the file
* Usage constraints
* Visibility intent

---

### Rule F-5.2 — Source Files (`.c`)

Source file documentation **MUST** describe:

* Implementation-specific responsibilities
* Limitations not visible in headers

---

## 7. Prohibited Content

### Rule F-6.1 — Versioning Information

File headers **MUST NOT** include:

* Revision history
* Change logs
* Modification dates
* Manual version numbers

Version control systems are the authoritative source of history.

---

## 8. License Headers

### Rule F-7.1 — License Placement

If a license header exists:

* It **MUST** appear before the Doxygen file header
* It **MUST NOT** interrupt the Doxygen comment

---

## 9. File-Level Review Checklist

A file **MUST be rejected** if:

* No Doxygen file header exists
* File responsibility is unclear
* Assumptions are implicit
* Implementation details appear in the file header

---

# Phase 2 — Module-Level Documentation

---

## 10. Purpose of Module-Level Documentation

Module-level documentation defines the **responsibility and contract of a module**
as a logical unit.

A **module** is defined as:

> A self-contained unit of functionality with a public interface intended for reuse.

Modules are **not files**.
Files are implementation details.

---

## 11. One-Module–One-Header Rule

### Rule M-1.1 — Single Public Header

Each module **MUST** have exactly **one public header file**.

This public header:

* Defines the module’s external API
* Acts as the module boundary
* Contains the module-level documentation

---

### Rule M-1.2 — Module Documentation Location

Module-level documentation **MUST**:

* Appear **once**
* Appear **only** in the module’s public header (`.h`)
* Appear **before** any public declarations

It **MUST NOT** appear in:

* Source files
* Private headers
* Multiple locations

---

## 12. Mandatory Module Documentation Block

### Rule M-2.1 — Required `@defgroup`

Each module **MUST** define a Doxygen group using `@defgroup`.

#### Required Module Documentation Template

```c
/**
 * @defgroup imu_driver IMU Driver
 * @brief Low-level driver for XYZ inertial measurement unit.
 *
 * @details
 * This module is responsible for:
 * - Device initialization
 * - Configuration
 * - Raw sensor data acquisition
 *
 * This module explicitly does NOT perform:
 * - Sensor fusion
 * - Calibration
 * - Coordinate frame transformations
 *
 * @note
 * All APIs in this module are non-reentrant.
 *
 * @warning
 * This module MUST NOT be accessed from ISR context.
 */

```
## 13. Required Module-Level Content

### Rule M-3.1 — Mandatory Content

Module documentation **MUST** describe:

* The module’s single responsibility
* Services it provides
* Services it explicitly does not provide
* Global assumptions
* Execution context constraints

---

### Rule M-3.2 — Prohibited Content

Module documentation **MUST NOT** describe:

* File-level details
* Algorithms
* Register-level behavior
* Function semantics

---

## 14. Public API Association

### Rule M-4.1 — Mandatory `@ingroup`

All public declarations in the module’s public header **MUST** be associated
with the module using `@ingroup`.

```c
/**
 * @ingroup imu_driver
 */
int imu_init(void);
```

---

## 15. Assumptions and Error Semantics

### Rule M-5.1 — Explicit Assumptions

All module-wide assumptions **MUST** be documented at the module level.

---

### Rule M-5.2 — Error Philosophy

Module documentation **MUST** describe:

* How errors are generally reported
* Whether errors are recoverable
* Whether permanent fault states are possible

---

## 16. Relationship to File-Level Documentation

| Level        | Answers                                 |
| ------------ | --------------------------------------- |
| File-Level   | What is this file responsible for?      |
| Module-Level | What is this subsystem responsible for? |

Module documentation **MUST NOT** duplicate file-level documentation.

---

## 17. Module-Level Review Checklist

A module **MUST be rejected** if:

* No module-level `@defgroup` exists
* Module responsibility is ambiguous
* Assumptions are implicit
* Public APIs are not grouped under the module
* Module documentation appears in multiple places

---

**End of Phase 1 and Phase 2**

# Phase 3 — Interface & Function-Level Documentation  
*(NASA C Style, Doxygen-Compatible)*

---

## 18. Purpose of Function-Level Documentation

Function-level documentation defines the **behavioral contract** of callable APIs.

A function contract specifies:
- What the function does
- What inputs it expects
- What outputs it produces
- What errors may occur
- What side effects it has
- Under what conditions it may be called

Function documentation applies to:
- All **public functions** (mandatory)
- **Private functions** that are non-trivial or safety-critical

---

## 19. Mandatory Documentation for Public Functions

### Rule FNC-1.1 — Public Function Documentation

Every public function **MUST** be documented with a Doxygen comment block.

A public function is any function:
- Declared in a public header file
- Intended to be called by code outside the module

Undocumented public functions are **FORBIDDEN**.

---

### Rule FNC-1.2 — Documentation Placement

Function documentation **MUST**:
- Appear immediately before the function declaration
- Be written in the header file (`.h`)
- Represent the authoritative contract

Documentation **MUST NOT** rely on comments in the `.c` file.

---

## 20. Required Doxygen Block Format

### Rule FNC-2.1 — Doxygen Style

Function documentation **MUST** use a Doxygen block comment beginning with `/**`.

---

### Required Function Documentation Template

```c
/**
 * @brief Initializes the IMU module.
 *
 * @details
 * This function initializes the IMU hardware and prepares the module
 * for data acquisition. It must be called exactly once before any
 * other IMU API is used.
 *
 * @pre
 * - I2C peripheral is initialized
 * - Power to the IMU is stable
 *
 * @post
 * - IMU module is ready for use
 *
 * @return
 * 0 on success, negative error code on failure.
 *
 * @note
 * This function is not reentrant.
 *
 * @warning
 * This function MUST NOT be called from ISR context.
 */
int imu_init(void);
```

---

## 21. Mandatory Doxygen Tags (Function-Level)

### Rule FNC-3.1 — Required Tags

Every documented public function **MUST** include:

| Tag        | Requirement                          |
| ---------- | ------------------------------------ |
| `@brief`   | Single-sentence summary              |
| `@details` | Behavioral description               |
| `@return`  | Required if function returns a value |
| `@pre`     | Required if preconditions exist      |
| `@post`    | Required if postconditions exist     |

---

### Rule FNC-3.2 — Conditional Tags

The following tags **MUST** be used when applicable:

| Tag        | Condition                            |
| ---------- | ------------------------------------ |
| `@param`   | For every function parameter         |
| `@retval`  | When returning multiple named values |
| `@note`    | For assumptions or usage notes       |
| `@warning` | For unsafe or restricted usage       |

---

## 22. `@brief` Rules

### Rule FNC-4.1 — `@brief` Content

The `@brief` description **MUST**:

* Be a single sentence
* Use the imperative mood
* Describe *what* the function does, not *how*
#### Correct

```c
@brief Reads raw accelerometer data from the IMU.


```
#### Incorrect

```c
@brief This function reads registers and shifts bits.


```
---

## 23. `@param` Documentation Rules

### Rule FNC-5.1 — Mandatory Parameter Documentation

Every function parameter **MUST** be documented using `@param`.

Each `@param` **MUST** specify:

* Direction (`[in]`, `[out]`, `[in,out]`)
* Expected units (if applicable)
* Valid range or constraints

#### Example

```c
/**
 * @param[out] ax Acceleration along X axis in m/s^2.
 * @param[out] ay Acceleration along Y axis in m/s^2.
 * @param[out] az Acceleration along Z axis in m/s^2.
 */
int imu_read_accel(float *ax, float *ay, float *az);
```

---

## 24. `@return` vs `@retval`

### Rule FNC-6.1 — `@return`

The `@return` tag **MUST** be used to describe:

* The meaning of the return value
* The general success/failure semantics

---

### Rule FNC-6.2 — `@retval`

The `@retval` tag **MUST** be used when specific return values have defined meanings.

#### Example

```c
/**
 * @retval 0 Success
 * @retval -EINVAL Invalid argument
 * @retval -EIO I2C communication failure
 */
```

---

## 25. Error Handling Documentation

### Rule FNC-7.1 — Error Semantics

Functions **MUST** document:

* All possible error conditions
* Whether errors are recoverable
* Whether the module enters a faulted state

Implicit error behavior is **FORBIDDEN**.

---

## 26. Preconditions and Postconditions

### Rule FNC-8.1 — Preconditions (`@pre`)

All assumptions required for correct operation **MUST** be documented using `@pre`.

Examples:

* Initialization order
* Required hardware state
* Valid pointer arguments

---

### Rule FNC-8.2 — Postconditions (`@post`)

All observable effects **MUST** be documented using `@post`.

Examples:

* State changes
* Resource ownership changes
* Side effects

---

## 27. Side Effects and Global State

### Rule FNC-9.1 — Side Effect Documentation

Functions that:

* Modify global state
* Access hardware
* Block execution
* Acquire resources

**MUST** explicitly document side effects in `@details` or `@note`.

---

## 28. Private Function Documentation

### Rule FNC-10.1 — Private Functions

Private (file-static) functions:

* **SHOULD** be documented if non-trivial
* **MUST** be documented if safety-critical
* **MUST NOT** be documented as public APIs

---

## 29. Prohibited Practices

### Rule FNC-11.1 — Prohibited Content

Function documentation **MUST NOT**:

* Restate the function name
* Describe implementation steps
* Contain redundant information
* Contradict the code behavior

---

## 30. Function-Level Review Checklist

A function **MUST be rejected** if:

* It is public and undocumented
* Parameters lack direction or constraints
* Error behavior is undocumented
* Preconditions are implicit
* Side effects are undocumented

---

**End of Phase 3 — Function-Level Documentation**

# Phase 4 — Type, Struct, Enum, and Macro Documentation  
*(NASA C Style, Doxygen-Compatible)*

---

## 31. Purpose of Type-Level Documentation

Type-level documentation defines the **meaning of data**, independent of functions.

In embedded and safety-critical systems, **data semantics are as important as code logic**.
Incorrect assumptions about units, ranges, or ownership are a common cause of failure.

Type-level documentation applies to:
- `struct`
- `enum`
- `typedef`
- `#define` macros
- Public constants

---

## 32. General Rules for Public Types

### Rule T-1.1 — Mandatory Documentation for Public Types

All **publicly visible** types **MUST** be documented.

This includes any type declared in:
- A public header file
- A module interface

Undocumented public types are **FORBIDDEN**.

---

### Rule T-1.2 — Location of Type Documentation

Type documentation **MUST**:
- Appear immediately before the type declaration
- Use a Doxygen-compatible comment block
- Be the authoritative source of truth

---

## 33. Units, Ranges, and Scaling (MANDATORY)

### Rule T-2.1 — Units

All public data **MUST** explicitly document units.

Examples:
- meters (`m`)
- meters per second squared (`m/s^2`)
- radians
- degrees
- raw ADC counts

Implicit units are **FORBIDDEN**.

---

### Rule T-2.2 — Valid Ranges

All public data **MUST** document:
- Minimum valid value
- Maximum valid value
- Behavior on out-of-range values (if applicable)

---

### Rule T-2.3 — Scaling and Encoding

If a value is:
- Scaled
- Encoded
- Fixed-point
- Bit-packed

The scaling **MUST** be documented explicitly.

---

## 34. Struct Documentation

### Rule T-3.1 — Struct-Level Documentation

Every public `struct` **MUST** have a Doxygen comment block describing:
- What the struct represents
- Ownership and lifetime
- Any invariants

---

### Rule T-3.2 — Member-Level Documentation

Every public struct member **MUST** be documented.

Member documentation **MUST** include:
- Meaning
- Units
- Valid range

---

### Required Struct Documentation Template

```c
/**
 * @brief IMU accelerometer measurement.
 *
 * @details
 * Represents a single accelerometer sample expressed in the body frame.
 */
typedef struct
{
    /** Acceleration along X axis in m/s^2. Range: [-160, 160]. */
    float ax;

    /** Acceleration along Y axis in m/s^2. Range: [-160, 160]. */
    float ay;

    /** Acceleration along Z axis in m/s^2. Range: [-160, 160]. */
    float az;
} imu_accel_t;
```

---

## 35. Enum Documentation

### Rule T-4.1 — Enum-Level Documentation

Every public `enum` **MUST** be documented with:

* Overall meaning
* Usage context

---

### Rule T-4.2 — Enum Value Documentation

Every enum value **MUST** be individually documented.

Undocumented enum values are **FORBIDDEN**.

---

### Required Enum Documentation Template

```c
/**
 * @brief IMU operating modes.
 */
typedef enum
{
    /** IMU is powered down. */
    IMU_MODE_OFF = 0,

    /** IMU is initialized but not sampling. */
    IMU_MODE_IDLE,

    /** IMU is actively sampling sensor data. */
    IMU_MODE_ACTIVE
} imu_mode_t;
```
---

## 36. Typedef Documentation

### Rule T-5.1 — Typedef Purpose

All public `typedef`s **MUST** document:

* Why the alias exists
* What semantic meaning it adds

Typedefs **MUST NOT** be used solely for brevity.

---

### Correct Example

```c
/**
 * @brief Timestamp in microseconds since system boot.
 */
typedef uint64_t timestamp_us_t;
```

---

## 37. Macro Documentation

### Rule T-6.1 — Public Macros

All public macros **MUST** be documented.

Macro documentation **MUST** include:

* Purpose
* Units (if applicable)
* Valid range

---

### Rule T-6.2 — Macros vs Constants

Macros **MUST NOT** be used where:

* A `const` variable
* An `enum`

Would be safer or clearer.

---

### Required Macro Documentation Template

```c
/** IMU maximum supported output rate in Hz. */
#define IMU_MAX_ODR_HZ 1000U
```

---

## 38. Bitfields and Packed Data

### Rule T-7.1 — Bitfield Documentation

Bitfields **MUST** document:

* Bit position
* Meaning
* Valid values

Implicit bit meaning is **FORBIDDEN**.

---

### Example

```c
/**
 * @brief IMU status register flags.
 */
typedef struct
{
    /** Data ready flag (bit 0). */
    uint8_t data_ready : 1;

    /** Overrun error flag (bit 1). */
    uint8_t overrun    : 1;
} imu_status_t;
```

---

## 39. Private Types

### Rule T-8.1 — Private Types

Private types:

* **SHOULD** be documented if non-trivial
* **MUST** be documented if safety-critical
* **MUST NOT** appear in public headers

---

## 40. Prohibited Practices

### Rule T-9.1 — Prohibited Content

Type documentation **MUST NOT**:

* Restate the type name
* Describe how values are computed
* Duplicate function documentation
* Contradict implementation behavior

---

## 41. Type-Level Review Checklist

A change **MUST be rejected** if:

* Public types lack unit or range information
* Enum values are undocumented
* Struct members are undocumented
* Scaling is implicit
* Macros are misused for typed values

---

**End of Phase 4 — Type, Struct, Enum, and Macro Documentation**

# Phase 5 — Inline & Control-Flow Commenting  
*(NASA C Style, Doxygen-Compatible)*

---

## 42. Purpose of Inline Comments

Inline comments exist to explain **intent, rationale, and constraints** that
cannot be expressed clearly through code alone.

Inline comments **do not document interfaces** — they document **decisions**.

---

## 43. General Rules for Inline Comments

### Rule I-1.1 — Intent Over Mechanics

Inline comments **MUST** explain:
- *Why* the code exists
- *Why* a decision was made
- *Why* a constraint exists

Inline comments **MUST NOT** explain:
- What the code is syntactically doing
- Restate obvious operations
- Mirror the code line-by-line

---

### Rule I-1.2 — Comment Quality

Inline comments **MUST**:
- Be concise
- Be accurate
- Be colocated with the code they describe

Inline comments **MUST NOT**:
- Drift from the code
- Contradict the implementation
- Become stale

---

## 44. Single-Line Comments

### Rule I-2.1 — Style

Single-line comments **MUST** use `//`.

```c
// Wait for sensor startup time before first access
delay_ms(IMU_STARTUP_DELAY_MS);
```

---

### Rule I-2.2 — Placement

Single-line comments **MUST**:

* Appear on the line above the code
* Not trail code on the same line (except for short clarifications)

#### Preferred

```c
// Clamp value to prevent overflow in fixed-point math
value = clamp(value, MIN_VAL, MAX_VAL);
```

#### Discouraged

```c
value = clamp(value, MIN_VAL, MAX_VAL); // clamp value
```
---

## 45. Block Comments

### Rule I-3.1 — Usage

Block comments (`/* ... */`) **MAY** be used to:

* Explain a complex algorithm
* Describe a multi-step sequence
* Explain non-obvious control flow

Block comments **MUST NOT** be used for:

* File-level documentation
* Function documentation
* Single-line explanations

---

### Example

```c
/*
 * The following sequence resets the IMU by toggling the power domain.
 * A delay is required between transitions to meet the datasheet
 * minimum reset pulse width.
 */
imu_power_off();
delay_ms(10);
imu_power_on();
```

---

## 46. Conditional Logic (`if`, `else`, `switch`)

### Rule I-4.1 — Non-Trivial Conditions

Conditionals with non-obvious intent **MUST** be commented.

```c
// Enter failsafe if sensor data is stale for more than 100 ms
if (timestamp_diff(now, last_update) > FAILSAFE_TIMEOUT_MS)
{
    enter_failsafe();
}
```

---

### Rule I-4.2 — `else` and Default Cases

`else` and `default` branches **MUST** be commented if:

* They represent error handling
* They represent unexpected states

```c
default:
    // Unexpected state — enter safe mode
    system_safe_mode();
    break;
```

---

## 47. Loops

### Rule I-5.1 — Loop Purpose

All non-trivial loops **MUST** be commented with:

* Loop intent
* Termination condition

```c
// Poll IMU until data-ready flag is set or timeout expires
while (!imu_data_ready())
{
    if (timeout_expired())
    {
        return -ETIMEDOUT;
    }
}
```

---

### Rule I-5.2 — Loop Invariants

If a loop relies on an invariant, it **MUST** be documented.

```c
// Invariant: buffer_index always points to next free slot
for (uint32_t i = 0; i < buffer_len; i++)
{
    buffer[buffer_index++] = data[i];
}
```

---

## 48. State Machines

### Rule I-6.1 — State Machine Documentation

State machines **MUST** be documented using inline comments that describe:

* State purpose
* Transition conditions
* Failure paths

```c
// STATE_INIT:
// - Power on sensor
// - Wait for startup delay
// Transition to STATE_IDLE on success
```

---

### Rule I-6.2 — State Transitions

Transitions **MUST** be commented when:

* They are conditional
* They are safety-critical

```c
// Transition to FAILSAFE if initialization fails
state = STATE_FAILSAFE;
```

---

## 49. Error Handling Paths

### Rule I-7.1 — Error Path Documentation

Error-handling code **MUST** be commented to explain:

* Why the error is handled here
* Why the chosen recovery action is safe

```c
// I2C failure is non-recoverable at runtime — enter safe mode
enter_safe_mode();
```

---

## 50. Timing and Ordering Constraints

### Rule I-8.1 — Timing Constraints

Code that depends on:

* Timing
* Ordering
* Hardware delays

**MUST** be commented with the rationale.

```c
// Datasheet requires 5 ms delay after reset before first register access
delay_ms(5);
```

---

## 51. Magic Numbers

### Rule I-9.1 — Prohibition of Magic Numbers

Magic numbers **MUST NOT** appear without explanation.

```c
// 14-bit ADC resolution (0–16383)
raw_value &= 0x3FFF;
```

---

## 52. Commenting What Not to Do

### Rule I-10.1 — Forbidden Inline Comments

Inline comments **MUST NOT**:

* State the obvious
* Repeat the code
* Explain syntax

#### Forbidden

```c
i++; // increment i
```


### Rule I-11.1 — TODO / FIXME / HACK / NOTE Discipline

Temporary-work comments **MUST** follow a standard format.

Allowed tags are:
* `TODO` – planned improvement or follow-up work
* `FIXME` – known bug or incorrect behavior
* `HACK` – short-term workaround that must be removed
* `NOTE` – important clarification that is not a task

Task-bearing tags (`TODO`, `FIXME`, `HACK`) **MUST** include:
* A responsible owner or team identifier
* A tracking reference (ticket/issue ID or equivalent)
* A clear removal condition (what “done” means)

Task-bearing tags **MUST NOT** be used as a substitute for proper design documentation.

#### Required Format

```c
// TODO(<owner>): <action> [<ticket-id>] — Remove when <removal-condition>.
```

#### Example

```c
// FIXME(sensor): Handle FIFO overflow [IMU-214] — Remove when overflow counter is verified in HIL.
```
---

## 53. Inline Comment Review Checklist

Code **MUST be rejected** if:

* Control flow is non-obvious and uncommented
* Error paths lack rationale
* Timing constraints are undocumented
* Magic numbers are unexplained
* Comments explain *what* instead of *why*

---

**End of Phase 5 — Inline & Control-Flow Commenting**

# Phase 6 — Embedded & Safety-Critical Commenting  
*(NASA C Style, Doxygen-Compatible)*

---

## 54. Purpose of Safety-Critical Commenting

Safety-critical commenting documents **hazards, constraints, and fault behavior**
that cannot be inferred from code alone.

This phase applies to:
- Interrupt Service Routines (ISRs)
- Hardware register access
- Timing-critical code
- Power-on / reset sequences
- Watchdogs
- Fault handling and failsafe logic

Failure to document safety-relevant behavior is a **system-level risk**.

---

## 55. Interrupt Service Routines (ISRs)

### Rule E-1.1 — Mandatory ISR Documentation

Every ISR **MUST** be documented with:
- Trigger source
- Execution constraints
- Allowed and forbidden operations
- Worst-case execution assumptions

---

### Required ISR Documentation Template

```c
/**
 * @brief IMU data-ready interrupt handler.
 *
 * @details
 * Triggered by the IMU when new sensor data is available.
 *
 * @note
 * This ISR performs minimal work and defers processing to the main loop.
 *
 * @warning
 * This ISR MUST NOT:
 * - Block
 * - Allocate memory
 * - Call non-reentrant functions
 */
void IMU_DRDY_IRQHandler(void);
```

---

### Rule E-1.2 — ISR Inline Comments

ISR bodies **MUST** contain inline comments explaining:

* Why work is deferred
* Why specific operations are safe in ISR context

---

## 56. Hardware Register Access

### Rule E-2.1 — Register Access Documentation

All direct hardware register access **MUST** be commented with:

* Register purpose
* Bit meaning
* Source (datasheet reference or section)

---

### Example

```c
// CTRL_REG1: Enables accelerometer and sets output data rate
// Datasheet XYZ-IMU, Section 7.3.1
write_reg(IMU_CTRL_REG1, CTRL_REG1_ACC_EN | CTRL_REG1_ODR_1KHZ);

```
---

### Rule E-2.2 — Bit Manipulation

Bit masks and shifts **MUST** be commented if their meaning is not obvious.

Implicit bit usage is **FORBIDDEN**.

---

## 57. Timing-Critical Code

### Rule E-3.1 — Timing Assumptions

Code that depends on:

* Delays
* Execution order
* Hardware timing

**MUST** document the timing rationale.

---

### Example

```c
// Datasheet requires minimum 2 ms delay after power-up
delay_ms(2);
```

---

### Rule E-3.2 — Busy-Wait Loops

Busy-wait loops **MUST** document:

* Why blocking is acceptable
* Maximum wait time
* Exit conditions

---

## 58. Power-On and Reset Sequences

### Rule E-4.1 — Startup Sequence Documentation

Power-on and reset code **MUST** be commented with:

* Sequence order
* Rationale for ordering
* Failure handling behavior

---

### Example

```c
// Power-on sequence:
// 1. Enable regulator
// 2. Wait for voltage stabilization
// 3. Release IMU reset
```

---

### Rule E-4.2 — Reset Side Effects

Reset routines **MUST** document:

* Which states are cleared
* Which states persist
* Any hardware that requires reinitialization

---

## 59. Watchdog Usage

### Rule E-5.1 — Watchdog Intent

Watchdog-related code **MUST** document:

* Why the watchdog exists
* What failure it protects against
* Reset consequences

---

### Example

```c
// Refresh watchdog to prevent reset during long SD card write
kick_watchdog();
```

---

### Rule E-5.2 — Watchdog Disablement

Any code that disables or pauses a watchdog **MUST**:

* Be explicitly commented
* Justify why this is safe
* Be reviewed as safety-critical

---

## 60. Fault Handling and Failsafe Logic

### Rule E-6.1 — Fault Path Documentation

Fault-handling code **MUST** document:

* What fault occurred
* Why the chosen response is safe
* Whether recovery is possible

---

### Example

```c
// IMU communication failure is non-recoverable in flight.
// Transition to safe mode to prevent invalid navigation data.
enter_safe_mode();
```

---

### Rule E-6.2 — Silent Failures

Silent failure handling is **FORBIDDEN**.

All fault paths **MUST**:

* Be explicit
* Be commented
* Lead to a defined system state

---

## 61. Resource Ownership and Lifetime

### Rule E-7.1 — Resource Documentation

Code that acquires hardware or system resources **MUST** document:

* Ownership
* Lifetime
* Release conditions

---

## 62. Concurrency and Shared State

### Rule E-8.1 — Shared State Access

Access to shared state **MUST** be commented when:

* Accessed from multiple contexts
* Accessed from ISR and non-ISR code

---

### Example

```c
// Protected by disabling interrupts to prevent race with ISR
disable_irq();
shared_counter++;
enable_irq();
```

---

### Rule E-8.2 — Locking, Critical Sections, and Lock Ordering

When shared state is protected by a lock, critical section, or interrupt masking, comments **MUST** state:
* What is being protected
* The protection mechanism (mutex/spinlock/IRQ mask/etc.)
* Any lock ordering rules required to avoid deadlock

---

### Rule E-8.3 — Atomicity and Memory Ordering

When correctness depends on atomic operations or memory ordering, comments **MUST** document:
* Which variables are atomic and why
* Which ordering guarantees are required (e.g., producer/consumer visibility)
* Which contexts access the data (tasks/threads/ISR/DMA)

---

### Rule E-8.4 — Reentrancy and Thread-Safety Declarations

Public APIs and any function used across execution contexts **MUST** declare whether they are:
* Reentrant / non-reentrant
* Thread-safe / thread-compatible (and what synchronization is required)
* ISR-safe / ISR-unsafe

This information **SHOULD** be stated in the function’s Doxygen block (`@note` / `@warning`) or the module documentation.

## 63. Defensive Programming Constructs

### Rule E-9.1 — Defensive Checks

Defensive checks (asserts, guards, sanity checks) **MUST** document:

* What invariant is being enforced
* What failure indicates

---

### Example

```c
// Assert ensures buffer index never exceeds allocated memory
assert(buffer_index < BUFFER_SIZE);
```

---

### Rule E-9.2 — Logging and Telemetry Message Intent

Where logging or telemetry is used to report faults, safety states, or degraded modes, messages **MUST** be meaningful and actionable.

Messages **MUST** communicate:
* What happened (condition/event)
* What the system did (mitigation/safe-state action)
* What the operator or integrator should infer (impact/severity), when applicable

Logs and telemetry **MUST NOT** replace required documentation of hazards, fault behavior, or constraints.

#### Example

```c
// Enter safe mode on repeated IMU read failures to prevent using stale attitude data
LOG_ERR("imu: read failed %u times; entering SAFE_MODE", fail_count);
enter_safe_mode();
```

## 64. Prohibited Practices (Safety Context)

### Rule E-10.1 — Forbidden Constructs

In safety-critical code, comments **MUST NOT**:

* Downplay risk (“should be fine”)
* Express uncertainty (“probably safe”)
* Justify undefined behavior

---

## 65. Safety-Critical Review Checklist

Code **MUST be rejected** if:

* ISRs lack documentation
* Register accesses are undocumented
* Timing assumptions are implicit
* Fault paths are uncommented
* Watchdog behavior is unclear
* Silent failures exist

---

## 66. Compliance

All safety-relevant code **MUST** comply with this phase.
Deviations **MUST** be formally reviewed and approved.

---
| Phase | What it Covers | Applies To        |
| ----- | -------------- | ----------------- |
| 1     | Files          | All code          |
| 2     | Modules        | Public headers    |
| 3     | Functions      | Public APIs       |
| 4     | Data           | Public types      |
| 5     | Inline         | Non-trivial logic |
| 6     | Safety         | Embedded / ISR    |
| 7     | Tooling        | CI / Docs         |
---
**End of Phase 6 — Embedded & Safety-Critical Commenting**

# Phase 7 — Tooling, Documentation Pipeline, and Enforcement  
*(NASA C Style, Doxygen-Compatible)*

---

## 67. Purpose of Tooling and Enforcement

Commenting standards are only effective if they are:
- Automatically checked
- Consistently rendered
- Enforced during development

This phase defines:
- Required tooling expectations
- Doxygen configuration assumptions
- Sphinx / ReadTheDocs integration
- Review and CI enforcement rules

---

## 68. Documentation Toolchain Overview

### Rule P-1.1 — Supported Toolchain

The official documentation pipeline **MUST** be:

```

C Source Code
↓
Doxygen (API extraction)
↓
XML / Markdown
↓
Sphinx
↓
ReadTheDocs (or equivalent)

```

Any alternative pipeline **MUST** produce equivalent output.

---

## 69. Doxygen Configuration Requirements

### Rule P-2.1 — Mandatory Doxygen Settings

The following Doxygen configuration options **MUST** be enabled:

```

EXTRACT_ALL            = YES
EXTRACT_PRIVATE        = NO
EXTRACT_STATIC         = NO
HIDE_UNDOC_MEMBERS     = YES
WARN_IF_UNDOCUMENTED   = YES
WARN_IF_DOC_ERROR      = YES
GENERATE_XML           = YES
GENERATE_HTML          = YES
OPTIMIZE_OUTPUT_FOR_C  = YES

```

---

### Rule P-2.2 — Warning Policy

Doxygen warnings **MUST** be treated as:
- **Errors** in CI
- **Blocking issues** in code review

Undocumented public symbols are **NOT ALLOWED**.

---

## 70. Directory Structure Expectations

### Rule P-3.1 — Recommended Documentation Layout

Projects **SHOULD** follow this layout:

```text
docs/
├── coding-standards/
│   └── comments.md
├── api/
│   └── doxygen/
│       └── Doxyfile
└── sphinx/
    ├── conf.py
    └── index.rst
```
---

### Rule P-3.2 — Source Code Inclusion

Only public headers **SHOULD** be included in Doxygen input paths.

Private headers and implementation files **MUST NOT** define public API documentation.

---

## 71. Sphinx / ReadTheDocs Integration

### Rule P-4.1 — API Documentation Source

API documentation rendered by Sphinx **MUST** be generated from:

* Doxygen XML output
* Not manually written API text

Manual duplication of API documentation is **FORBIDDEN**.

---

### Rule P-4.2 — Single Source of Truth

Source code comments **ARE** the single source of truth.

Any contradiction between:

* Code comments
* Rendered documentation

**MUST** be resolved in favor of the code.

---

## 72. Review Enforcement Rules

### Rule P-5.1 — Mandatory Review Checks

Code **MUST be rejected** during review if:

* Any public symbol lacks required documentation
* Documentation contradicts implementation
* Units, ranges, or constraints are missing
* Error behavior is undocumented
* Safety-critical code lacks rationale

---

### Rule P-5.2 — Review Language

Review comments **SHOULD** reference rule IDs:

> “Violation of Rule FNC-5.1 — undocumented parameters.”

This ensures objective, non-personal reviews.

---

## 73. Continuous Integration (CI) Enforcement

### Rule P-6.1 — CI Requirements

CI pipelines **SHOULD**:

* Run Doxygen
* Fail on documentation warnings
* Publish generated documentation artifacts

---

### Rule P-6.2 — Documentation Drift

CI **SHOULD** detect:

* New undocumented symbols
* Removed documentation
* Broken Doxygen references

Documentation drift is considered a **regression**.

---

### Rule P-6.3 — Minimum Mandatory CI Gates

At minimum, CI **MUST**:

* Run Doxygen on the repository (or on the affected documentation inputs)
* Fail the build on documentation warnings for public headers and any files changed in the PR
* Fail the build if new public symbols are introduced without required documentation
* Publish generated documentation artifacts for `main`/release builds (or equivalent protected branches)

---

### Rule P-6.4 — Legacy Code and Incremental Adoption

For legacy codebases, CI **MAY** scope strict documentation enforcement to:

* Newly added files
* Modified files
* Public headers and public API surfaces

However, any file touched by a change **MUST** be brought into compliance with this standard.

---

### Rule P-6.5 — Test and Non-Production Code

Tests and non-production code **MUST** be readable and self-explanatory.

Test code **MUST** document:
* The intent of the test (what behavior is being validated)
* The scenario being exercised (inputs/setup)
* The expected result (including tolerances for numeric comparisons)
* Why the chosen edge cases matter

Test helper libraries that are reused across modules **SHOULD** follow the same public API documentation rules as production code.

## 74. Deviations and Exceptions

### Rule P-7.1 — Deviation Handling

Deviations from this standard:

* **MUST** be explicitly documented
* **MUST** include technical justification
* **MUST** be approved by a technical lead

---

### Rule P-7.2 — Temporary Deviations

Temporary deviations **MUST**:

* Be tracked
* Include a removal plan
* Not persist indefinitely

---

### Rule P-7.3 — Third-Party / Vendor Code

Third-party or vendor-managed code **MAY** be exempt from full compliance when:
* The license prohibits modification of headers/comments
* The code is imported and maintained externally
* The code is machine-generated by a vendor toolchain

Even when exempt, such code **MUST**:
* Be isolated (separate directory/module boundary) to prevent mixing styles
* Be wrapped by an internal interface that **MUST** follow this standard
* Include a top-level `README` or wrapper header note describing provenance and version

---

### Rule P-7.4 — Auto-Generated Code

Auto-generated code **MAY** use generator-provided headers and comment style.

Auto-generated code **MUST**:
* Clearly identify that it is generated
* Document the generator/tool and version (if available)
* Identify the source inputs (configuration files, models, schemas) when practical
* Forbid manual edits inside generated regions (unless explicitly supported by the generator)

Any manual changes to generated output **MUST** be treated as a deviation (Rule P-7.1) and include a plan to upstream or regenerate safely.

## 75. Onboarding and Training

### Rule P-8.1 — Developer Awareness

All developers **MUST** be made aware of this standard.

New team members **SHOULD**:

* Review `comments.md`
* Follow provided templates
* Use existing code as reference

---

## 76. Compliance Statement

Compliance with this standard is:

* **Mandatory** for all production code
* **Expected** for all prototypes
* **Required** for all safety-critical systems

Non-compliant code **MUST NOT** be merged.

---

## 77. Document Ownership

This document **MUST**:

* Be version-controlled
* Have a designated technical owner
* Be updated through formal review

---

*End of Phase 7 — Tooling, Documentation Pipeline, and Enforcement*
---
**End of Coding Comment Standards**
