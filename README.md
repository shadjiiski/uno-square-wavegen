# uno-square-wavegen
Square wave generator for Arduino/Genuino UNO based on the Atmega 328 built-in PWM functionality on Timer1

Overview
========
uno-square-wavegen sets PWM on Timer1 of the Atmega328 microcontroller (the heart of the Arduino Uno) to transform a regular Arduino Uno into a square wave generator with controllable frequency and duty cycle. Output is fixed: pin9 (output A) and pin 10 (output B). Both outputs share the same frequency, however, they can have different duty cycles.

Boundaries
==========
min frequency: 0.2 Hz
max frequency: 8000 Hz
min duty cycle: 0
max duty cycle: 1

Precision
=========
two decimal places

How to set frequency and duty cycle
===================================
The wave generator can be tuned via serial communication. Just send messages like this:
**frequency:dutyA[:dutyB]**
frequency, dutyA and dutyB are integers, that are a thousand times larger than the values that you wish to set. For example, if you want to set 10.23 Hz and 0.59 duty cycle, pass
**10230:590**
This is done to fix the precision and to avoid float point numbers.
_Note: dutyB is optional. If it is not set, both ouptuts will have the same duty cycle_

Communication protocol
======================

Echo
----
Every command (known or unknown) will be echoed back with a 'Echo:' prefix (without the quotes)

Tuning
------
1. You send the tuning command **frequency:dutyA[:dutyB]**
1. You receive **Echo:frequency:dutyA[:dutyB]**
1. The wave generator tunes itself (if possible)
1. You receive the current state **Frequency:< frequency >:DutyA:< dutyA >:DutyB:< dutyB >** (with < varX > expanded to integer values) 
1. You use the current state to determine whether you were able to tune the wave generator

Getting current state
---------------------
1. You send the command **STATE**
1. You receive **Echo:STATE**
1. You receive the current state **Frequency:< frequency >:DutyA:< dutyA >:DutyB:< dutyB >** (with < varX > expanded to integer values)

Getting debug information (all vars, all PWM related registers)
------------------------------------------------------------------------------
1. You send the command **DEBUG**
2. You receive **Echo:DEBUG**
3. You receive all the debug information in a single line with the following format: **...:PropertyName:< value >:...**

Additional notes
----------------
* Every message from uno-square-wavegen is terminated by a new line
* When you send commands you do not have to terminate them. There is a small timeout after which the command is considered received. However, if you want to send multiple subsequent commands, to avoid crosstalking, you should terminate your commands with a new line. This forces the command to be interpreted right away, without waiting for the timeout.
* Float point numbers are hell in embedded programing and also pose risks of precision loss. To avoid these, here only integers are used. Thus, When you want to set 12.34 Hz and 0.45 duty cycle, you will have to pass 12340:450 to the wave generator. It divides everything to **NORMALIZATION_FACTOR**
