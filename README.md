# Motor Driver with Stall Detection
This motor driver allows to safely operate small, brushed DC motors with just two inputs. It has current-based stall detection that automatically stops driving the motor when the end of travel is reached or an excessive load is sensed.

Keep one button pressed and the motor spins in one direction. Press the other button and the motor spins in the reverse direction. Whenever the motor blocks, it is switched off until the button is released. This prevents the stalled motor from heating up and causing the windings to burn out.

In many situations, this also removes the need to install mechanical or optical endstops.

The circuit is built around a DRV8251A motor driver, a few filters and discrete logic components.

Please note that this simple board was originally intended to be manually operated by physical buttons. Technically, you can apply PWM inputs to have speed control, but the stall detection mechanism has not been designed for this and will have degraded performance.

## Features
- Operated by two button inputs, one for each motor direction
- Detects end of travel without adding mechanical or optical endstops
- Robust DRV8251A motor driver chip
- Configurable current limit, limiting peak motor current at any time
- Extra on-board fuse, as a fail-safe
- No microcontroller, no firmware
- Tested with 12V, 2A DC motors
- Open-drain stall status output
- Stall threshold and filtering tunable with a set of trimpots

## Theory of operation
For an explanation on how the circuit works, see [this page](THEORY.md).

## Build results

## Build instructions
