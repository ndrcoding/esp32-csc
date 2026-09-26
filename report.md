Embedded Systems Security – Task Report

1. Peripheral Bus Security and Physical Sniffing (I²C)
   
 (simulation link: https://wokwi.com/projects/475372978010816513)

Aim

To understand I²C communication and see how data travelling through the I²C bus can be observed.

Components

- ESP32
- SSD1306 OLED
- MPU6050
- Wokwi Logic Analyzer

Connections

For both OLED and MPU6050:

- SDA → GPIO 21
- SCL → GPIO 22
- VCC → 3.3V
- GND → GND

What I Did

First, I created an I²C scanner using the ESP32.

The scanner found:

- OLED → "0x3C"
- MPU6050 → "0x68"

Then I read data from the MPU6050 using its registers.

I also read the temperature register. After converting the raw value correctly, the temperature shown was about 24°C.

After that, I connected the Wokwi Logic Analyzer:

- D0 → GPIO 21 (SDA)
- D1 → GPIO 22 (SCL)

I started the simulation and observed the I²C signals. The Logic Analyzer showed the clock pulses on SCL and changing data on SDA.

Vulnerability Identified

I²C does not provide encryption by itself.

Therefore, if someone gets physical access to an exposed I²C bus, they may be able to observe the communication.

This experiment demonstrated the idea using the Wokwi Logic Analyzer.

Mitigation

- Restrict physical access to the hardware.
- Avoid exposing unnecessary I²C connections.
- Use additional security or encryption when sensitive data is transferred.

What I Learned

I learned:

- Basic I²C communication.
- I²C device addresses.
- How to scan for I²C devices.
- How to read sensor data.
- How SDA and SCL signals look.
- How a Logic Analyzer can be used to observe communication.


2. Firmware Concurrency and Shared State

   (simulation link: https://wokwi.com/projects/475794044534080513)

Aim

To understand interrupts and how the main program and an interrupt can access the same variable.

Components

- ESP32
- Push button
- Serial Monitor

Connections

- Push button → GPIO 4
- Other side of button → GND

The ESP32's internal pull-up was used, so no external resistor was needed.

What I Did

I created a variable called "count".

The push button generates an interrupt when it is pressed.

The interrupt increases the value of "count".

The main program also reads the same variable.

So, both the interrupt and the main program are accessing the same shared variable.

I used:

volatile int count = 0;

because "count" is changed inside the interrupt.

Protection

I used:

noInterrupts();

int safeCount = count;

interrupts();

This temporarily stops interrupts while the main program copies the shared value.

The value was then displayed using the Serial Monitor.

Vulnerability Identified

When an interrupt and the main program access the same shared data, improper handling can cause inconsistent results.

Therefore, shared data should be handled carefully.

Mitigation

- Use "volatile" for variables shared with an interrupt.
- Protect critical access using appropriate synchronization or critical sections.

What I Learned

I learned:

- What an interrupt is.
- How to use a push-button interrupt.
- What shared data means.
- Why "volatile" is used.
- How to protect shared data using a critical section.


Conclusion

Through these two tasks, I learned two basic embedded security concepts:

I²C: Communication on a physical bus can be observed if the bus is accessible.

Firmware concurrency: Shared data between the main program and interrupts needs proper handling.

I also gained practical experience with ESP32, Wokwi, I²C, Logic Analyzer, GPIO interrupts, and shared variables.


