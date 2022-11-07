## PIDTuner
PIDTuner is an in-house, easy to use PID tuning suite set up directly into Shuffleboard. No longer will programmers have to slave over PID tuning for a long time. Now, that crucial time can be spent instead pre-programming other subsystems instead of these horrible feedback loops.
___
#### What is a PID controller?
PID Controllers refer to Proportional, Integral and Derivative controllers. In short, think of a PID Controller as a black box. The black box receives two values: a setpoint, where want to be at, and a current point, where we are at currently at. The PID Controller then uses SUPPLIED constants to do complex calculus to give you a value. That value is usually what we feed back into the "set" method, or what sets the rpm, the voltage, etc. The hard part is finding those constants, of which there are three: P, I and D. This tuner will help you explore PID and tune motors very easily.

#### What does PIDTuner do?
PIDTuner gives you the ability to tune any Falcon motor's PID settings. These values can then be documented and then forwarded into actual values that are constantly applied to that motor in play. Within the suite, there is also the ability to activate a benchmark mode, giving the tuners the ability to see if their change was meaningful or not.

___
#### How do I declare a PIDTuner?
First, the hard part: Getting the utils directory. Hopefully you already have it, but if not, just download a copy of the official 4645 "init-repo" and use that. After that, it's simple. Here's a declaration example:

![image](https://user-images.githubusercontent.com/93739747/200406168-f9c2ab4a-6237-4242-a7e2-1dfae7f072c8.png)

___
#### What do I do now?
Now, you'll want to build and push code to the robot so you can control your motor. That should automatically activate Shuffleboard, but if it doesn't you can manually open it by accessing the windows start menu and typing Shuffleboard. As you open that, you should see a new tab at the top which has the name of what you gave it in code. Click on the tab. All of your tools should be in order.

___
#### What does it look like?
