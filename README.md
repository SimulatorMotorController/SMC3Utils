# SMC3Utils

## Installation

Simply clone or [download](https://github.com/SimulatorMotorController/SMC3Utils/archive/master.zip) SMC3Utils into a directory of your choice and run from there.

Edit the SMCUtils.ini file with notepad or a text editor to setup your serial com port used to connect to Arduino.

## Tuning Parameters

OK, now that you know your motors aren’t going to race off in the wrong direction you can start tuning the parameters. Again this is done with the Windows SMC3 Utility software – so far we haven’t gone near any sim software! If you have experience tuning PID control loops then you can probably jump in and setup to your liking, however it would be worth scanning the following steps to get familiar with the clipping, limiting, and braking parameters

Clip Input Used to create a band at either limit of the target range beyond which any values sent to the SMC3 are clipped. In addition to this clipping, if the motor feedback does move beyond this range (typically through inertia), the SMC3 will attempt to brake the motors hard by driving them in reverse until they are back out of this limit zone. The value can be anything from 0-255 (however can’t be less than the current limit setting). Reverse braking is applied in the band between the Clip Input and the Max Limit settings. Reverse braking can be disabled - refer to PWMrev.

Max Limits Used to create a band at either limit of the feedback range beyond which if the motors move (typically through inertia) the SMC3 will automatically shutdown the drivers and keep them disabled until reset. This is essentially a safety mechanism if something goes wrong. The value can be between 0-255 (however can’t be greater than the current clip setting).

## Limits.JPG 

Deadzone This creates a hysteresis zone for the motor feedback If you need values greater than 1, you probably have too much “slack” in your mechanical setup.

Fpid / By default the PID loop performs 4000 calculation updates per second. This divider enables you to reduce the number of PID calculations per second by the divided amount. Sometimes this can help if the motor windings are producing audible noise. The approx number of calcs per second is displayed in the center bottom of the SMC3 Utils window. (This may jump around a bit as it is just an indicator)

Kp The proportional term of the PID loop. Essentially the larger the value here the “harder” the SMC3 controller will try to drive the motor to bring it to its target position. It is scaled by the distance away the motor is from its current target position, again the further away the harder it is driven (within the constraints set by other parameters) This is typically the main tuning parameter and the greatest effect – this can even be used alone with Ki and Kd set to 0. The setting will be very dependent on the motors, H-bridges, and mechanical loads however values in the range 300 to 500 would not be uncommon with the SMC3. Allowed values range from 0-1000.

Ki The integral term is useful to help get the steady state error closer to zero. Generally speaking for sim designs I have found that if you have large enough PWMmin and Kp values the need for Ki is reduced and it can often be set to 0. Too large a Ki can cause the position to overshoot and oscillate.

Kd The derivative term is great for reducing overshoot on fast step changes in position... The negative effect is that it can slow down the movement particularly when the target undergoes a step change.

Ks This is a "smoothing" parameter for the Kd term. Essentially the Kd parameter is only computed when the loop counter matches this value. So a Ks of 5 means the Kd term will only be updated once every 5 times the other parameters are calculated.

Fpwm The frequency of the selected Motor PWM. Note Motors 1 and 2 are tied together and will always have the same PWM frequency and have more selection options than Motor 3. This is a limitation of the microprocessor used on the Arduino Uno.

PWMmin This is the minimum duty cycle that is used to drive the motors to their target position. For example if the motors are already close to target they will not need to be driven as hard as if they are a long way from target (or they may overshoot and oscillate). The reason we don’t just use 0 is that due to friction, loads, and other factors, the motors will need a certain minimum current just to get them moving. This helps us set that minimum. Settings are in the range 0-255 (which are mapped to the range of 0-100% duty cycle) however cannot be greater than PWMmax setting.

PWMmax This is the maximum duty cycle that is used to drive the motors to their target position. As the target position gets further away from the actual position the control loop attempts to increase the motor drive (PWM) to get it to the target position. It will increase the PWM until it reaches PWMmax.

PWMrev This is the PWM duty cycle that is used to drive the motors in reverse (hard brake) if they enter the limit zone. Higher values here will brake the motor harder but will draw more current and heat the H-Bridge more. It may also cause an abrupt jolt in your sim as it tries to change direction. Setting this to a similar value as PWMmax would make sense. If you want to disable the revers braking then simply set this value to 0 and the position will be driven by the normal PID algorithm. Settings are in the range 0-255 which are mapped to the range of 0-100% duty cycle.


## Other Settings

Connecting to Simtools via UDP 

Simtools has the great ability to output the motion data over your network via UDP instead of (or as well as) sending data via a serial port. The Windows SMC3 Utility software now has the ability to receive this network UDP motion data from Simtools and pass it onto the Arduino SMC3. So why setup such a complicated arrangement? Well it’s not actually that complicated and it allows you to perform real time monitoring and setup of the controller while Simtools is running without the need for the second serial port hardware mentioned above. If this approach is used it is still recommended that a second computer be used to run the Windows SMC3 Utility software (especially if you are just starting out with Sim software).
