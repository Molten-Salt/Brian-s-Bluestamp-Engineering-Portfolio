# Dual Axis Solar Tracker with Liquid Cooling Loop
In order to maximize electrcity generation, I created a device that would follow the sun. By comparing the difference of voltage from 2 photoresistors on opposite sides, the ardunio would order a servo motor to rotate, dyamically moveing the solar panel alongside a dual axis so it would be perpendicular to the sun. Because solar panels lose efficacy as temperature increases above 25 C, I decided to create a negative feedback cooling system. If the temperature increases above a certain amount above a set threshold,a pump will pump cooled water into a aluinimum cooling block, which has billions of microscopic microchannels to maximize surface area (maximizes collisions) so that heat transfers to the water, which flows to a radiator, where a fan would assist in its cooling. The water then flows back to the pump tank, where it shall be reused in a cycle. 


| Brian C | Gunn High | Electrical Engineering | Incoming Junior |

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

<img width="1565" height="1820" alt="image" src="https://github.com/user-attachments/assets/36a56b77-d04a-482f-ad11-3cdfdcc688dd" />

# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/JbJ70Yp1YcA?si=nc6Ti0EgEx7VySFC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


https://github.com/user-attachments/assets/8259d1ae-22a5-4189-adcb-918a07aa95fe





  FOr my second milestone, I designed the mount of the solar panel through a CAD program called Onshape.

  By extruding and lofting 2d sketches, I managed to create a hollowed out frustum as the base, which has a rectangular prism hole to fit a Servo Motor. Connecting to the rotating tip of the Servo motor is the base which holds up the panel. On one end of a beam, there will be a motor to tilt the panel up and down, with the other end being connected loosely by a bolt and nut. 

# First Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/R-oDt9jQxLU?si=cud6JWD-leJm_vVq" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<img width="560" height="450" alt="Screenshot 2026-07-13 at 8 48 04 AM" src="https://github.com/user-attachments/assets/06054deb-a1b5-4681-8e16-ffc33836b6a6" />  

For my First Milestone, I have written my code through Tinkercad, which converts the blocks into C++, which is the programing language the Arduino IDE reads, which converts to binary code to be run. The Arduino's 5 volt socket powers the positive row of tbe breadboard. Down this path, is the power pin for the Servo Motor, supplying the energy needed.The male-to-male golden brown pin connects the Aduino's ground socket to the negative row of the breadboard, allowing electrcity to flow back into the Arduino. Further up the positive row, there is the leg of 2 photoresistors. The other end connects to the general area of the breadboard, where, in the same columb, a 10k resistor goes back to the negative row of breadboard, bringing it to 0 volts. above the 10k resistor, is a jumper wire that connects to the analog pins to be read. IN the code, A0 and A1 are read, which leads to the rotation of the motor. 





<img width="560" height="375" alt="IMG_0272" src="https://github.com/user-attachments/assets/883433e1-7c0b-463b-aca5-90b6efa8dd01" />

  
  
# Schematics 

<img width="1146" height="886" alt="Screenshot 2026-07-17 at 11 54 58 AM" src="https://github.com/user-attachments/assets/2cc0e38e-0b6e-4ae6-b4c0-412491e0dd7a" />



# Code

https://www.markdownguide.org/extended-syntax/
```
#include <Servo.h>

int POS = 90;

int i = 90;

Servo servo_6;

Servo servo_0;

Servo servo_5;

void setup()
{
  servo_6.attach(6, 500, 2500);
  servo_0.attach(0, 500, 2500);
  Serial.begin(9600);
  pinMode(A0, INPUT);
  pinMode(A1, INPUT);
  pinMode(A4, INPUT);
  pinMode(A5, INPUT);
  servo_5.attach(5, 500, 2500);
  pinMode(A2, INPUT);
  pinMode(2, OUTPUT);

  servo_6.write(90);
  servo_0.write(90);
  POS = 90;
}

void loop()
{
  Serial.print("A0: ");
  Serial.println(analogRead(A0));
  Serial.print("A1: ");
  Serial.println(analogRead(A1));
  Serial.print("A4: ");
  Serial.println(analogRead(A4));
  Serial.print("A5: ");
  Serial.println(analogRead(A5));
  if (analogRead(A1) > analogRead(A0)) {
    POS = (POS + 3);
    servo_6.write(POS);
  } else {
    if (analogRead(A1) < analogRead(A0)) {
      POS = (POS - 3);
      servo_6.write(POS);
    }
  }
  if (analogRead(A4) > analogRead(A5)) {
    POS = (POS + 3);
    servo_5.write(POS);
  } else {
    if (analogRead(A4) < analogRead(A5)) {
      POS = (POS - 3);
      servo_5.write(POS);
    }
  }

  Serial.print("Temp:");
  Serial.println((-40 + 0.488155 * (analogRead(A2) - 20)));
  if ((-40 + 0.488155 * (analogRead(A2) - 20)) > 40) {
    digitalWrite(2, HIGH);
  } else {
    digitalWrite(2, LOW);
  }
  delay(10); // Delay a little bit to improve simulation performance
}
```


# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
|Solar Panel|generates electrcity via photoeletric effect.|$20.95|<a href="https://www.adafruit.com/product/5366"> Link </a>|
|Arduino|A microcontroller that runs code you uploaded from your computer|$29.95|<a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a>|
|2 Servo Motors |Rotates the base of the solar panel mount, and tilts the solar panel to the direction of the sun |$11.95|<a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a>|
|4 Light dependent resistors| The voltage it outputs is determined by the level of light absorbtion| $0.95 |<a href="https://www.adafruit.com/product/161/"> link </a> |
|10k Resistors|Reduces voltage by 10k to prevent damage to electronic parts|$0.75 |<a href="https://www.adafruit.com/product/2784 srsltid=AfmBOorDZXOLv9Lx9h1fra0yGWd5E9hwYpEPAJExjlKzU7HgiaWaw3Go/"> link </a> |
|N-channel power MOSFET - 30V / 60A|llows Arduino to control the pump and fan|$2.25|<a href="https://www.adafruit.com/product/355?srsltid=AfmBOooPkirS8LThTlKzDdTRovkNZEuPz7EFrS-6DsfyduYLiH5jbACx/"> link </a> |
|Aluminum water cooling block|absorbs heat from hot solar panel, and allows water to flow through its micro channels so that the water can absorb its heat| $6.29|<a href="https://botland.store/aluminium-heat-sinks/22902-water-block-for-cooling-40x40mm-aluminum-heatsink-for-peltier-cells.html/"> link </a> |
|Liquid Pump|Pumps water through the water block to absorb the solar panel's heat, and pumps that warm water into a radiator, which then loops back to the pump.| $24.95 | <a href="https://www.adafruit.com/product/3910/"> link </a> |
|Tubing|transports water in a cycle, from pump -- water block -- radiator -- pump| $8.00|<a href="https://www.amazon.com/Hooshing-Silicone-Flexible-Winemaking-Transfer/dp/B0BR7SMSHG/ref=pd_ci_mcx_di_int_sccai_cn_d_sccl_1_1/139-5812766-1485522?pd_rd_w=7Oxge&content-id=amzn1.sym.751acc83-5c05-42d0-a15e-303622651e1e&pf_rd_p=751acc83-5c05-42d0-a15e-303622651e1e&pf_rd_r=T6D24261BYKPPCTXS3V0&pd_rd_wg=mdJqA&pd_rd_r=d87a5675-5478-4fd1-a0dd-80e13b2a4b51&pd_rd_i=B08PTXZ51Q&th=1/"> link </a>|
|Temperature sensor|detects and outputs temperature| $9.95 |<a href="https://www.adafruit.com/product/381?srsltid=AfmBOoqYiRXyGEczd5sXp8Ay9t2m8BE7f4jkRrg1vgjZ6Gxci-3dVl2T/"> link </a>|
|N4001 Diode - 10 pack| Protects the MOSFETs from voltage spikes when the pump and fan switch off| $1.50|<a href="https://www.adafruit.com/product/755/"> link </a>|
|Heat Sink Thermal Tape| Helps heat absorption| $4| <a href="https://www.adafruit.com/product/1468/"> link </a>|
|Radiator| Cools down water| $58.32|<a href="https://www.amazon.com/dp/B082585F2J/"> link </a>|
|Radiator fan|Assits the radiator| $34.95|<a href="https://www.amazon.com/dp/B07DXQTCK6?th=1/"> link </a>|
|G1/4 Thread with OD 4mm Nozzle|Screws into the radiator's threaded ports so the silicone tubing can connect|$14.39|<a href="https://www.amazon.com/uxcell-Fitting-Thread-Nozzle-Cooling/dp/B091YQZC3Y?th=1/"> link </a>|
|8mm to 4mm|Bridges the gap between the water block’s 8mm and the tubing|$6.11|<a href="https://www.amazon.com/dp/B07ZCQ5D8F?th=1"> link </a>|








# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
