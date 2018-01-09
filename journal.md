# ROCO222 Coursework

## Lab 1: Git & Hack into a Robot

### First Markdown

I hope that by reading this document I have succeeded in writing this journal to markdown and it's working fine.  Markdown is a form of WYSIWYG (what you see is what you get), that easily translates basic text documents (like this journal) into something that could be used for HTML. It often formats readme files and creates rich text documents from plain text. The main goal of markdown is readability. In the words of John Gruber, who made it: "Markdown is a text-to-HTML conversion tool for web writers. Markdown allows you to write using an easy-to-read, easy-to-write plain text format, then convert it to structurally valid XHTML (or HTML)."

### Command Line 101

ls - lists files and directories

cd - changes current directory

mkdir - creates new directories

echo - writes to a file

cat - "concatenate" creates/views/concatenates files

cp - copies a file

mv - moves/renames a file

rm - deletes a file


### Github

Again, hopefully this should be in a repository in my github account (FishySays), else how did you get here?




## Lab 2: Build a DC Motor

### What is a DC Motor?

A DC motor, is a electrical machine that turns electrical power into mechanical power. It does this by manipulating a magnetic field around an armature. This happens when a current flows through a coil suspended in a magnetic field, causing it to pivot on it's axis and generating torque. However, once it reaches a set point, we have to invert the flow of the current via a commutator in order to keep the torque in the same direction, otherwise it will simply roll back on itself. The more coils you add, reduces torque ripple and ensures it moves in the same direction.

There are two main types of DC Motors, Brushed and Brushless. In this task we were asked to make a brushed simple dc motor, so they are the ones I will discuss.

DC Motors are created from the central armature of a coil of wire, being powered, and that is then rotated around a "chamber" with opposite magnets on either side, creating a magnetic field. They are primarily used in the 21st Century for Cranes, Conveyors and Drills, with many other types going the way of Brushless DC Motors.

### Methodology

 For starters, we had to build the DC motor from scratch. For this we were given; a piece of copper tape (that we split in two) a cork, which we wrapped the copper tape around and sellotaped together. This created our commentator. We then added pins into each end of the cork to either end, creating the shaft for rotation.

<image of it without wire on it>

After that, we took the 10m of copper wire supplied, and wrapped it around the cork length-ways, thereby creating a coil around the cork. This gave us 100 turns. We then soldered the ends of the wire to the commentator, completing the commutator.

<image not on the board>

After this we created a support shaft out of a plank of cardboard, some paperclips creating this.

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_1.png)

And then finally added magnets to either side of the commutator, completeing the setup.



### Testing

 For our testing, we connected the commutator to a power source to act as brushes, and immediately saw the armature begin to rotate.

[This is our video of it working:](https://www.youtube.com/watch?v=kQmZnGIjSNc&feature=youtu.be)

### Changes we'd make

As seen in the final image, our coil was less than ideal, as was the copper tape around the cork, the uneven surface made it harder to apply the "brushes" to the commutator, therefore next time we'd ensure the copper tape was perfectly smooth, perhaps as a classmate of ours did, by cutting a bit of the cork off and attaching it to the top of the copper wire, ensuring it at least stays rigid all the way around, whereas what we did was add the cork piece in after we'd realised our mistake.

Another thing that would help to keep the brushes attached is we could have clamped them in place next to the wire, which would remove the human error shown in the video, where my lab partner's shaky hands caused it to stop.

To improve the way the armature rotates, I would instead 3D Print out a box with holes on either end, then when building this again, I would slide the pins through the hole into the cork waiting in the middle, which would be a lot more sturdy than the paperclips we used for this one.

 We believe from this experiment, talking with classmate who used less coils than us, that using more does increase the speed of the motor, however without testing this more accurately, we cannot say for certain.



## Lab 3: Incremental Encoder

### What is an incremental encoder?

 An incremental encoder is defined as a pulse generator that creates square-wave signals and a zero index. (<http://www.optical-encoders.eu/encoders.html>). Essentially, it converts a displacement (of the disk) into digital pulse signals.

This is made using a rotating disk (usually set to a electric motor), a light source and a photo detector. It works by providing a set amount of pulses in a rotation of the encoder. By knowing the pulses in the rotation and the how long it is for a rotation to complete, you can use it to determine the current rotation of the encoder. This happens because as the encoder disk spins, it recieves pulses of light, which in turn create electrical pulses, which on an oscilloscope or an object looking for a signal, would come out as a 1, and when the target it blocked, creates a 0. As the disk rotates faster, so too does the pulse frequency, therefore the pulse frequency is an indication of the speed of the rotation.

Other incremental encoders commonly use two output channels to determine the position, which are 90 degrees out of phase, which helps tell us which direction the encoder is spinning in (a > b = clockwise, b > a = counter clockwise, for example).

### How did we build it?

By using a photo transistor across from an ultraviolet LED, we were able to create a simplifier incremental encoder via the circuit below.

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_2.png)
Figure 1. The circuit diagram.

Across the phototransistor we hooked up an oscilloscope, and this was the completed circuit, minus the motor.

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_3.png)

Figure 2. The completed circuit, Voltage sense cable was added shortly after!

For the motor, we simply added a cardboard disk with a cut-out slit on it to the central axle of the motor



### The encoder in action.


![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_4.png)
Figure 3. The Encoder with commercial motor.

Unfortunately during this lab session our motor gave up and would not work again. However, we did instead get hold of a commercial motor to see if our circuit worked, which after connecting it to an oscilloscope, showed it did work, as shown in this video:

https://www.youtube.com/watch?v=B1t7ziU7dFo

In the video, you can see our square wave wasn't as clean as it could be, as the slot on our disk was far too small, so the encoder was only changing states very briefly. Also, we had a lot of noise on the system due to how close our led and phototransistor were to the board, so the disk couldn't get close enough.

### Arduino Code

 Using the arduino code provided, I modified it to calculate the angular velocity of the motor via counting the number of pulses per cycle:

```
const byte ledPin = 13;
const byte interruptPin = 2;
volatile byte state = LOW;
volatile int PCount;

void PCounter(){

Pcount++;
}

void setup() {
	Serial.begin(9600);
	pinMode(ledPin, OUTPUT);
	pinMode(interruptPin, INPUT);
	// configure the interrupt call-back: blink is called everytime the pin
	// goes from low to high.
	attachInterrupt(digitalPinToInterrupt(interruptPin), blink, RISING);
}

void loop() {
	Pcount = 0; //reset to 0
	delay(20);
	digitalWrite(ledPin, state);
	int result = Pcount; //holds value

	Serial.print(F("Pulse Count = "));
	Serial.println(result); //output to serial
}

void blink() {
	state = !state;
}
```
### Conclusion

 If I was to redo this I would ensure the two pieces were very far off the board, ensuring that we get a much better read off of the system, as there were a few times where it got caught on the board (due to my hand twitching slightly), thereby altering the wave. This in turn, brings up another problem with my system. I should have clamped the two pieces together, instead of what was shown in the image where I stuck it to the table, and held the motor. 

I also should have split the circle into 8 equal slices, and cut out every odd or even piece. This would have given a perfect sine wave. Instead, we got a small signal increase every time it went through the gap, and was dead silent the rest of the time, thereby making a poor "sine" wave.


![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_5.png)
Figure 4. Left, showing what our encoder disk looked like, vs Right, showing what it ideally would have looked like.




## Lab 4: Controlling the motor

Arduino is a computer software/hardware hybrid microcontroller kit that uses open source software to develop small electronic projects. For this, we utilised the pinouts of it to control a DC motor, primarlily on ports 3, 8, 9, 11, 12 and 13. Using the arduino code, we simply set the pins to high, which sent a pulse along the wire to the motor, turning it on and giving us control. 

Rotation direction is changed by setting ports 12(A) and 13(B) to either high or low to grant it a direction, high being forwards, low being backwards, and speed is changed on ports 3(A) and 11(B), which in this example I have simply set to their maximum value (255).


### What is PWM?

PWM stands for Pulse Width Modulation. Commonly used for controlling motors or dimming LED's it works off a duty cycle, which is "how much the the signal is high". These are usually given as a percentage of the time, for example 50% duty cycle means it is on half the time, giving us essentially half the voltage output, whereas 100% means it's on all the time, giving us maximum voltage output.

In hobby motors this is used in a feedback loop with a hall-effect sensor to essentially see how fast the motor is actually moving vs how fast it should be, and retroactively changing it.

Here is a graphical example of it from sparkfun.com:
![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_6.png)

Fig1. Duty Cycles.

### What is a H-Bridge?

A H-Bridge is a circuit with 4 switches and a load at the centre, looking like a H:

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_7.png)

Fig2. Very Simple H Bridge.


In this example of the H Bridge, if S1 and S4 are turned on (top left to bottom right), then the motor will for example spin forward, whereas if S2 and S3 are turned on, the motor will spin backwards. However, as these are controlled via switches, its very easy to set this up to be controlled by a few lines of code, allowing the motor to told to follow a path. This can further be used with PWM by turning on/off the switches repeatedly to drive the motor at certain speeds or quickly turn a corner by inverting it and reverting it repeatedly.

Turning on two on the same side essentially creates a quick path from the power to the ground and short circuits the power supply.

For the first half of this lab session, we used a small RC Servo Motor to test it out, and used the following code for that.

### Code

```
int delaylength = 20;
int speed = 255;

void setup() {
	pinMode(12, OUTPUT); //HIGH = forwards and LOW = backwards
	pinMode(13, OUTPUT); //HIGH = forwards and LOW = backwards
	pinMode(9, OUTPUT);
	pinMode(8, OUTPUT);
}

void loop() {
	digitalWrite(9, LOW);
	digitalWrite(8, HIGH);
	digitalWrite(12, HIGH);
	analogWrite(3, speed);
	delay(delaylength);

	digitalWrite(9, HIGH);
	digitalWrite(8, LOW);
	digitalWrite(13, LOW);
	analogWrite(11, speed);
	delay(delaylength);

	digitalWrite(9, LOW);
	digitalWrite(8, HIGH);
	digitalWrite(12, LOW);
	analogWrite(3, speed);
	delay(delaylength);

	digitalWrite(9, HIGH);
	digitalWrite(8, LOW);
	digitalWrite(13, HIGH);
	analogWrite(11, speed);
	delay(delaylength);
}
```
### Video:

 https://www.youtube.com/watch?v=N6enz24aQUU&feature=youtu.be

 Very basic operation, simply making move back and forth.


### Close the loop




## Lab 5: Stepper Motors

Stepper motors are synchronous electrical motors which create an equilibrium position when aligned with the stators of a magnetic field. Sequencing the current in the coils forms a magnetic field, which causes a rotation of the motor.

There are two main types, variable reluctance motors, where the rotor isn't permanently magnetised, and permenant magnet steppers, which are. There are also hybrid steppers, which combine both to improve torque, but are more expensive and have smaller steps.

There are four ways of controlling them, which are covered in this lab. They are: full step (1 phase at a time), double step (2 phase at a time), half-step (1 or 2 phase a time) and microstepping (phase based on a sine-cosine wave).

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_8.png)

 Figure 1. A Hybrid Stepper

 Motor


- 1 Phase at a time
- 4 steps per revolution
- 90 degree rotation

**Truth Table**:

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_9.png)


**Code**:

```
void setup() {
	// put your setup code here, to run once:
	pinMode(12, OUTPUT);
	pinMode(9, OUTPUT);
	pinMode(8, OUTPUT);
	pinMode(13, OUTPUT);
}

void Afwd() { //Motor A Forward
	digitalWrite(12, HIGH);
	digitalWrite(9, LOW);
}

void Astop() { //Motor A Stop
	digitalWrite(12, LOW);
	digitalWrite(9, HIGH);
}

void Aback(){ //Motor A Back
	digitalWrite(12, LOW);
	digitalWrite(9, LOW);
}

void Bfwd(){ //Motor B Forward
	digitalWrite(13, HIGH);
	digitalWrite(8, LOW);
}

void Bstop(){ //Motor B Stop
	digitalWrite(13, LOW);
	digitalWrite(8, HIGH);
}

void Bback(){ //Motor B Back
	digitalWrite(13, LOW);
	digitalWrite(8, LOW);
}

void loop() {
	analogWrite (3, 255);
	analogWrite (11, 255);
	Afwd();
	Bfwd();
	delay(10);
	Aback();
	Bfwd();
	delay(10);
	Aback();
	Bback();
	delay(10);
	Afwd();
	Bback();
	delay(10); 
}
```


### Double Step

- 2 Phases at a time
- 4 Steps per revolution
- More Torque than 1 Phase

**Truth Table**:

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_10.png)


**Code**:

```
//Same setup and variables as Full Step, trimmed out to reduce size of this file

void loop() {
	analogWrite (3, 255);
	analogWrite (11, 255);


	Afwd();
	Bstop();
	delay(10);

	Afwd();
	Bfwd();
	delay(10);

	Astop();
	Bfwd();
	delay(10);

	Aback();
	Bfwd();
	delay(10);

	Aback();
	Bstop();
	delay(10);

	Aback();
	Bback();
	delay(10);

	Astop();
	Bback();
	delay(10);

	Afwd();
	Bback();
	delay(10);
}
```


### Half Step

- 1 or 2 Phases on at a time
- 8 Steps per revolution
- 45 degree rotation

**Truth Table**:

![alt text](https://github.com/FishySays/journallymcjournal/blob/master/image_11.png)

**Code**:

(again, same variables and setup as above)

```
void Bfwdhalf(){
	analogWrite (11, 127);
	digitalWrite(13, HIGH);
	digitalWrite(8, LOW);
}

void Bbackhalf(){
	analogWrite (11, 127);
	digitalWrite(13, LOW);
	digitalWrite(8, LOW);
}

void Afwdhalf() {
	analogWrite (3, 127);
	digitalWrite(12, HIGH);
	digitalWrite(9, LOW);
}

void Abackhalf(){
	analogWrite (3, 127);
	digitalWrite(12, LOW);
	digitalWrite(9, LOW);
}


void loop() {
	Afwd(); //1
	Bfwd();
	delay(10);
	Afwd(); //2
	Bfwdhalf();
	delay(10);
	Afwd(); //3
	Bstop();
	delay(10);
	Afwd(); //4
	Bbackhalf();
	delay(10);
	Afwd(); //5
	Bback();
	delay(10);
	Afwdhalf(); //6
	Bback();
	delay(10);
	Astop(); //7
	Bback();
	delay(10);
	Abackhalf();//8
	Bback();
	delay(10);
	Aback(); //9
	Bback();
	delay(10);
	Aback(); //10
	Bbackhalf();
	delay(10);
	Aback(); //11
	Bstop();
	delay(10);
	Aback(); //12
	Bfwdhalf();
	delay(10);
	Aback(); //13
	Bfwd();
	delay(10);
	Abackhalf();//14
	Bfwd();
	delay(10);
	Astop(); //15
	Bfwd();
	delay(10);
	Afwdhalf(); //16
	Bfwd();
	delay(10);
}
```

 As you can see, this initial code is a mess to look at and understand, so I wrote a simple variable called "check" that does exactly the same thing in half the lines of code, where A/B HL stand for the poles of the motor (A or B) going High or Low.


```
void setup() {
	// put your setup code here, to run once:
	pinMode(12, OUTPUT);
	pinMode(9, OUTPUT);
	pinMode(8, OUTPUT);
	pinMode(13, OUTPUT);
}

int check (int Aspeed, boolean AHL1, boolean AHL2, int Bspeed, boolean BHL1, boolean BHL2){
	analogWrite(3, Aspeed);
	digitalWrite(12, AHL1);
	digitalWrite(9, AHL2);
	analogWrite(11, Bspeed);
	digitalWrite(13, BHL1);
	digitalWrite(8, BHL2);
	delay(10);
}

void loop() {
	check(255, HIGH, LOW, 255, HIGH, LOW);//fwdfwd
	check(255, HIGH, LOW, 127, HIGH, LOW);//fwdfwdhalf
	check(255, HIGH, LOW, 0, LOW, HIGH);//fwdstop
	check(255, HIGH, LOW, 127, LOW, LOW);//fwdbackhalf
	check(255, HIGH, LOW, 255, LOW, LOW);//fwdback
	check(127, HIGH, LOW, 255, LOW, LOW);//fwdhalfback
	check(0, LOW, HIGH, 255, LOW, LOW);//stopback
	check(127, LOW, LOW, 255, LOW, LOW);//backhalfback
	check(255, LOW, LOW, 255, LOW, LOW);//backback
	check(255, LOW, LOW, 127, LOW, LOW);//backbackhalf
	check(255, LOW, LOW, 0, LOW, HIGH);//backstop
	check(255, LOW, LOW, 127, HIGH, LOW);//backfwdhalf
	check(255, LOW, LOW, 255, HIGH, LOW);//backfwd
	check(127, LOW, LOW, 255, HIGH, LOW);//backhalffwd
	check(0, LOW, HIGH, 255, HIGH, LOW);//stopfwd
	check(127, HIGH, LOW, 255, HIGH, LOW);//fwdhalffwd
}
'''


### Microstepping

- Single step gives jerky movement at low speed
- Sine-Cosine stepping adjusts the current in each winding, so net torque is constant
- Gradually transfers from one to another
- Duty cycle of A is decreased as B increases

 Essentially, when the value sine is at 90 degrees, it's at maximum voltage (5), and when at 270 degrees, it's at minimum voltage, and the cosine wave is 90 degrees out of phase, so is at minimum at 180 degrees, and at maximum at 0/360.

I had a previous version of this code I wanted to comment on and explain but due to a fault I spent 2 days trying to get rid of I accidentally deleted it. Here's the actual finished piece:

####Code:

```
//#define DEBUG

// Motor winding pins
#define A_DIR_PIN 12
#define A_PWM_PIN 3
#define B_DIR_PIN 13
#define B_PWM_PIN 1 1

#define FORWARD HIGH
#define BACKWARD LOW

#define TWO_PI 6.28318530718
#define STEP_PERIOD 120

#define MICROSTEP_DIVISIONS 8


void setup() {
	// Configure pins for motor windings
	pinMode(A_DIR_PIN, OUTPUT);
	pinMode(A_PWM_PIN, OUTPUT);
	pinMode(B_DIR_PIN, OUTPUT);
	pinMode(B_PWM_PIN, OUTPUT);
	Serial.begin(9600);
}


void setPhase(double angle) {
	int aPWM = 255 * cos(angle); //winding A is the cosine wave
	int aDir = (aPWM > 0.0)? FORWARD : BACKWARD;
	aPWM = abs(aPWM); //these two lines determine if it is negative, then make it not negative, as the motors do not accept negative numbers to make them run
	digitalWrite(A_DIR_PIN, aDir);
	analogWrite(A_PWM_PIN, aPWM);
	
	int bPWM = 255 * sin(angle); //winding B is the sine wave
	int bDir = (bPWM > 0.0)? FORWARD : BACKWARD;
	bPWM = abs(bPWM);
	digitalWrite(B_DIR_PIN, bDir);
	analogWrite(B_PWM_PIN, bPWM);

#ifdef DEBUG
Serial.print("=================="); Serial.print(angle); Serial.println("==================");
Serial.println();
Serial.print("aPWM: "); Serial.println(aPWM);
Serial.print("aDir: "); Serial.println(aDir);
Serial.println();
Serial.print("bPWM: "); Serial.println(bPWM);
Serial.print("bDir: "); Serial.println(bDir);
Serial.println();
Serial.println("======================================================");

#endif
} //debugging just to see where it goes wrong


void loop() {
	for (int microStep = 0; microStep < MICROSTEP_DIVISIONS; microStep++) {
		double phaseAngle = (double)microStep * TWO_PI / (double)MICROSTEP_DIVISIONS;

		setPhase(phaseAngle);

		delay(STEP_PERIOD / MICROSTEP_DIVISIONS);
	}
}
```


### Conclusions:

Overall I feel this lab session went well and I definitely learned a lot of different techniques in Arduino. The first 2 were very simplistic, but by the third task the simplistic techniques I was using, while they worked, could be cleaned up to make the code work smoother.

This became very clear by the 4th task where I had to write several versions of the code until I settled on the piece above.




## Robotic Arm Mini Project

### The Brief

We've been asked to create a Robotic Arm with two degrees of freedom which use servo motors to sweep back and forth. It must be able to sweep back and forth using a sine-cosine wave, then in response to signals from ROS, the robotic operating system.

####Control via RC Servo

```
#include <Servo.h>

Servo servo1;
Servo servo2;

int count;
int cosA;
int sinB;

void calc(){
	cosA = cos(count * 0.01745329) * 180;
	sinB = sin(count * 0.01745329) * 180;
}

void reset(){
	cosA = 0;
	sinB = 0;
}

void setup(){
	servo1.attach(9);
	servo2.attach(8);
}

void loop() {
	for (count = 0; count <= 180; count += 1) {
		calc();
		writeto();
	}
	for (count = 180; count >= 0; count -= 1) {
		calc();
		writeto();
	}
}

void writeto (){
	servo1.write(cosA);
	servo2.write(sinB);
	delay(15);
}
```

 This is very simple code that sweeps the servo back and forth using the Servo.h library provided by Arduino. The sweep "speed" is based on a sine-cosine wave.

### Potentiometer

### The Physical Design


