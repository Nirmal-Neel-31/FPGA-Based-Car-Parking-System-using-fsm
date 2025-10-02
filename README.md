This repository contains a Verilog implementation of a controller for a simple parking system. The system uses a Moore Finite State Machine (FSM) to manage the flow, including password-protected entry and visual indicators for different states.

⚙️ Module Functionality
The parking_system module controls the logic for a basic parking garage entrance. Key functionalities include:

Entry Trigger: A car approaching the entrance triggers the system via sensor_entrance.

Password Verification: The system enters a waiting period for a two-part password (password_1, password_2) to be entered.

Access Control: Access is granted only upon entering the correct password (hardcoded as password_1 = 2 
′
 b01 and password_2 = 2 
′
 b10).

Exit Detection: An exit sensor (sensor_exit) is used to detect a car leaving, returning the system to an idle state.

Visual Feedback: Two LEDs (Green and Red) and two 7-segment displays (HEX_1, HEX_2) provide status information.

🚦 Finite State Machine (FSM)
The system is implemented as a Moore FSM with the following states:

State	Name	Binary	Description	LED Output	HEX Output (HEX_1, HEX_2)
1	IDLE	3 
′
 b000	Waiting for a car at the entrance sensor.	OFF, OFF	OFF, OFF
2	WAIT_PASSWORD	3 
′
 b001	Car detected; waiting for password input.	RED ON	E, n
3	WRONG_PASS	3 
′
 b010	Incorrect password entered.	RED Blinking	E, E
4	RIGHT_PASS	3 
′
 b011	Correct password entered; access granted.	GREEN Blinking	6, 0
5	STOP	3 
′
 b100	Car stopped inside (both entrance/exit sensors high). Requires re-entry of correct password to clear.	RED Blinking	5, P

Export to Sheets
State Transitions
Current State	Condition	Next State
IDLE	sensor_entrance = 1	WAIT_PASSWORD
WAIT_PASSWORD	counter_wait ≤3 (Timeout period)	WAIT_PASSWORD
WAIT_PASSWORD	Correct Password AND counter_wait >3	RIGHT_PASS
WAIT_PASSWORD	Wrong Password AND counter_wait >3	WRONG_PASS
WRONG_PASS	Correct Password	RIGHT_PASS
WRONG_PASS	Wrong Password	WRONG_PASS
RIGHT_PASS	sensor_exit = 1 (Car is exiting)	IDLE
RIGHT_PASS	sensor_entrance = 1 AND sensor_exit = 1	STOP
STOP	Correct Password	RIGHT_PASS
STOP	Wrong Password	STOP

Export to Sheets
📌 Ports
Port Name	Direction	Size	Description
clk	Input	1-bit	System clock.
reset_n	Input	1-bit	Asynchronous active-low reset.
sensor_entrance	Input	1-bit	High when a car is at the entrance.
sensor_exit	Input	1-bit	High when a car is at the exit detection point.
password_1	Input	2-bit	First part of the password.
password_2	Input	2-bit	Second part of the password.
GREEN_LED	Output	1-bit	Green LED indicator (Active High).
RED_LED	Output	1-bit	Red LED indicator (Active High).
HEX_1	Output	7-bit	Control signals for the first 7-segment display.
HEX_2	Output	7-bit	Control signals for the second 7-segment display.

Export to Sheets
🛠️ Implementation Details
Language: Verilog HDL

FSM Type: Moore FSM

Password: The correct password is fixed in the code: password_1=2 
′
 b01, password_2=2 
′
 b10.

Time Out: A simple counter (counter_wait) is used to enforce a brief window (when counter_wait ≤3 clock cycles) in the WAIT_PASSWORD state before checking the password.

Blinking: LED blinking is implemented by toggling the temporary LED register (red_tmp, green_tmp) on every positive clock edge in the relevant states.
