# How it works
## Motor drive control
The control circuit consists of a DRV8251A motor driver, which has two inputs (IN1 and IN2) allowing to drive the motor as follows:
| IN1 | IN2 | Action         |
|-----|-----|----------------|
| 0   | 0   | Coast          |
| 1   | 0   | Spin (normal)  |
| 0   | 1   | Spin (reverse) |
| 1   | 1   | Brake          |

During normal operation, the user buttons directly control the IN1 and IN2 inputs of the chip. However, during stall conditions, the buttons are overruled: IN1 and IN2 are forced to zero in this case, resulting in the motor to 'coast' (which means: it is not actively driven).

The input circuit for user button 1 looks like this (a copy of this circuit is used for the other button):

<img width="450" alt="image" src="https://github.com/user-attachments/assets/29f77d84-3008-4018-8648-db9004d66240">

The input signal SW1_N is assumed to be active-low and has a pull-up (R1). Furthermore, there is a TVS diode (D6) against EMI surges and a low-pass filter (R16/C11) to filter noise and input bounce. It has a MOSFET acting as an inverter (Q1) to accommodate for the IN1/IN2 polarity of the motor driver, and a status LED (D1). Voltage divider R3/R4 ensures the input voltage at SW1 (0-9V) is scaled down to keep the input voltage on IN1 within the allowed input range (0-5.5V) of the DRV8251A. Series resistor R3 also allows to safely short IN1 to ground (using N-channel MOSFET Q2) and overrule the pin during stall events. 

## Stall detection
The DRV8251A chip internally uses [current mirrors](https://en.wikipedia.org/wiki/Current_mirror) to eliminate the need for shunt resistors to measure instantaneous motor current. It has an output pin called "IPROPI" through which a current flows that is proportional to the instantaneous motor current (scaling factor: 1575 µA/A typ.).

<img width="754" alt="image" src="https://github.com/user-attachments/assets/30c22100-163a-4acb-8d0b-f378971271da">

Using Ohm's law, the output current of this pin can easily be converted to a voltage by placing a parallel resistor to ground. The voltage on this pin now becomes 

$`V_{IPROPI} = \frac{IPROPI}{R_{IPROPI}}`$,

or, calculated back into the actual motor current:
$`I_{\mathrm{motor}} = \frac{V_{IPROPI}}{R_{IPROPI} \cdot {1575 \cdot 10^{-6}}}`$

This is fed to a comparator which defines a hard threshold between 'stalled' and 'not stalled'. The output of the comparator is high (9V) when V+ > V-, and 0V otherwise. V- is the voltage proportional to the motor current, and V+ defines the 'trip current'. So, when the motor current is higher than the trip current, the output of the comparator becomes LOW.

<img width="1017" alt="image" src="https://github.com/user-attachments/assets/4df15498-6161-47d1-9c34-984b07b6e1d1">

The comparator output is then connected through an RC filter to a 555 chip. The filter is required to smooth out glitches and initial current peaks when the motor starts spinning. Note that if you don't include this filter, the stall circuit would trigger immediately when you press a button!

The 555 chip is used as 'set-reset' flip flop. This means the output is high (and stays high) whenever the 'trigger' input gets below 1/3 of the supply voltage (so here: 3V), and becomes low when the 'threshold' pin gets higher than 2/3 of supply voltage (here: 6V). So, in essence, the 'trigger' input acts as an inverted 'set' signal of a regular flip flop, while the 'threshold' pin acts as the reset signal of the flip flop.

In this circuit, the trigger pin falls below 3V when the comparator indicates an overcurrent condition during a sufficiently long period. When this happens, the 'STALL' signal becomes and remains active despite the motor getting disabled and soon not drawing current anymore.

To make the STALL signal inactive again, the 'threshold' pin needs to rise to a voltage of at least 6V. Due to the N-channel MOSFETs at the bottom of the circuit, the threshold pin sees a low voltage as soon as one of the user inputs are active. Both buttons must be released to allow the pin to charge (through an RC low-pass filter) back to a voltage higher than 6V.

This mechanism effectively stops the motor until new button presses.
