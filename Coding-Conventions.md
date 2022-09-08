##### What are conventions?
Conventions are rules or agreed on terms that we should all follow to have an easier and organized time. Conventions can change if for a good reason.    
There are two specific convention groups in this document, one for actual code and one for styling. You should probably read both. Read the practical code conventions especially because it explains some things that are always going to appear in the Robot and how to use them.
___
## Practical Code Conventions
### Xbox Binds
We use Xbox controllers a lot in FRC. Usually declaring a Xbox button and using it looks something like this:    
```java
XboxController xCont = new XboxController(1); // declare a new Joystick on port 1 (check Driver Station page to find port #)
JoystickButton button = new JoystickButton(xCont, 1); // declare a new button on xCont on button port 1
// do something with button
```
This process seems relatively simple but becomes annoying when you want to change around the functions of a button. Relating which button port #'s are equal to its name is annoying (how do we know which port # is the A button?). There's also a lot of code that we have to write to declare every single button. This gets annoying and a space hog very quickly. The solution is a HashMap that uses the button name as an index string. Here is that decl:
```java
public HashMap<String, JoystickButton> xboxBinds = new HashMap<String, JoystickButton>();
for (int i = 1; i < MISC.KEY_NAMES.length; i++) {
    xboxBinds.put(MISC.KEY_NAMES[i], new JoystickButton(joystick, i));
}
```
Using this method, we add all the JoystickButtons into a HashMap that we can use later. Here is the [Constants](https://github.com/Aidan747/FRC-Offseason-2022/blob/in-dev/src/main/java/frc/robot/Constants.java) page. In here, you can find the variable KEY_NAMES under the MISC sub class. All of these are names of the Joystick buttons with their appropriate index array. This way you can just get a button by doing:
```java 
// returns the "A" button JoystickButton instance and sets the "when pressed" method to a command.
xboxBinds.get("A").whenPressed(commandOne) 
// Now this command runs when the button is pressed.
```

### 


___
## Code Styling Conventions
### Reference Points
Reference points refer to how we name object variables in relation to parts on the robot. Usually, our reference point is the battery.    
Here's an example of a single sensor in an Indexer subsystem:
```java
DigitalInput indexSensor = new DigitalInput(id);
```
This sets up a new digital input. Since we only have one and it's already located in the class file, it can be assumed it's a sensor in the index. However, having that name allows us to be very certain of what the part does and where it's located.    
It gets more complicated when we introduce multiple sensors.    
For parts that are directly above and below each other use
```java
// one sensor on top, one on bottom
DigitalInput indexSensorTop = new DigitalInput(id);
DigitalInput indexSensorBottom = new DigitalInput(id2);
```
For parts that are on the same vertical level but farther on the horizontal, we name them by number as they would seen from the reference point. This means that the closest would be one, then two, then three, etc. Optionally, if there are only two, you can use front and back (front referring to the farthest from the reference point & back referring closest to the reference point).
```java
DigitalInput inpOne = new DigitalInput(id); // closest
DigitalInput inpTwo = new DigitalInput(id2); // farthest
// optionally, inpOne could be inpBack and inpTwo could be inpFront.
```
Finally, if there are multiple of the same parts on both sides of the robot, include the side that it's on. For example, there are usually four motors in total on a drivetrain. If our reference point is the battery then our motor declarations would look something like this:
```java
// robot reference point = battery
Motor leftMotorOne = new Motor(id); // closest motor on the left
Motor leftMotorTwo = new Motor(id); // farthest motor on the left
Motor rightMotorOne = new Motor(id); // closest motor on the right
Motor rightMotorOne = new Motor(id); // farthest motor on the right
```
Optionally, we can sub out one and two for front and back because there are only two motors on each side,
```java
Motor backLeftMotor = new Motor(id); // closest motor on the left
Motor frontLeftMotor = new Motor(id); // farthest motor on the left
Motor backRightMotorOne = new Motor(id); // closest motor on the right
Motor frontRightMotorOne = new Motor(id); // farthest motor on the right
```
> NOTE: 9 times out of 10, the reference point for everything will be on the "back" of the robot. Usually that's where the battery is.

### Detached Config & Testing
Configs and Testing are parts of subsystems that setup and analyze the pieces of a subsystem. However, declaring the configs and testing within the constructor gets super messy and creates hard to read code. It's required you split up your subsystem config setups into a function and testing method calls into another. Here is an example of code without this style convention:
```java
public DriveTrain(MotorControllerGroup leftMotors, MotorControllerGroup rightMotors, Joystick driveController, MotorController encLeftMotor, MotorController encRightMotor) {
    this.driveController = driveController;

    driveMode = Constants.DriveModes.JOYSTICK_DRIVE;
    LimeMath = RobotContainer.LimeMath;

    this.leftMotors = leftMotors;
    this.rightMotors = rightMotors;

    this.encLeftMotor = (WPI_TalonFX) encLeftMotor;
    this.encRightMotor = (WPI_TalonFX) encRightMotor;

    averageDisplacement = 0;
    
    resetEncoders();
    this.encLeftMotor.configSelectedFeedbackSensor(FeedbackDevice.IntegratedSensor);
    this.encRightMotor.configSelectedFeedbackSensor(FeedbackDevice.IntegratedSensor); // note the configs

    differentialDriveSub = new DifferentialDrive(leftMotors, rightMotors);
    differentialDriveSub.setMaxOutput(Constants.DriveConstants.MAX_OUTPUT); // more configs

    ahrs.reset();
    // i removed most of the analyzation code from this segment because it is really long..
}
```    

Now with our convention:
```java
public DriveTrain(WPI_TalonFX leftMotorOne, WPI_TalonFX leftMotorTwo, WPI_TalonFX rightMotorOne, WPI_TalonFX rightMotorTwo) {
    this.leftMotorOne = leftMotorOne;
    this.leftMotorTwo = leftMotorTwo;
    this.rightMotorOne = rightMotorOne;
    this.rightMotorTwo = rightMotorTwo;
    
    this.left = new MotorControllerGroup(this.leftMotorOne, this.leftMotorTwo);
    this.right = new MotorControllerGroup(this.rightMotorOne, this.rightMotorTwo);
    
    this.drive = new DifferentialDrive(left, right);
    config();
    
    // if we are testing, enable testing mode.
    if (testing) {
      enableTesting();
    }    
  } 

  // a huge boost in readability. Now we can directly edit and add to configs instead of doing it in the constructor.
  public void config() {
    left.setInverted(false);
    right.setInverted(true);
  }
  
  public void enableTesting() {
    // shuffleboard code
  }
}
```
As you can see from these examples, we can and should simplify our DriveTrain code to more simplistic standards. Code also highly depends on being readable. Splitting up different things into more methods is usually a good idea because it makes debugging that much easier. Don't try to do everything on one line. Space things out. Complex does not mean better.

### Naming Conventions
#### General
You should be using UPPER_SNAKE_CASE for anything in the constants class. PascalCase varies, but usually instances of higher importance will have those names. camelCase is the average variable.

#### Abbreviations 
Abbreviations might seem like a bad thing, but some names can get really long. You need to be descriptive in naming your variables because when people read the code they need to get the general idea of a variable as soon as they read it. For example, a variable named ```x``` or ```dVar``` aren't descriptive at all. On the other hand, a variable named ```curDriveVelocity``` is super descriptive. Super long variable names are not great though because they can be horrible to type and look ugly. The best of both worlds is using an abbreviated variable name but also adding a comment describing the variable. A good example:
```java
int curDriveVel = some number; // current drive velocity declaration
```
Only comment on variables that you want to abbreviate. Self explanatory ones or ones with descriptive names don't need it.

### Methods spacing
Methods spacing is relatively trivial but can make things much nicer. Basically, if a method call has a lot of variables that go into the arguments, space them out onto multiple lines. For example, 
```java
// This is super messy. It's hard to read and can be hard to edit for others.
drive.setDefaultCommand(new RunCommand(() -> drive.joyDrive(-joystick.getY(), joystick.getZ()), drive));
```
Try and make code as readable as possible:
```java
drive.setDefaultCommand(new RunCommand(
  () -> drive.joyDrive(-joystick.getY(), joystick.getZ()), drive
));
```
While there are not set rules, spacing out some arguments and putting a long line that makes sense in context into a new line will make things look better. We can see that in the example above, all the args for the ```RunCommand``` are placed onto a new line. That makes it really easy to edit them.
### Imports

#### Import structuring
Split your imports at the top of the screen. Here are the sections you should use:
```java
package frc.robot;

// Java base imports
import java.util.HashMap;

//WPILib imports
import edu.wpi.first.wpilibj.Joystick;
import edu.wpi.first.wpilibj.XboxController;
import edu.wpi.first.wpilibj2.command.Command;
import edu.wpi.first.wpilibj2.command.InstantCommand;
import edu.wpi.first.wpilibj2.command.RunCommand;
import edu.wpi.first.wpilibj2.command.button.JoystickButton;

// Vendor imports
import com.ctre.phoenix.motorcontrol.can.WPI_TalonFX;
import com.kauailabs.navx.frc.AHRS;

// in package imports
import frc.robot.Constants.MOTOR_IO;
import frc.robot.Constants.MISC;
import frc.robot.subsystems.DriveTrain;
```
Notice the four basic import categories: base java (data structures and basically anything out of vendor stuff), WPILib imports meaning from the wpilibj1 or wpilibj2 classes, Vendor imports from vendors and "in package imports" meaning from the frc.robot package where the file is located. Anything from commands, subsystems, constants, etc are placed there.   
After creating it like this, most of the light bulb auto completes (automatically imports your class) will fall into this section as well.

#### Constants Imports
This is SUPER important to keeping lines of code readable. When you want to get a constant from the constants class, please import specifically from the **SUB CLASS**.   
What that looks like in the import statement:
```java
import frc.robot.Constants.MOTOR_IO;
// as opposed to "import frc.robot.Constants"
```
While this seems trivial, we will have multiple embedded constants classes in season. What that means is that only importing the Constants class creates a huge chain of indexes to get to the variable we want. Shown here:
```java
import frc.robot.Constants

// ...
WPI_TalonFX talon = new WPI_TalonFX(Constants.IO.MOTOR_IO.DRIVE_TRAIN.ID1);

// while this is exaggerated, we can go up to three or four indexes, which is just too much.
// this looks much cleaner.

import frc.robot.Constants.IO.MOTOR_IO.DRIVE_TRAIN

// ...
WPI_TalonFX talon = new WPI_TalonFX(DRIVE_TRAIN.ID1);
```
