StepperDriver
=============

A4988, DRV8825 and generic two-pin stepper motor driver library.
Original version, wiring and more info: 
<a href="https://github.com/laurb9/StepperDriver">https://github.com/laurb9/StepperDriver</a>
Thanks to Laurentiu Badea for making this lib!

===========
This version is used for our camera slider project. (ESP8266 + DRV8825 + Motor) 
Pictures, schematics, test video coming soon.

We added:
	- Internal function for non-blocking timming
	- Stop() function
	- status(), isMoving(), getDirection() functions 
	- onMove callback function


Sample code
====

```C++
#include <Arduino.h>
#include "DRV8825.h"

#define MOTOR_STEPS 200
#define MOTOR_DIR_PIN 5
#define MOTOR_STEP_PIN 4
#define DRV_MODE0 13
#define DRV_MODE1 12
#define DRV_MODE2 15

//Left & right limit switch pins
#define DRV_LEFT_STOP 14
#define DRV_RIGHT_STOP 16

int leftSwitch = 0;
int rightSwitch = 0;

DRV8825 driver(MOTOR_STEPS, MOTOR_DIR_PIN, MOTOR_STEP_PIN, DRV_MODE0, DRV_MODE1, DRV_MODE2);

void setup() {
	//init with 100 rpm & 1 microstep
	driver.begin(100, 1);
	
	//onMove callback
	driver.onMove(Driver_OnMove);
}

void loop() {
   //watch for limit switches
   leftSwitch  = digitalRead(DRV_LEFT_STOP);
   rightSwitch = digitalRead(DRV_RIGHT_STOP); 
   
   //if camera moves to left side 
   if (driver.getDirection()==0) {
	  //if the camera reaches the left side switch stop the motor immediately
	  if (leftSwitch) {
		 driver.stop();
	  }
   }
   //if camera moves to right side 
   else if (driver.getDirection()==0) {
	  //if the camera reaches the right side switch stop the motor immediately
	  if (rightSwitch) {
		 driver.stop();
	  }
   }
   
   //move it if needed
   driver.moveLoop();
}

//this function will be called on each motor step
void Driver_OnMove(long rem_steps, long steps_count) {
	//long rem_steps = remaining steps
	//long steps_count = elapsed steps
}
```

