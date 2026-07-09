# Dual Axis Solar Tracker 
Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Brian C | Gunn High | Electrical Engineering | Incoming Junior |

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

# First Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://youtu.be/R-oDt9jQxLU?si=CN162O6yvE0kh_LA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

  For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

#include <Servo.h>
Servo servo_A5;
int POS = 90;
const int DEADBAND = 15; 

void setup() {
  servo_A5.attach(A5, 500, 2500);
  Serial.begin(9600);
  pinMode(A0, INPUT);
  pinMode(A1, INPUT);
  servo_A5.write(90);
  POS = 90;
}

void loop() {
  int a0 = analogRead(A0);
  int a1 = analogRead(A1);
  int diff = a1 - a0;

  Serial.print("servo:"); Serial.println(POS);
  Serial.print("A0:"); Serial.println(a0);
  Serial.print("A1:"); Serial.println(a1);

  if (diff > DEADBAND) {
    POS = constrain(POS + 2, 5, 175);   // stay off hard 0/180
    servo_A5.write(POS);
  } else if (diff < -DEADBAND) {
    POS = constrain(POS - 2, 5, 175);
    servo_A5.write(POS);
  }

  delay(50);  // slow the loop so the servo can settle before re-checking
}


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
|Heat Sink Thermal Tape| Helps heat absorption| $4| <a href="https://www.adafruit.com/product/755/"> link </a>|
|Radiator| Cools down water| $58.32|<a href="https://www.amazon.com/dp/B082585F2J/"> link </a>|
|Radiator fan|Assits the radiator| $34.95| https://www.amazon.com/dp/B07DXQTCK6?th=1|
|G1/4 Thread with OD 4mm Nozzle|Screws into the radiator's threaded ports so the silicone tubing can connect|$14.39|https://www.amazon.com/uxcell-Fitting-Thread-Nozzle-Cooling/dp/B091YQZC3Y?th=1|
|8mm to 4mm|Bridges the gap between the water block’s 8mm and the tubing|$6.11|https://www.amazon.com/dp/B07ZCQ5D8F?th=1|








# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
