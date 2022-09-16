## Breakdown
#### What to expect
I'll have one robot project with one DriveTrain and indexer subsystem here. We'll go one by one through the files and explain the thought process of how someone might come up with a design and code like this.
___
### Constants
```java
package frc.robot;

// Constants class, usable anywhere if imported. 
// DO NOT SOLELY IMPORT THE CONSTANTS CLASS
// It looks ugly and refs get very long very quickly.

public final class Constants {
    public static final class MOTOR_IO {
        // motor ids go from back to front, ref as battery (closest to battery -> 1)
        // left motor IO
        public static final int LEFT_ONE = 10;
        public static final int LEFT_TWO = 7;
        // right motor IO
        public static final int RIGHT_ONE = 9;
        public static final int RIGHT_TWO = 8;
    }
    
    public static final class MISC {
        public static final String[] KEY_NAMES = {
            "null",
            "A",
            "B",
            "X",
            "Y",
            "LB",
            "RB",
            "back",
            "start"
        }; // used to bind our ids to buttons for the bind hashmap
    }

    public static final class DRIVE_CONSTANTS {
        public static final double MAX_COEFF = .8; // highest percentage output that the joystick will accept from the motors (80%)
    }
}
```
Constants are variables that don't change. Notice how every single variable has that "Constant" modifier which is ```public static final```.
Notice the embedded classes, ```MOTOR_IO```, ```MISC```, and ```DRIVE_CONSTANTS```. Comments are included to explain the inclusion of the IDs. 
___
### RobotContainer
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

public class RobotContainer {
  /* 
    Base level classes (motors, subsystems, etc) are declared here
    Logic should be used only in subsystems/commands
  */
  // current reference point: Battery = back of robot
  
  // DriveTrain motors  
  // Our motors are Falcon 500s, which are controlled by integrated Talon controllers.
  private WPI_TalonFX leftOne = new WPI_TalonFX(MOTOR_IO.LEFT_ONE);
  private WPI_TalonFX leftTwo = new WPI_TalonFX(MOTOR_IO.LEFT_TWO);

  private WPI_TalonFX rightOne = new WPI_TalonFX(MOTOR_IO.RIGHT_ONE);
  private WPI_TalonFX rightTwo = new WPI_TalonFX(MOTOR_IO.RIGHT_TWO);

  // Our custom DriveTrain subclass, which is mentioned below.
  private DriveTrain drive = new DriveTrain(leftOne, leftTwo, rightOne, rightTwo);

  // Joystick & navX (is static so it can be referenced anywhere)
  public static final Joystick joystick = new Joystick(0); // identify your peripheral ids on DS
  public static final XboxController xbox = new XboxController(1);
  public static final AHRS navX = new AHRS(); // navX is a gyroscope. It gives us direction.
  // Why not put these in constants? Well, we want to declare it AFTER the robot has started.
  // Constants are established immediately, which is not ideal.

  // init of JoystickButton hashmap. Usage in the @RobotContainer method
  public HashMap<String, JoystickButton> xboxBinds = new HashMap<String, JoystickButton>();
  // look up HashMap docs if you are confused. 
  // When in doubt, use a hashmap.

  public RobotContainer() {
    // bind JoystickButtons to string keys in the hashmap
    // usage: (binds.get({name}) -> JoystickButton), names are located in Constants under MISC, usable anywhere with a valid
    // RobotContainer object (there should only be one)
    for (int i = 1; i < MISC.KEY_NAMES.length; i++) {
      xboxBinds.put(MISC.KEY_NAMES[i], new JoystickButton(xbox, i));
    }
    
    // default command
    // Basically, this tells the Command Scheduler that this command should be scheduled whenever the drive subsystem
    // is not required by another command.
    drive.setDefaultCommand(new RunCommand(
      () -> drive.joyDrive(-joystick.getY(), joystick.getZ()), drive)
    );
    // Configure the button bindings
    configureButtonBindings();
  }

  // Configures button bindings using hashmap
  private void configureButtonBindings() {}
  
  // Returns the currently used SendableChooser auto profile (names in Constants)
  public Command getAutonomousCommand() {
    // InstantCommand being returned right now for simplicity's sake (does nothing)
    // InstandCommands run immediately, once, and then end.
    return new InstantCommand();
  }
}
```
Super basic version of the RobotContainer. Notice how it's mostly declaration and setup here. No logic. We are declaring motors, subsystems and other parts of hardware that we might need for the robot. I have comments throughout explaining what's going on. Remember, RobotContainer is the very, very first thing that is init'd by the Robot class. It is the first thing started. Notice how it goes from Constructor to configureButtonBindings. That's a good example of what we also do with the testing() and config() methods in our subsystems. 
___
### DriveTrain Subsystem
```java
package frc.robot.subsystems;

// java base imports

// WPILib imports
import edu.wpi.first.wpilibj.drive.DifferentialDrive;
import edu.wpi.first.wpilibj.motorcontrol.MotorControllerGroup;
import edu.wpi.first.wpilibj2.command.SubsystemBase;

// Vendor imports
import com.ctre.phoenix.motorcontrol.can.WPI_TalonFX;

// In package imports
import frc.robot.Constants.DRIVE_CONSTANTS;

public class DriveTrain extends SubsystemBase {
  /** Creates a new DriveTrain. */
  
  // allows us to have a "testing mode" whenever we want.
  // Avoid having it all on all the time because analyzation can cause slowness and clutters up Shuffleboard.
  public boolean testing = false;
  
  // Motor instances variables. We are using Falcons, which use integrated Talon controllers.
  private WPI_TalonFX leftMotorOne;
  private WPI_TalonFX leftMotorTwo;
  private WPI_TalonFX rightMotorOne;
  private WPI_TalonFX rightMotorTwo;
  
  // MotorControllerGroups essentially group together motors. This makes it so they follow each other.
  // Left motor group and right motor group (with ref as battery)
  private MotorControllerGroup left;
  private MotorControllerGroup right;
  
  // A differential drive is basically a motor group on one side and a motor group on the other side. 
  // There are three types: Differential, Mecanum and Swerve.
  // We have all three types of chassis, but Differential is the easiest to explain.
  private DifferentialDrive drive;
  
  public DriveTrain(WPI_TalonFX leftMotorOne, WPI_TalonFX leftMotorTwo, WPI_TalonFX rightMotorOne, WPI_TalonFX rightMotorTwo) {
    // Take our motors and set them to our instance variables.
    this.leftMotorOne = leftMotorOne;
    this.leftMotorTwo = leftMotorTwo;
    this.rightMotorOne = rightMotorOne;
    this.rightMotorTwo = rightMotorTwo;
    
    // Create our motor controller groups in house.
    this.left = new MotorControllerGroup(this.leftMotorOne, this.leftMotorTwo);
    this.right = new MotorControllerGroup(this.rightMotorOne, this.rightMotorTwo);
    
    // create our drive in house.
    this.drive = new DifferentialDrive(left, right);
    
    // Configure all parts of the subsystem into modes and settings we need./
    config();
    // if we are testing, enable testing mode.
    if (testing) {
      enableTesting();
    }
    
  }
  
  // we should start doing this with our classes, just so we can easily change configs without the mess in the decl.
  public void config() {
    // Motors can either spin clockwise or counterclockwise. Positive means it spins clockwise. However, since we invert the direction
    // Of the right side motors, we have to invert the right side to match.
    left.setInverted(false);
    right.setInverted(true);
  }
  
  // runs testing software to monitor and control specific aspects about the subsystems.
  public void enableTesting() {
    ShuffleboardTab tab = Shuffleboard.getTab("Drivetrain"); // gets the "Drivetrain" tab on Shuffleboard
    ShuffleboardTab tabMain = Shuffleboard.getTab("MAIN");
    tab.add("drivetrain", this); // Adds the drivetrain class into the shuffleboard tab.
  }

  public void voltsDrive(double leftVs, double rightVs) { // Vs is a good abrev. for volts
    left.setVoltage(leftVs); // Motors take a voltage, between 1-12 volts. We are just setting it's voltage directly. More volts = more power.
    right.setVoltage(rightVs);

    // when setting voltages, make sure to feed the safety system.
    drive.feed(); // safety systems need a direct input into the DiffDrive class, so when we set left and right
    // individually, we need to feed the system.
  }

  public void joyDrive(double Y, double Z) {
    // add slope later?
    double fY = Math.min(Y, DRIVE_CONSTANTS.MAX_COEFF); // make sure we can't drive more than a max value.
    double fZ = Math.min(Z, DRIVE_CONSTANTS.MAX_COEFF);
    drive.arcadeDrive(fY, fZ); // arcadeDrive means the joystick rotation is the rotation of the robot and front & back control
    // the forward and back motion of the robot. This method directly relates joystick to movement.
  }

  @Override
  public void periodic() {
    // This method will be called once per scheduler run
  }
}
```
Refer to the comments for most of the information. Basically, we have a constructor that allows us to put all the motors into a drive system. Then, we have methods that allow us to manipulate those motors. The idea of the subsystem is that any four motors would work. Subsystems are meant to be manipulated. We have our two manipulation methods, ```joyDrive``` and ```voltsDrive```. ```joyDrive``` takes direct input from the joystick (from 0 to 1 of the axis) and then ports that directly to the percentage of the motors power to apply to the drivetrain. Volts drive is more specifically for other things that we might have manipulate the DriveTrain such as a navX and RamseteCommand (read abt in Concepts).
___
### Drive Command (Encoder Drive)
```java
package frc.robot.commands;

import edu.wpi.first.wpilibj2.command.CommandBase;


import frc.robot.subsystems.DriveTrain;

public class EncoderDrivePrelim extends CommandBase {
  /** Creates a new EncoderDrivePrelim. */
  // Instance variables to store our drivetrain, distance to travel, speed in volts and the initial encoder value.
  DriveTrain drive;
  double dist;
  double speedVs;
  double initEncoder;

  public EncoderDrivePrelim(DriveTrain drive, double dist, double speedVs) {
    this.drive = drive;
    this.dist = dist;
    this.speedVs = speedVs;
    // We set the requirement to run this command to be the drivetrain subsystem.
    // this effectively stops all "setDefaultCommand" commands from being scheduled.
    addRequirements(drive);
  }

  // Called when the command is initially scheduled.
  @Override
  public void initialize() {
    // we set our initial encoder value to the one we init at.
    // since our encoder will most likely not be 0, we have to subtract the initial to get the actual change in
    // encoder value.
    this.initEncoder = drive.getEncoderLeft();
  }

  // Called every time the scheduler runs while the command is scheduled.
  @Override
  public void execute() {
    // drive @ the speed we set in the constructor ^
    drive.voltsDrive(speedVs, speedVs);
  }

  // Called once the command ends or is interrupted.
  @Override
  public void end(boolean interrupted) {
    // when the command ends, we want to stop. set both sides voltage to 0.
    drive.voltsDrive(0, 0);
  }

  // Returns true when the command should end.
  @Override
  public boolean isFinished() {
    // returns a bool that represents "is the absolute change in encoder from where we started greater than the distance we want to travel?"
    // if yes, then return true and end the command because we have driven the requirement.
    return Math.abs(drive.getEncoderLeft() - initEncoder) <= dist
  }
}
```
This is an example of an encoder command. We use encoders to count the rotations of a wheel. That has a lot of uses including reaching specific parts of the field or getting to specific positions relatively. Here are some notes:   
1. **Motor types**    
   There are specific types of motors that have built-in encoders. Falcon 500s (our primary motor) and NEOs (we have 0) are the motors that have built in encoders. Others such as CIMs and Mini-CIMs need specific encoder modules. Usually, we don't use motors that don't have encoders when the application requires an encoder. Most likely if you are working with encoders, you're using a Falcon 500 or a Romi.
   > Romi has built in encoders on DIOs 4, 5 for left and DIOs 6, 7 for right. However, on the educational template, there are methods to retrieve the encoder values directly in inches. Please still review the math in #2 for encoder value calcuations.
2. **Encoder value calculations**    
   Integrated Talon controllers sadly do not have direct distance per pulse set methods, so whenever we write a method to return an encoder value that we can understand, we have to do the math there. Usually, that means just converting the motor's counted encoder pulses to something like meters (use metric). You also need to find the gear ratio of the gearbox connected to the motor. Motors used in FRC do not have enough torque to overcome static friction without slowly destroying the motor. We put it through a gearing, which decreases the free speed of the motor and increases the torque. Usually, a gear ratio can be found on the gearbox manufactor's website (we use versaplantaries generally). Here's the general formula, with definitions:     
   ### ${P_c \over P_r} = R_s$    
      > Returns shaft rotations.    
        $P_c =$ the pulse count of the encoder (returned by ```MotorObject.getSelectedSensorPosition()```)    
        $P_r =$ amount of pulses in a revolution (2048 in Falcon 500 motors)    
        $R_s =$ shaft rotations.
   ### ${R_s \over G} = R_w$
      > Returns wheel rotations    
        $R_s =$ shaft rotations    
        $G =$ gearing
   ### $R_w * D_w * \pi = D_t$
      > returns distance traveled in meters    
        $R_w =$ wheel rotations    
        $D_w =$ diameter of the wheel (in meters)    
        $D_t =$ distance traveled (in meters)    
   ### ${P_c \over P_rG} * D_w * \pi = D_t$    
      > what to put in your code, basically the most simplified version.
___
### Indexer Subsystem
```java
```
This is our indexer subsystem. An indexer, in ball games specifically, takes an object from the intake and stores it within the boundaries of the robot. The indexer needs to be able to store the balls, and then move them towards another position. That other position could be the shooter, or the floor if we want to reject the ball. Usually, an indexer consists of pulleys and motors to drive those pulleys. They can also include hardware switches such as limit switches or beam breaks to enumerate (to classify in number form) positions so we can easily make decisions based on where balls are present. 