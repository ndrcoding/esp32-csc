# **Hardware / Embedded Security REPORT**

## **ESP32 Project - Task 0**

## **1. Button LED control using Wokwi**
(simulation link: https://wokwi.com/projects/475163504726918145)

**Aim**

Building a beginner ESP32 project using Wokwi- Controlling LED using a button

**Components**

- ESP32
- LED
- Resistor(220 ohm)
- push button

**Connections**

- LED( anode ) - GPIO 2
- LED( Cathode ) - Resistor end
- Resistor - GND
- push button - GPIO4
              - GND

**What I did**

First, I created a blinking LED using digitalWrite() and delay(), then changed it into a
LED controlled using a button by using digitalRead() in a if-else loop .

**What I Learned**

- ESP32 basics 
- INPUT_PULLUP--internal resistance
- digitalRead() and digitalWrite()
- delay()



## **Embedded Security Research – Task 1A**

## **1. Debug Interfaces and Physical Access – UART**

**Aim**

To understand UART communication and how a UART debug interface can expose information from an embedded system.

**Components Used**

- ESP32
- Wokwi Serial Monitor

No external hardware was required.

**What I did**

I started UART using:

Serial.begin(115200);

Then I used "Serial.println()" to send messages through UART.

First, I tested a simple debug message:

Debug message: ESP32 is running

Then I used a counter to show changing information:

Debug: Count = 1
Debug: Count = 2
Debug: Count = 3

**Security Demonstration**

To demonstrate the security problem, I intentionally sent a dummy password through the UART debug output:

System running
Debug: Device ID = ESP32_001
Debug: Password = 1234

This showed that if sensitive information is printed through an accessible debug interface, someone with access to that interface could read the information.

**Vulnerability Identified**

Sensitive information may be exposed through accessible UART debug interface.

**Mitigation**

- Do not print passwords or secret keys through UART.
- Restrict physical access to UART pins.
- Disable unused debug interfaces when appropriate.

**What I Learned**

From this task, I learned:

- How UART is used for communication.
- How UART can be used for debugging.
- Why debug information can be useful.
- How sensitive information can accidentally be exposed through a debug interface.
- Why debug interfaces should be protected in a real embedded system.
  

## **2. Peripheral Bus Security and Physical Sniffing (I²C)**
   
 (simulation link: https://wokwi.com/projects/475372978010816513)

**Aim**

To understand I²C communication and see how data travelling through the I²C bus can be observed.

**Components**

- ESP32
- SSD1306 OLED
- MPU6050
- Wokwi Logic Analyzer

**Connections**

For both OLED and MPU6050:

- SDA - GPIO 21
- SCL - GPIO 22
- VCC - 3V3
- GND - GND

**What I Did**

First, I created an I²C scanner using the ESP32.

The scanner found:

- OLED - "0x3C"
- MPU6050 - "0x68"

Then I read data from the MPU6050 using its registers.
I also read the temperature register. After converting the raw value correctly, the temperature shown was about 24°C.

After that, I connected the Wokwi Logic Analyzer:

- D0 - GPIO 21 (SDA)
- D1 - GPIO 22 (SCL)

I started the simulation and observed the I²C signals. The Logic Analyzer showed the clock pulses on SCL and changing data on SDA.

**Vulnerability Identified**

I²C does not provide encryption by itself.

Therefore, if someone gets physical access to an exposed I²C bus, they may be able to observe the communication.

This experiment demonstrated the idea using the Wokwi Logic Analyzer.

**Mitigation**

- Restrict physical access to the hardware.
- Avoid exposing unnecessary I²C connections.
- Use additional security or encryption when sensitive data is transferred.

**What I Learned**

- Basic I²C communication.
- I²C device addresses.
- How to scan for I²C devices.
- How to read sensor data.
- How SDA and SCL signals look.
- How a Logic Analyzer can be used to observe communication.


## **3. Firmware Concurrency and Shared State**

   (simulation link: https://wokwi.com/projects/475794044534080513)

**Aim**

To understand interrupts and how the main program and an interrupt can access the same variable.

**Components**

- ESP32
- Push button
- Serial Monitor

**Connections**

- Push button - GPIO 4
- Other side of button - GND

The ESP32's internal pull-up was used, so no external resistor was needed.

**What I Did**

I created a variable called "count".

The push button generates an interrupt when it is pressed.
The interrupt increases the value of "count".
The main program also reads the same variable.
So, both the interrupt and the main program are accessing the same shared variable.

*I used:*

volatile int count = 0;
because "count" is changed inside the interrupt.

**Protection**

I used:

noInterrupts();

int safeCount = count;

interrupts();

This temporarily stops interrupts while the main program copies the shared value.
The value was then displayed using the Serial Monitor.

**Vulnerability Identified**

When an interrupt and the main program access the same shared data, improper handling can cause inconsistent results.
Therefore, shared data should be handled carefully.

**Mitigation**

- Use "volatile" for variables shared with an interrupt.
- Protect critical access using appropriate synchronization or critical sections.

**What I Learned**

- What an interrupt is.
- How to use a push-button interrupt.
- What shared data means.
- Why "volatile" is used.
- How to protect shared data using a critical section.


## **Conclusion**

Through these three tasks, I learned three basic embedded security concepts:

UART : How an exposed debug interface can reveal sensitive information.

I²C: Communication on a physical bus can be observed if the bus is accessible.

Firmware concurrency: Shared data between the main program and interrupts needs proper handling.

I also gained practical experience with ESP32, Wokwi, I²C, Logic Analyzer, GPIO interrupts, and shared variables.


