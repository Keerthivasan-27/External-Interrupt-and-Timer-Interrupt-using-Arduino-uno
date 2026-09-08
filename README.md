# External-Interrupt-and-Timer-Interrupt-using-Arduino-uno
EXP 3: EXTERNAL INTERRUPT AND TIMER INTERRUPT USING ARDUINO UNO

# Aim
To implement External Interrupt and Timer Interrupt using an Arduino UNO and observe interrupt-driven execution.

Hardware / Software Tools Required
Arduino UNO Board
USB Cable
PC/Laptop with Arduino IDE Installed
Breadboard
Push Button
LED
220 Ω Resistor
10 kΩ Resistor (Pull-down, optional if not using INPUT_PULLUP)
Jumper Wires
# Circuit Diagram
<img width="827" height="412" alt="image" src="https://github.com/user-attachments/assets/bdd58ea7-b8da-434b-958a-e5d469409ac5" />


# Procedure
Step 1: Assemble the Circuit
Place the Arduino UNO, breadboard, push button, LED, and resistor on the workbench.
Connect the Arduino UNO to the computer using a USB cable.
Step 2: Connect the External Interrupt Circuit
Connect the LED anode to Digital Pin 13 through a 220 Ω resistor.
Connect the LED cathode to GND.
Connect one terminal of the push button to Digital Pin 2 (INT0).
Connect the other terminal of the push button to GND.
Configure Pin 2 as INPUT_PULLUP in the program.
Step 3: Configure the Timer Interrupt
Use Timer1 to generate a periodic interrupt.
Configure the timer in the Arduino program.
Define an Interrupt Service Routine (ISR) for Timer1.
Step 4: Open the Arduino IDE
Open Arduino IDE.
Select Tools → Board → Arduino UNO.
Select the correct COM Port.
Step 5: Write and Upload the Program
Write the program for external and timer interrupts.
Verify the program.
Upload it to the Arduino UNO.
Step 6: Execute the Program
Press the push button and observe the external interrupt response.
Observe the LED blinking periodically due to the timer interrupt.
Open the Serial Monitor to observe interrupt messages (if included).
Step 7: Verify the Output
Confirm that pressing the push button immediately triggers the external interrupt.
Confirm that the timer interrupt executes periodically without polling.
Record the observations.
# Program
```
#include <FspTimer.h>

FspTimer timer;

volatile bool timerFlag = false;
volatile bool externalFlag = false;

// -----------------------------
// External Interrupt ISR
// -----------------------------
void externalISR() {
  externalFlag = true;
}

// -----------------------------
// Timer Interrupt ISR
// -----------------------------
void timerISR(timer_callback_args_t *arg) {
  timerFlag = true;
}

void setup() {

  // Built-in LED
  pinMode(LED_BUILTIN, OUTPUT);
  digitalWrite(LED_BUILTIN, LOW);

  // -----------------------------
  // External Interrupt - D2
  // -----------------------------
  pinMode(2, INPUT_PULLUP);

  attachInterrupt(
    digitalPinToInterrupt(2),
    externalISR,
    FALLING
  );

  // -----------------------------
  // Timer Interrupt
  // -----------------------------
  uint8_t timerType = GPT_TIMER;

  int8_t channel =
    FspTimer::get_available_timer(timerType);

  if (channel < 0) {
    while (1);
  }

  // Timer frequency = 1 Hz
  bool success = timer.begin(
    TIMER_MODE_PERIODIC,
    timerType,
    channel,
    1.0,
    0.0,
    timerISR
  );

  if (!success) {
    while (1);
  }

  timer.setup_overflow_irq();
  timer.open();
  timer.start();
}

void loop() {

  // -----------------------------
  // TIMER INTERRUPT RESPONSE
  // -----------------------------
  if (timerFlag) {

    timerFlag = false;

    // Toggle LED every 1 second
    digitalWrite(
      LED_BUILTIN,
      !digitalRead(LED_BUILTIN)
    );
  }

  // -----------------------------
  // EXTERNAL INTERRUPT RESPONSE
  // -----------------------------
  if (externalFlag) {

    externalFlag = false;

    // Toggle LED immediately
    digitalWrite(
      LED_BUILTIN,
      !digitalRead(LED_BUILTIN)
    );
  }
}
```
<img width="720" height="1600" alt="image" src="https://github.com/user-attachments/assets/8ffe9015-dd9a-430d-ab15-dc5d874135fb" />


# Observation
Activity	Expected Output
Board Powered ON	System initializes
Push Button Pressed	External ISR executes immediately
Timer Running	Timer ISR executes periodically
LED	Toggles/blinks according to ISR
# Result
The External Interrupt and Timer Interrupt were successfully implemented using the Arduino UNO. The external interrupt responded immediately to the push button event, while the timer interrupt executed periodically, demonstrating efficient interrupt-driven programming without continuous polling.

