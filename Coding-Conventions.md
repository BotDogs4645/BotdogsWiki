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
As you can see from these examples, we can easily simplify our DriveTrain code to more simplistic standards. Code also highly depends on being readable. Splitting up different things into more methods is usually a good idea because it makes debugging that much easier. Don't try to do everything on one line. Space things out. Complex does not mean better.

### Naming Conventions
#### Abbreviations 
Abbreviations might seem like a bad thing, but some names can get really long. You need to be descriptive in naming your variables because when people read the code they need to get the general idea of a variable as soon as they read it. For example, a variable named ```x``` or ```dVar``` aren't descriptive at all. On the other hand, a variable named ```curDriveVelocity``` is super descriptive. Super long variable names are not great though because they can be horrible to type and look ugly. The best of both worlds is using an abbreviated variable name but also adding a comment describing the variable. A good example:
```java
int curDriveVel = some number; // current drive velocity declaration
```
Only comment on variables that you want to abbreviate. Self explanatory ones or ones with descriptive names don't need it.