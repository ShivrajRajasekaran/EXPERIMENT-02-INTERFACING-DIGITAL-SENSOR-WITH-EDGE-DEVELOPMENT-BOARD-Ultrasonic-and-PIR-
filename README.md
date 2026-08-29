<img width="1919" height="1079" alt="Screenshot 2026-08-01 153652" src="https://github.com/user-attachments/assets/8089111e-d25a-4af0-8e75-8f7f03bd239e" /># EXPERIMENT-02-INTERFACTING-DIGITAL-SENSOR-WITH-EDGE-DEVELOPMENT-BOARD-ULTRASONIC-AND-PIR-SENSOR-(RASPBERRYPI-PI4)
### NAME : SHIVRAJ R
### DEPARTMENT : CSE(IoT)
### ROLL NO : 212223110051 
### DATE OF EXPERIMENT : 03.08.2026 

### AIM
To interface a digital sensor (Ultrasonic and PIR) with the Raspberry Pi 4 and control it using Python.

## APPARATUS REQUIRED
Raspberry Pi 4
LED (Light Emitting Diode)
330Ω Resistor
IR Sensor
Breadboard
Jumper Wires
USB Cable
 ## THEORY

![Raspberry Pi Pin](https://github.com/user-attachments/assets/19e5a1e7-cb46-4909-ba59-e4f4560cae03)





 
 
 
 ### FIGURE-01 RASPI PI 4 PINOUT DIAGRAM: 


The Raspberry Pi 4 Model B is built around a Broadcom BCM2711 system-on-chip that integrates a quad-core ARM Cortex-A72 (64-bit) CPU, VideoCore VI GPU, memory controller, and peripheral interfaces, forming a compact yet complete computer architecture where the SoC connects internally to RAM, USB 3.0 controller, Gigabit Ethernet, HDMI display, and wireless modules. Its 40-pin GPIO header provides a flexible pin configuration consisting of power pins (5 V and 3.3 V), multiple ground pins, and general-purpose input/output pins that operate at 3.3 V logic and can be programmed for digital I/O or alternate functions. Key alternate functions include I²C (SDA, SCL) for sensor communication, SPI (MOSI, MISO, SCLK, CS) for high-speed peripheral interfacing, UART (TX, RX) for serial communication, and PWM for control applications.  For communication, I2C (SDA, SCL), SPI (MOSI, MISO, SCK), and UART (TX, RX) interfaces are mapped across different GPIO pins, allowing seamless connectivity with sensors and peripherals. All GPIO pins support PWM (Pulse Width Modulation), making it useful for motor control, LED brightness adjustment, and sound applications. The BOOTSEL button enables USB mass storage mode for firmware flashing, while the DEBUG pins (SWD interface) provide debugging capabilities. With its low power consumption, flexible GPIO options, and rich interface support, the Raspberry Pi Pico is widely used for IoT, embedded systems, robotics, and automation projects.This architecture and pin multiplexing allow the Raspberry Pi 4 to act as both a general-purpose computing platform and an embedded controller, supporting rapid prototyping, hardware interfacing, and IoT applications.
## Ultrasonic Sensor:
An ultrasonic sensor is a distance-measuring device that uses high-frequency sound waves to detect objects and calculate how far away they are. It works by emitting ultrasonic pulses (typically around 40 kHz) and measuring the time taken for the echo to bounce back after hitting an object. Using the speed of sound in air, the sensor converts this time into distance. Ultrasonic sensors are widely used in robotics, obstacle detection, parking systems, and automation because they provide reliable, contactless measurement regardless of lighting conditions.
<img width="600" height="411" alt="image" src="https://github.com/user-attachments/assets/d879d982-347a-45ff-92c1-b78b57e9fdef" />
 ### FIGURE-02 Ultrasonic Sensor 

## PIR Sensor:
A Passive Infrared (PIR) sensor is a motion detection device that senses changes in infrared radiation emitted by warm objects such as humans or animals. Instead of emitting signals, it passively detects heat variations within its field of view. When a warm body moves across the sensor’s detection zones, it triggers an electrical signal indicating motion. PIR sensors are commonly used in security alarms, automatic lighting systems, and energy-saving smart devices due to their low power consumption and ability to detect human presence effectively.
<img width="428" height="494" alt="image" src="https://github.com/user-attachments/assets/bb6b0f22-33d7-4d63-b5c6-05e6d655e71d" />

### FIGURE-03 PIR Sensor 
## Working Principle:
Experiment 2A
The Ultrasonic sensor Trig pin is connected to one of the GPIO pins of the Raspberry Pi 4.
The Ultrasonic sensor Echo pin is connected to one of the GPIO pins of the Raspberry Pi 4.
The Python script sets the take the distance taken echo output and shown in Thingspeak cloud with current status and Console.
CIRCUIT DIAGRAM
Connect the Vcc of the Ultrasonic sensor is connected to +5V in Raspberrry Pi4.
Connect the Gnd of the Ultrasonic sensor is connected to Gnd in Raspberrry Pi4.
Connect the Trig pin to any one GPIO.
Connect the Echo pin to any one GPIO.


Experiment 2B
The IR sensor is connected one of the GPIO pins in Raspberry Pi 4.
The Python script sets the PIR sensor value based on the motion detected and shown in Thingspeak and console.
CIRCUIT DIAGRAM
Connect the PIR sensor Vcc to any +5V.
Connect the PIR sensor GND to any GND.
Connect the PIR sensor OUT to any one GPIO. 

# Experiment 2A
## PROGRAM (Python)
```
import RPi.GPIO as GPIO 
import time 
import requests
# ThingSpeak settings 
API_KEY = "71U2JWP1PFKWYPF8" 
THINGSPEAK_URL = "https://api.thingspeak.com/update" 
# GPIO pins 
TRIG = 23 
ECHO = 24 
GPIO.setmode(GPIO.BCM) 
GPIO.setup(TRIG, GPIO.OUT) 
GPIO.setup(ECHO, GPIO.IN) 
def get_distance(): 
 GPIO.output(TRIG, False) 
 time.sleep(0.5) 
 # Trigger pulse 
 GPIO.output(TRIG, True) 
 time.sleep(0.00001) 
 GPIO.output(TRIG, False) 
 while GPIO.input(ECHO) == 0: 
  pulse_start = time.time() 
 while GPIO.input(ECHO) == 1: 
  pulse_end = time.time() 
 pulse_duration = pulse_end - pulse_start 
 distance = pulse_duration * 17150 
 distance = round(distance, 2) 
 return distance 
try: 
 while True: 
  distance = get_distance() 
  # Console output 
  print("distance =", distance, "cm") 
  # Text message for ThingSpeak 
  status_text = f"distance = {distance} cm" 
  # Send data to ThingSpeak 
  payload = { 
  "api_key": API_KEY, 
  "field1": distance, # numeric for chart 
  "status": status_text # text message 
  } 
  response = requests.get(THINGSPEAK_URL, params=payload) 
  print("Sent to ThingSpeak")
 time.sleep(15) 
except KeyboardInterrupt: 
 GPIO.cleanup()
 
```

### OUPUT  

# FIGURE -04 Circuit : 

![alt text](exp2Connec1.jpeg)

#  FIGURE -05 Console

![alt text](exp2Ultra.jpeg)

# FIGURE -06 Cloud

![alt text](<Screenshot 2026-07-25 113711.png>)

# Experiment 2B
## PROGRAM (Python)
```
import RPi.GPIO as GPIO
import time
import requests

WRITE_API_KEY = "71U2JWP1PFKWYPF8"
URL = "https://api.thingspeak.com/update"

PIR_PIN = 18

GPIO.setmode(GPIO.BCM)
GPIO.setup(PIR_PIN, GPIO.IN)

print("PIR Monitoring Started...")
time.sleep(2)

last_state = -1 # store previous state

def update_thingspeak(state):
 data = {
 "api_key": WRITE_API_KEY,
 "field1": state
 }
 try:
  requests.get(URL, params=data)
  print("Uploaded to ThingSpeak:", state)
 except:
  print("Upload Failed")
while True:
 motion = GPIO.input(PIR_PIN)

 if motion != last_state: # send only if changed
  if motion == 1:
   print("Motion Detected")
   update_thingspeak(1)
  else:
   print("No Motion")
   update_thingspeak(0)

  last_state = motion
  time.sleep(15) # ThingSpeak delay

 time.sleep(1)

```

### OUPUT  

# FIGURE -07 CIRCUIT 

<img width="1536" height="1536" alt="exp2Connec2" src="https://github.com/user-attachments/assets/5b419aea-da88-4fa6-9f0e-0cec491f031c" />


#  FIGURE -08 CONSOLE
## ULTRASONIC
<img width="1919" height="1079" alt="Screenshot 2026-08-01 162424" src="https://github.com/user-attachments/assets/113d1d39-7800-4e4b-a44c-2d9ea4b45205" />

## PIR
<img width="1919" height="1079" alt="Screenshot 2026-08-01 153652" src="https://github.com/user-attachments/assets/8ab654e9-e86a-4089-b30a-391314ea20b1" />

# FIGURE -09 CLOUD 
## ULTRASONIC
<img width="1917" height="1019" alt="Screenshot 2026-08-01 162406" src="https://github.com/user-attachments/assets/83ec1cff-8052-4e11-b2c8-dde5eece6790" />

## PIR

<img width="1919" height="984" alt="Screenshot 2026-08-01 153614" src="https://github.com/user-attachments/assets/f6ff0332-9d78-4f15-b5e6-2b1be0d609e9" />


<img width="1919" height="1028" alt="Screenshot 2026-08-01 153606" src="https://github.com/user-attachments/assets/a8f133be-60b3-4e65-9c17-3dae1c3ba89c" />


## RESULTS
The Ultrasonic sensor and PIR sensor is connected to the Raspberry Pi 4 successfully and the distance and the motion detection is visualised in thingspeak confirming the proper interfacing of a digital output.
