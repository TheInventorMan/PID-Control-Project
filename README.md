# CarND-Controls-PID
Self-Driving Car Engineer Nanodegree Program

---
## Project Goal

To design a PID controller to drive a car around a race track given only crosstrack error, and being able to only control front wheel angle.

## Overview

A PID controller uses three hyperparameters to minimize error between a desired control value, and the system state. In this project, we were given a vehicle on a racetrack that had two degrees of freedom: front wheel angle, and throttle. As feedback, the simulator would return crosstrack error, or the distance of the car from the centerline of the lane. The PID controller would use the crosstrack error to output a front wheel angle to follow the centerline as closely as possible.  

The front wheel angle is simply a correction signal to bring the car closer to the centerline. To generate this correction signal, a PID controller has three sub-controllers: Proportional, Integral, and Derivative. The proportional controller provides a correction signal directly proportional to the error. The integral controller provides a correction signal proportional to the sum of the errors over time. Finally, the derivative controller provides a correction signal proportional to the rate-of-change of the error. The output correction signal is a simple sum of these three correction signals.  

Each sub-controller has a proportionality constant, to 'weight' the three correction types against each other: Kp, Ki, Kd for the P, I, and D controllers respectively. Kp controls how much the error should directly manipulate the correction signal. Too high, and the correction will be too violent and the car will oscillate right and left. Too low, and the car will be too 'sluggish' and will not follow the centerline closely.  

Ki corrects for accumulated error over time. This is mainly used for systematic biases in the system, resulting in an error that is consistently positive or negative. Too high, and the car will be sluggish to inputs. Too low, and the car will begin to oscillate in a seemingly undefined pattern.

Finally, Kd is a constant for damping the correction signal. As the P and I controllers provide a correction signal that decreases the input error, the rate-of-change is negative, which decreases the correction signal. This prevents overcorrecting of the error so the car's path will smoothly converge onto the centerline, instead of overshooting and oscillating around it. If Kd is too low, the car will overshoot the centerline and begin to oscillate around it for some time. If it's too high, then the car begins to unstably oscillate at a high frequency.  

One issue with this specific implementation is that the only feedback we receive is crosstrack distance error, and not crosstrack angle error. As a result, a vehicle slightly to the side of the crosstrack no different than a vehicle in the same position, but perpendicular to the crosstrack. Due to this issue, there were some oscillations that are impossible to get rid of, since the car must move forward in order to move sideways, and the centerline is not perfectly straight. It it were, the car would eventually converge, but we currently have no information on how the centerline curves, which would have been conveyed by a crosstrack angle error.

## Summary

My implementation uses a PID controller for both steering angle and throttle. When tuning the PID controller for the steering, I started out with P = 1.0, I = 0.0, D = 0.1. Within a few seconds, the car began to oscillate wildly and ended up in the water. After manually tuning, my final values were: P = 0.13, I = 0.0, D = 2.6. In this simulator, there is no systematic error in the car steering, so the I term is set to zero. However, the track is biased to the left, since it is a loop. Because there are right turns as well, the only admissible I term would be inconceivably small, speaking from past experience, so I chose to set it to zero.  

The throttle controller used a very simple PID setup, with P = 0.05, I = 0.0, and D = 0.05. I defined a speed setpoint, and passed the difference between that and the current speed as the error to the PID controller. In return, I got the throttle, which was sent back to the car.
