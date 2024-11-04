# Motor Driver with Stall Detection
This motor driver safely operates small, brushed 5-24V DC motors up to 2A. It has current-based stall detection that automatically stops driving the motor when the end of travel or an excessive load is sensed.

The board has two inputs that make the motor spin in either direction. When a stall condition is detected, the motor is disabled until both inputs are released. This prevents the stalled motor from heating up and causing the windings to burn out.

In many situations, this removes the need to install mechanical or optical end-stops.

The circuit is built around a DRV8251A motor driver IC, with a few filters and discrete logic components.

>[!NOTE]
>This board was originally intended to be operated by physical buttons. Speed control through PWM is not supported.

## Features
- Robust DRV8251A motor driver chip
- Detects end of travel without adding mechanical or optical end-stops using motor stall current detection
- Operated by two (active-low) inputs, one for each motor direction
- LED indication for both inputs, and for stall conditions
- Simple setup: no microcontroller, no firmware
- Stall threshold, filtering and current limit tuneable with a set of trimpots
- Extra on-board fuse, as a fail-safe
- Open-drain stall status output

## Build overview
<img src="https://github.com/user-attachments/assets/0d0f84a4-08f4-43fc-b6d8-f2c6ce13c2c8" width="337" />
<img src="https://github.com/user-attachments/assets/ebf6cf4f-164d-4988-aba5-6d88f41215bf" width="462" />

Navigate to the releases section to obtain the latest schematic and production files.

## How it works
For an explanation on how the circuit works, see [this page](THEORY.md).

## How to use
### Connections
After assembling the board, power (5-24VDC) must be applied to J1 at pins 'VIN' and 'GND'. The motor leads are to be connected to the same connector, at the pins indicated by 'MOTOR'.

Inputs are to be connected to J2, on pins IN1 and IN2. Inputs are considered active when they are pulled to GND (active-low). If switches are used, place these between GND and IN1/IN2.

### Tuning
The circuit has to be tuned before use:
1. Set the I-LIM trimpot so that the voltage at TP2 matches following formula: $V_{TP2} = I_{max}R_{R6}\cdot 1575\cdot 10^{-6}$.<br />Here, $I_{max}$ is the current limit you want to use for your motor, and $R_{R6}$ is normally 1K. To be safe, you can take $I_{max}$ around the rated stall current of your motor.
2. Then, first turn the T-TRIP trimpot completely clockwise to set the trip time to the maximum
3. Turn the I-TRIP trimpot almost completely clockwise to set the trip current to a very low level
4. Tune the trip current<br />
   a. Be sure that the motor can spin freely<br />
   b. Use IN1/IN2 to spin the motor. If it stalls, release the button, turn I-TRIP a bit counter-clockwise and try again until it no longer stalls.<br />
   c. After finding an acceptable stall threshold, turn I-TRIP a bit more to have some margin.
5. Tune the trip time if necessary<br />
   a. To make the circuit detect stalls earlier, turn the T-TRIP time counter-clockwise<br />
   b. When reducing this time, it's possible that you need to increase the trip current from step 4 a bit, to filter out the initial current peak when the motor starts spinning.
