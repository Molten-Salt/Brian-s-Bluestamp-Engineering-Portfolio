# Dual Axis Solar Tracker

I built a device that would follow the sun. 


A stationary solar panel only points directly at the sun for a moment each day, which means it spends most of its time collecting light at an angle instead of head-on. To close that gap, I built a dual-axis solar tracker: a device that dynamically adjusts the solar panel so its surface stays perpendicular to the sun as it moves across the sky, absorbing more sunlight— and therefore more electricity — out of the same panel.

| Brian C | Gunn High | Electrical Engineering | Incoming Junior |
---
<img width="672" height="508" alt="Brian C" src="https://github.com/user-attachments/assets/d1018f1b-3417-4495-9aef-23aeda31b950" />

---
## First Milestone

In order to get the Servo Motors to rotate the solar panel to the direction of the sun, I needed 3 core parts.
Light dependent resistors (LDRs), Servo Motors, and the algorithm to run it. 
The LDRs receive electricity from the Arduino, with the resistance against this flow decreasing with greater brightness. As this power flows back to ground, a signal wire read this quantity, and takes it to the Arduino's analog pin to be read. These values are then outputted to the IDE's serial monitor, where if-then-else statements in C++ tell the Servo Motors to rotate to the direction of greater light. 
```
int POS = 90;

void setup() {
  Serial.begin(9600);
  panServo.attach(6);
  panServo.write(POS);
  delay(300);
}

void loop() {
  Serial.print(analogRead(A1)); Serial.print(" "); Serial.print(analogRead(A0));
  int Diff = analogRead(A1) - analogRead(A0);
  if (Diff > 50) {
    POS += 3;
  } else if (Diff < -50) {
    POS -= 3;
  }
  panServo.write(POS);
}
```

You can't actually command a Servo Motor to move 3 degrees clockwise, or 15 degrees counterclockwise, because It doesn't remember its current position after you stop powering the Motor! You can only command it to move to a certain degree. Because of these limitations I made it so that the Servo Motor would start at the fixed position of 90 degrees (so that it can go in both directions), and assigned a variable to be always equal to the Servo Position (POS). Everytime the algorithm is repeated, POS changes ±3, thereby rotating the servo Motor. 

 


<table>
  <tr>
    <td width="50%">
    <img width="397.5" height="323" alt="Screenshot 2026-07-30 at 12 01 06 PM" src="https://github.com/user-attachments/assets/53507237-3d78-4e24-a181-2da9e8e1a504" />  
    </td>
    <td width="50%">
      <iframe width="560" height="315" src="https://www.youtube.com/embed/R-oDt9jQxLU?si=cud6JWD-leJm_vVq" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
      <!-- If the platform blocks iframes, use a linked image instead -->
      
    </td>
  </tr>
</table>






---

## Second Milestone




For my second milestone, I designed the solar panel mount in Onshape, a CAD program. 





<table>
  <tr>
    <td width="50%">
   <img width="611.5" height="528.5" alt="Screenshot 2026-07-31 at 9 19 01 AM" src="https://github.com/user-attachments/assets/92462f74-b4ea-47ff-a06a-dabb5dd7edfb" />
    </td>
    <td width="50%">
     <iframe width="560" height="315" src="https://www.youtube.com/embed/JbJ70Yp1YcA?si=BKetJSrrPya8Xgct" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
      <!-- If the platform blocks iframes, use a linked image instead -->
      
    </td>
  </tr>
</table>












By extruding and lofting 2D sketches, I created a hollowed-out frustum to serve as the base, a cylinder with two rectangular beams to be rotated by a servo motor, and a U shape. On top of the base is a hole for the servo motor to snap on. The horn of the Servo is attached to the cylindric base via 2 miniature bolts. A hole is also placed on the rectangular beam, where a servo motor snaps on. The motor tilts the U shape, and therefore, the solar panel up and down. The other beam provides structural support via a bolt and nut. 














## Final Milestone

I installed the photoresistors to complete the solar tracker. Soldering all these parts onto a small perfboard came with plenty of difficulties-- I created a ton of shorts, which required a  lot of desoldering. 






<iframe width="510" height="315" src="https://www.youtube.com/embed/JbJ70Yp1YcA?si=nc6Ti0EgEx7VySFC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>






 Each LDR's resistance drops as more light hits it, so shining light unevenly across the four sensors produces four different voltage readings on the Arduino Analog pins. If the top sensors are reading high volts than the bottom (or the left brighter than the right) by a small deadband, the Arduino nudges the corresponding servo a few degrees in that direction. Running this comparison in a continuous loop lets the mount creep toward the sun in small steps throughout the day, on both the vertical (tilt) and horizontal (pan) axes.  The deadband keeps the servos from jittering back and forth chasing tiny, insignificant differences between sensors. 





## Schematics

<img width="1000" height="772" alt="Screenshot 2026-07-31 at 10 00 24 AM" src="https://github.com/user-attachments/assets/bc5c839b-bffd-41ae-b6d6-fa8da6e5fc81" />

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
