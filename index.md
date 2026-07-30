# Dual Axis Solar Tracker

A stationary solar panel only points directly at the sun for a moment each day, which means it spends most of its time collecting light at an angle instead of head-on. To close that gap, I built a dual-axis solar tracker: a mount that dynamically adjusts the panel so its surface stays perpendicular to the sun as it moves across the sky, absorbing more sunlight— and therefore more electricity — out of the same panel. I also added a liquid cooling loop, since panel efficiency drops as temperature rises. 

| Brian C | Gunn High | Electrical Engineering | Incoming Junior |




---










<img width="1080" height="1350" alt="Brian C" src="https://github.com/user-attachments/assets/d1018f1b-3417-4495-9aef-23aeda31b950" />


---





## Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/JbJ70Yp1YcA?si=nc6Ti0EgEx7VySFC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

I installed the photoresistors to complete the solar tracker. Soldering all these parts onto a small perfboard came with plenty of difficulties-- I created a ton of shorts, which required a  lot of desoldering. 

Four light-dependent resistors (LDRs) are placed on the corners of the panel. Each LDR's resistance drops as more light hits it, so shining light unevenly across the four sensors produces four different voltage readings on the Arduino Analog pins. If the top sensors are reading brighter than the bottom (or the left brighter than the right) by a small deadband, the Arduino nudges the corresponding servo a few degrees in that direction. Running this comparison in a continuous loop lets the mount creep toward the sun in small steps throughout the day, on both the vertical (tilt) and horizontal (pan) axes.  The deadband keeps the servos from jittering back and forth chasing tiny, insignificant differences between sensors. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/JbJ70Yp1YcA?si=nc6Ti0EgEx7VySFC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

## Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/w8I2iujtMhk?si=RQaVOStHIRxvRER_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/DKBY4hZPokM?si=iDBtwOJHNVWD8kCy" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my second milestone, I designed the solar panel mount in Onshape, a CAD program.

By extruding and lofting 2D sketches, I created a hollowed-out frustum for the base, with a rectangular-prism cutout that holds a servo motor. The servo's rotating shaft connects to a base that supports the panel. On one end of a beam sits a second motor that tilts the panel up and down; the other end is loosely connected with a bolt and nut.

---

## First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/R-oDt9jQxLU?si=cud6JWD-leJm_vVq" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<img width="560" height="450" alt="Screenshot 2026-07-13 at 8 48 04 AM" src="https://github.com/user-attachments/assets/06054deb-a1b5-4681-8e16-ffc33836b6a6" />

For my first milestone, I wrote code in Tinkercad, which converts block-based logic into C++ — the language the Arduino IDE reads and compiles down to binary for the board to run.

The Arduino's 5V pin powers the breadboard's positive rail. Along this rail sits the servo motor's power pin, supplying it with energy. A male-to-male jumper wire connects the Arduino's ground pin to the breadboard's negative rail, completing the return path for current. Further up the positive rail are the legs of two photoresistors; their other legs connect to a shared point on the breadboard where, in the same column, a 10kΩ resistor ties back to the negative rail, pulling that node to 0V. Above each resistor, a jumper wire runs to an analog input pin for reading. In the code, pins A0 and A1 are read and used to drive the motor's rotation.

<img width="560" height="375" alt="IMG_0272" src="https://github.com/user-attachments/assets/883433e1-7c0b-463b-aca5-90b6efa8dd01" />

---

## Schematics

<img width="1146" height="886" alt="Schematic" src="https://github.com/user-attachments/assets/2cc0e38e-0b6e-4ae6-b4c0-412491e0dd7a" />

---

## Code

```cpp
#include <Servo.h>

// --- SERVO DEFINITIONS ---
Servo panServo;
Servo tiltServo;

// --- PIN DEFINITIONS ---
const int LDR_TOP_LEFT     = A1;
const int LDR_BOTTOM_LEFT  = A0;
const int LDR_TOP_RIGHT    = A5;
const int LDR_BOTTOM_RIGHT = A4;

// --- SAFE BOUNDARIES ---
const int TILT_MIN = 10;   // Lowest tilt boundary
const int TILT_MAX = 170;  // Highest tilt boundary
const int PAN_MIN  = 0;    // Left pan limit
const int PAN_MAX  = 177;  // Right pan limit

// --- TUNING & SPEED SETTINGS ---
int tiltAngle = 90;   // Start centered
int panAngle  = 90;   // Start centered

const int STEP_SIZE  = 3;   // Movement step size (degrees)
const int STEP_DELAY = 30;  // Loop delay (ms)
const int DEADBAND   = 20;  // Sensitivity threshold

// --- CALIBRATION MULTIPLICATIVE SCALARS ---
// Adjust these fine-tuning multipliers (e.g., 0.95 to 1.05) to balance sensor readings
const float SCALE_TL = 1.00;
const float SCALE_BL = 1.2;
const float SCALE_TR = 1.00;
const float SCALE_BR = 2.70;

void setup() {
  Serial.begin(9600);

  // Staggered startup sequence
  panServo.write(panAngle);
  panServo.attach(6);
  delay(300);

  tiltServo.write(tiltAngle);
  tiltServo.attach(5);
  delay(300);
}

void loop() {
  // 1. Read raw analog values from LDR pins
  int rawTL = analogRead(LDR_TOP_LEFT);
  int rawBL = analogRead(LDR_BOTTOM_LEFT);
  int rawTR = analogRead(LDR_TOP_RIGHT);
  int rawBR = analogRead(LDR_BOTTOM_RIGHT);

  // 2. Apply multiplicative scaling calibration
  int topLeft     = rawTL * SCALE_TL;
  int bottomLeft  = rawBL * SCALE_BL;
  int topRight    = rawTR * SCALE_TR;
  int bottomRight = rawBR * SCALE_BR;

  // 3. Average sensor pairs for dual-axis tracking
  int avgTop    = (topLeft + topRight) / 2;
  int avgBottom = (bottomLeft + bottomRight) / 2;
  int avgLeft   = (topLeft + bottomLeft) / 2;
  int avgRight  = (topRight + bottomRight) / 2;

  // 4. Print adjusted/scaled values to Serial Monitor
  Serial.print("ADJ -> TL:"); Serial.print(topLeft);
  Serial.print(" BL:");       Serial.print(bottomLeft);
  Serial.print(" TR:");       Serial.print(topRight);
  Serial.print(" BR:");       Serial.print(bottomRight);
  Serial.print(" | Tilt:");   Serial.print(tiltAngle);
  Serial.print(" Pan:");      Serial.println(panAngle);

  // --- VERTICAL AXIS (TILT) ---
  int vertDiff = avgTop - avgBottom;
  if (vertDiff > DEADBAND) {
    tiltAngle += STEP_SIZE;
  } else if (vertDiff < -DEADBAND) {
    tiltAngle -= STEP_SIZE;
  }

  // --- HORIZONTAL AXIS (PAN) ---
  int horizDiff = avgLeft - avgRight;
  if (horizDiff > DEADBAND) {
    panAngle -= STEP_SIZE;  // Swap sign if pan turns away from light
  } else if (horizDiff < -DEADBAND) {
    panAngle += STEP_SIZE;
  }

  // Enforce mechanical limits
  tiltAngle = constrain(tiltAngle, TILT_MIN, TILT_MAX);
  panAngle  = constrain(panAngle, PAN_MIN, PAN_MAX);

  // Output position updates to servos
  tiltServo.write(tiltAngle);
  panServo.write(panAngle);

  delay(STEP_DELAY);
}
```

---

## Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--|:--|:--:|:--:|
| Solar Panel | Generates electricity via the photoelectric effect | $20.95 | <a href="https://www.adafruit.com/product/5366">Link</a> |
| Arduino | A microcontroller that runs code uploaded from your computer | $29.95 | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/">Link</a> |
| 2× Servo Motors | Rotates the panel mount's base and tilts the panel toward the sun | $11.95 | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/">Link</a> |
| 4× Light Dependent Resistors | Outputs a voltage determined by the amount of light absorbed | $0.95 | <a href="https://www.adafruit.com/product/161/">Link</a> |
| 10kΩ Resistors | Limits current to protect the electronics | $0.75 | <a href="https://www.adafruit.com/product/2784">Link</a> |
| N-Channel Power MOSFET (30V / 60A) | Lets the Arduino switch the pump and fan | $2.25 | <a href="https://www.adafruit.com/product/355">Link</a> |
| Aluminum Water Cooling Block | Absorbs heat from the panel; its microchannels let water pick up that heat | $6.29 | <a href="https://botland.store/aluminium-heat-sinks/22902-water-block-for-cooling-40x40mm-aluminum-heatsink-for-peltier-cells.html">Link</a> |
| Liquid Pump | Circulates water through the cooling block and radiator loop | $24.95 | <a href="https://www.adafruit.com/product/3910/">Link</a> |
| Tubing | Carries water through the pump → block → radiator → pump cycle | $8.00 | <a href="https://www.amazon.com/Hooshing-Silicone-Flexible-Winemaking-Transfer/dp/B0BR7SMSHG/">Link</a> |
| Temperature Sensor | Measures and reports temperature | $9.95 | <a href="https://www.adafruit.com/product/381/">Link</a> |
| 1N4001 Diode (10-pack) | Protects the MOSFETs from voltage spikes when the pump/fan switch off | $1.50 | <a href="https://www.adafruit.com/product/755/">Link</a> |
| Heat Sink Thermal Tape | Improves heat transfer | $4.00 | <a href="https://www.adafruit.com/product/1468/">Link</a> |
| Radiator | Cools the circulating water | $58.32 | <a href="https://www.amazon.com/dp/B082585F2J/">Link</a> |
| Radiator Fan | Assists the radiator's cooling | $34.95 | <a href="https://www.amazon.com/dp/B07DXQTCK6/">Link</a> |
| G1/4 Thread, OD 4mm Nozzle | Screws into the radiator's threaded ports so tubing can attach | $14.39 | <a href="https://www.amazon.com/uxcell-Fitting-Thread-Nozzle-Cooling/dp/B091YQZC3Y/">Link</a> |
| 8mm-to-4mm Adapter | Bridges the water block's 8mm port to the 4mm tubing | $6.11 | <a href="https://www.amazon.com/dp/B07ZCQ5D8F/">Link</a> |
