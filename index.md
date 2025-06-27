# Wrist Rehabilitation Device
Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Samhita M | Irvington High School | Biomedical Engineering | Incoming Junior

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
<!--- # Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE -->



# Second Milestone

<!--**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone -->

**Description:**
For my second milestone, I focused on finding and testing ranges for the flex sensor and accelerometer to measure wrist movement accurately for my rehabilitation device. I programmed the flex sensor to calculate wrist bend angles between 0° and 90°, with flat and fully bent readings of 2000 and 2900, respectively. I chose the value of 2450 for the flex sensor because it represents the midpoint (45°) between flat and fully bent positions. The if loop runs continuously and checks to see if the sensor is bent past the value assigned. This ensures the buzzer activates when the wrist bends beyond approximately halfway through its motion range especially before reaching uncomfortable or unsafe positions. The code I made for the accelerometer was to collect 10 consecutive readings of the X, Y, and Z axes, calculate their averages, and detect key tilt positions based on those averages. The main purpose of the averages was to get rid of inaccuracies  found wrist tilt positions corresponding to X values of about -7 m/s² when bent downward and 4.6 m/s² when bent upward. If the wrist moves outside these ranges, the buzzer buzzes. Together, these components allow the system to track wrist motion precisely and give immediate feedback to guide proper rehab movements.

**Challenges:**
One of the biggest challenges I faced was getting the flex sensor and accelerometer to work together smoothly. Debugging was tricky because even small mistakes in timing or the way I averaged the data caused the buzzer to go off at the wrong times or not at all. Since each sensor sensitively responded at different speeds, I had to spend time adjusting values. Another tough part was making sure the angles calculated from the flex sensor actually matched real wrist positions. I also had to do a good amount of research to find threshold values that consistently told the difference between safe wrist movements and ones that might be risky.

**Next steps:**
My next step is to assemble all the components into a wearable prototype. After assembling, I plan to test the device during wrist exercises to evaluate how accurately it detects motion and provides feedback in real-time. I will observe whether the buzzer alerts at the right times and adjust the calibration ranges if needed to improve accuracy and comfort.


**flex sensor code:** 
```c++
const int flexPin = 34;                   // pin where flex sensor wire is connected
const int buzzerPin = 23;                 // pin where buzzer is connected

const int flatValue = 2000;               //flat value of unbent flex sensor
const int bentValue = 2900;   

void setup() {
  Serial.begin(115200);
  pinMode(buzzerPin, OUTPUT);
}

void loop() {
  int flexValue = analogRead(flexPin);

  float angle = (float)(flexValue - flatValue) * 90.0 / (bentValue - flatValue);    // converts angle into degrees 

  angle = constrain(angle, 0, 90);

  if (flexValue >= 2450) {              //if flex sensor is bent past 2450, buzzer will buzz
    digitalWrite(buzzerPin, HIGH);      
  } else {
    digitalWrite(buzzerPin, LOW);      
  }
  delay(20);

  Serial.print("Sensor: ");
  Serial.print(flexValue);
  Serial.print("  →  Angle: ");
  Serial.print(angle, 1);
  Serial.println("°");

  delay(500);
}
```

**accelerometer code:**
```c++
#include <Adafruit_LSM6DS3TRC.h>
Adafruit_LSM6DS3TRC lsm6ds;

int buzzerPin = 23;
float accelXValues[10];
float accelYValues[10];
float accelZValues[10];
int count = 0;
float sumX = 0;
float sumY = 0;
float sumZ = 0;
float avgX = 0;
float avgY = 0;
float avgZ = 0;
const int bentUp = 4.6;                                               // accelerometer value when tilted up
const int bentDown = -7;                                              // accelerometer value when tilted down

void setup(void) {
  Serial.begin(115200);
  while (!Serial) delay(1);
  pinMode(buzzerPin, OUTPUT);
  Serial.println("Adafruit LSM6DS Accelerometer Only");

  if (!lsm6ds.begin_I2C()) {
    Serial.println("Failed to find LSM6DS chip");
    while (1) delay(100);
  }
  Serial.println("LSM6DS Found!");
}

void loop() {
sensors_event_t accel;
lsm6ds.getAccelerometerSensor()->getEvent(&accel);

  Serial.print("Reading ");
  Serial.print(count + 1);
  Serial.print(": X = ");
  Serial.print(accel.acceleration.x);                                   // prints the x value reading
  Serial.print(", Y = ");
  Serial.print(accel.acceleration.y);                                   // prints the y value reading
  Serial.print(", Z = ");
  Serial.println(accel.acceleration.z);                                 // prints the z value reading
  
  count++;

  sumX += accel.acceleration.x;
  sumY += accel.acceleration.y;
  sumZ += accel.acceleration.z;
  
  Serial.print(accel.acceleration.x);
  if (accel.acceleration.x>=bentUp || accel.acceleration.x<=bentDown) {  // if bent past ranges, buzzer will buzz
    digitalWrite(buzzerPin, HIGH);
  } else {
    digitalWrite(buzzerPin, LOW);
  }

  delay(100);

  if (count == 10) {
    avgX = sumX / 10.0;
    avgY = sumY / 10.0;
    avgZ = sumZ / 10.0;
    Serial.print("average x: ");
    Serial.println(avgX);                                               // prints the average x value from 10 readings
    Serial.print("average y: ");
    Serial.println(avgY);                                               // prints the average y value from 10 readings
    Serial.print("average z: ");
    Serial.println(avgZ);                                               // prints the average z value from 10 readings
    count = 0;
    sumX = 0;                                                           // resets the x,y, and z values to 0 to rerun the code
    sumY = 0;                                                           
    sumZ = 0;                                                           
  }
}

```

# First Milestone

<!-- **Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/CaCazFBhYKs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe> -->

<!-- For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project --->

**Description:**
For my first milestone, I tested each component individually and figured out how each piece worked. Some of the components I tested were:
- Flex sensor
- Accelerometer
- Buzzer

The wrist rehab device uses three main components: a flex sensor, an accelerometer, and a buzzer, all connected to a device called the ESP 32. The flex sensor checks how much the wrist is bent. It works by using a basic electrical setup called a voltage divider, which just means the sensor and another resistor split up the power. Ohms law says that V = I x R (voltage = current x resistance) so when the resistance changes, the voltage also changes. The ESP 32 reads that voltage to figure out how much the wrist is bending. Bigger numbers usually mean more bending. The accelerometer is a motion sensor that measures how fast the wrist moves in three directions: left/right (x), up/down (y), and forward/back (z). The code turns the values into angles, like 40 degrees, to show how tilted the wrist is. The buzzer is the part that makes a sound when the wrist bends too much. If bent too much or the sensor value gets too high (in my code i set this value to 2500 or greater), the buzzer buzzes.

**Challenges:**
The biggest challenge I faced was that the accelerometer's test code wasn't working because the code was for a different type of accelerometer and there wasn't any exisiting code to test it so I had to get multiple parts of code from different areas and put it together. There were also multiple libraries I had to download to run the test code. 

**Next Steps:**
My next step is to work on milestone 2 which is to work on sensor integration and find ranges. I need to research about the correct and incorrect angles to bend your wrist at and add my information into my flex sensor and accelerator code.

<img src="Bluestamp_1.jpg" alt="Alt Text" width="600" height="500"> 

# Starter Project

<iframe width="560" height="315" src="https://www.youtube.com/embed/rH0NynEeV1s?si=uxwH6TJWs3OIG8ov" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<!--- For your first milestone, describe what your project is and how you plan to build it. You can include: -->
<!--- An explanation about the different components of your project and how they will all integrate together -->
<!--- Technical progress you've made so far -->
<!--- Challenges you're facing and solving in your future milestones -->
<!--- What your plan is to complete your project -->

**Description:**
This is my starter project. It is a retro arcade console, and the reason I picked this is because I felt like it interested me the most. Basically, once you press the red button to turn it on, you can click the left and right yellow buttons, which change the games. As you can see, there are multiple games you can play. Some games you can play are Tetris, Snake, etc., etc. Some components in this project are, of course, the buttons, the score tracker in the top right, the port for connecting it to a computer, a buzzer, a battery pack, the clear plastic frame, and so much more. 

**Challenge:**
The biggest challenge I faced was soldering. It was not only tedious but also difficult, as the holes were pretty close to each other, which caused solder to get into the wrong holes. I messed up the soldering for the battery pack, so I had to remove the solder and redo it, but it worked great after that.

**Next Steps:**
My next step is to start on my wrist rehabilitation device. I've set 3 milestones for myself too, which are
  1. Test each component individually (ESP32, buzzer, flex sensors, etc.)
  2. Work on sensor integration and finding ranges
  3. Set up a Bluetooth connection and real-time feedback system with a buzzer
     
I also plan to add some modifications, which include
  1. Add vibration feedback in case the person wants silent updates from the device
  2. Create a progress tracking system that shows updates or improvements over time
  3. Add a small screen that shows live data on the wrist sleeve


 # Schematics 
<!--Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. --->

<img src="bluestamp schematic.png" alt="Alt Text" width="800" height="500">
The image above is a schematic I used for my first and second milestone.

<!--- # Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  Serial.println("Hello World!");
}

void loop() {
  // put your main code here, to run repeatedly:

}
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here. --->
