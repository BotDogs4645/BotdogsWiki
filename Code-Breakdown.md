## Breakdown
#### What to expect
I'll have one robot project with one DriveTrain and indexer subsystem here. We'll go one by one through the files and explain the thought process of how someone might come up with a design and code like this.
___
### Constants
```java
package frc.robot;

// Constants class, usable anywhere if imported. 
// DO NOT SOLELY IMPORT THE CONSTANTS CLASS
// It looks ugly, and refs get very long very quickly.

public final class Constants {
    public static final class MOTOR_IO {
        public static final class DRIVE_IO {
        // drive motor ids go from back to front, ref as the battery (closest to battery -> 1)
            // left motor IO
            public static final int LEFT_ONE = 10;
            public static final int LEFT_TWO = 7;
            // right motor IO
            public static final int RIGHT_ONE = 9;
            public static final int RIGHT_TWO = 8;
        }
        public static final class INDEX_IO {
            public static final int TOP = 5;
            public static final int BOTTOM = 3;
        }
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
        public static final double MAX_COEFF = .8;
        public static final double GEAR_RATIO = 1 / 10.71;
        public static final double ENCODER_TICKS = 2048.0;
        public static final double WHEEL_DIAMETER = 6; // in inches 
        public static final double WHEEL_CIRCUMFRENCE = Units.inchesToMeters(WHEEL_DIAMETER) * Math.PI;
        // circ = d*pi
    }
    
    public static final class INDEXER_CONSTANTS {
        public static final double MAX_VOLTS = 12;
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
import edu.wpi.first.wpilibj2.command.ConditionalCommand;
import edu.wpi.first.wpilibj2.command.InstantCommand;
import edu.wpi.first.wpilibj2.command.RunCommand;
import edu.wpi.first.wpilibj2.command.button.JoystickButton;
import edu.wpi.first.wpilibj.DigitalInput;

// Vendor imports
import com.kauailabs.navx.frc.AHRS;
import com.ctre.phoenix.motorcontrol.can.WPI_TalonFX;


// in package imports
import frc.robot.Constants.MOTOR_IO.DRIVE_IO;
import frc.robot.Constants.MOTOR_IO.INDEX_IO;
import frc.robot.commands.EncoderDrivePrelim;
import frc.robot.commands.Index;
import frc.robot.Constants.MISC;
import frc.robot.subsystems.DriveTrain;
import frc.robot.subsystems.Indexer;

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

  // Indexer
  private WPI_TalonFX indexTop = new WPI_TalonFX(INDEX_IO.TOP);
  private WPI_TalonFX indexBottom = new WPI_TalonFX(INDEX_IO.BOTTOM);
  private DigitalInput beam = new DigitalInput(4); // DIO 4 on roboRIO

  private Indexer index = new Indexer(indexTop, indexBottom, beam);

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
    index.setDefaultCommand(new RunCommand(
      () -> index.idle(), index)
    );

    // binds buttons on the xbox cont to commands
    xboxBinds.get("A").toggleWhenPressed(new EncoderDrivePrelim(drive, 2, 4)); // drives 2 meters @ 4 volts
    xboxBinds.get("B").toggleWhenPressed(new ConditionalCommand(
      new Index(index, true), new Index(index, false), () -> xbox.getRightTriggerAxis() > .5)
    ); // basically, when we press a button we see if the right trigger is pulled.
    // if it is, reject the ball. if it isn't, intake. it's on a toggle system as well.

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
A basic version of the RobotContainer. Notice how it's mostly declaration and setup here. No logic. We declare motors, subsystems, and other parts of hardware that we might need for the robot. I have comments throughout explaining what's going on. Remember, RobotContainer is the very, very first thing that is init'd by the Robot class. It is the first thing that starts. Notice how it goes from the constructor to configureButtonBindings. That's an excellent example of what we also do with our subsystems' testing() and config() methods. 
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
  // Avoid having it all on all the time because the analysis can cause slowness and clutters up Shuffleboard.
  public boolean testing = false;
  
  // Motor instances variables. We are using Falcons, which use integrated Talon controllers.
  private WPI_TalonFX leftMotorOne;
  private WPI_TalonFX leftMotorTwo;
  private WPI_TalonFX rightMotorOne;
  private WPI_TalonFX rightMotorTwo;
  
  // MotorControllerGroups essentially group together motors. This makes them follow each other.
  // Left motor group and right motor group (with ref as battery)
  private MotorControllerGroup left;
  private MotorControllerGroup right;
  
  // A differential drive is basically a motor group on one side and a motor group on the other side. 
  // There are three types: Differential, Mecanum, and Swerve.
  // We have all three types of chassis, but Differential is the easiest to explain.
  private DifferentialDrive drive;
  
  public DriveTrain(WPI_TalonFX leftMotorOne, WPI_TalonFX leftMotorTwo, WPI_TalonFX rightMotorOne, WPI_TalonFX rightMotorTwo) {
    // Take our motors and set them to our instance variables.
    this.leftMotorOne = leftMotorOne;
    this.leftMotorTwo = leftMotorTwo;
    this.rightMotorOne = rightMotorOne;
    this.rightMotorTwo = rightMotorTwo;
    
    // Create our motor controller groups in-house.
    this.left = new MotorControllerGroup(this.leftMotorOne, this.leftMotorTwo);
    this.right = new MotorControllerGroup(this.rightMotorOne, this.rightMotorTwo);
    
    // create our drive in-house.
    this.drive = new DifferentialDrive(left, right);
    
    // Configure all parts of the subsystem into modes and settings we need./
    config();
    // if we are testing, enable testing mode.
    if (testing) {
      enableTesting();
    }
    
  }
  
  public void config() {
    // Motors can either spin clockwise or counterclockwise. Positive .set() values means it spins clockwise. Vice versa with negative.
    left.setInverted(false);
    right.setInverted(false);
  }
  
  // runs testing software to monitor and control specific aspects about the subsystems.
  public void enableTesting() {
    ShuffleboardTab tab = Shuffleboard.getTab("Drivetrain"); // gets the "Drivetrain" tab on Shuffleboard
    ShuffleboardTab tabMain = Shuffleboard.getTab("MAIN");
    tab.add("drivetrain", this); // Adds the drivetrain class into the shuffleboard tab.
  }

  public void voltsDrive(double leftVs, double rightVs) { // Vs is a good abbreviation for volts
    left.setVoltage(leftVs); // Motors take a voltage, between -12 and 12 volts. We are just setting its voltage directly. More volts = more power.
    right.setVoltage(rightVs);

    // when setting voltages, make sure to feed the safety system.
    drive.feed(); // safety systems need a direct input into the DifferentialDrive instance, so when we set left and right
    // individually, we need to feed the system.
  }

  public void joyDrive(double Y, double Z) {
    drive.arcadeDrive(Y * DRIVE_CONSTANTS.MAX_COEFF, Z * DRIVE_CONSTANTS.MAX_COEFF); 
    // arcadeDrive means the joystick rotation is the rotation of the robot and front & back movement of the stick
    // controls forward and back motion of the robot. This method directly relates joystick to movement.
  }

  @Override
  public void periodic() {
    // This method will be called once per scheduler run
  }
}
```
Refer to the comments for most of the information. We have a constructor that lets us put all the motors into a drive system. Then, we have methods that will enable us to manipulate those motors. The idea of the subsystem is that any four motors would work. Subsystems are meant to be manipulated. We have two manipulation methods, ```joyDrive``` and ```voltsDrive```. ```joyDrive``` takes direct input from the joystick (from 0 to 1 of the axis) and then ports that directly to the percentage of the motor's power to apply to the drivetrain. Volts drive is used by commands/code that need to be very precise with the robot's movements.
___
### Drive Command (Encoder Drive)
```java
package frc.robot.commands;

import edu.wpi.first.wpilibj2.command.CommandBase;


import frc.robot.subsystems.DriveTrain;

public class EncoderDrivePrelim extends CommandBase {
  /** Creates a new EncoderDrivePrelim. */
  // Instance variables to store our drivetrain, distance to travel, speed in volts, and the initial encoder value.
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
This is an example of an encoder command. We use encoders to count the rotations of a wheel. That has a lot of uses, including reaching specific parts of the field or moving to specific positions relatively. Here are some notes:   
1. **Motor types**    
   There are specific types of motors that have built-in encoders. Falcon 500s (our primary motor) and NEOs (we have 0) are motors that have built-in encoders. Others, such as CIMs and Mini-CIMs, need specific encoder modules. Usually, we don't use motors that don't have encoders when the application requires an encoder. Most likely, if you are working with encoders, you're using a Falcon 500 or a Romi.
   > Romi has built-in encoders on DIOs 4 and 5 for left and DIOs 6 and 7 for right. However, there are methods to retrieve the encoder values directly in inches on the educational template. Please still review the math in #2 for encoder value calculations.
2. **Encoder value calculations**    
   Integrated Talon controllers sadly do not have direct distance per pulse set methods, so whenever we write a method to return an encoder value that we can understand, we have to do the math there. Usually, that means just converting the motor's counted encoder pulses to meters (use metric). You also need to find the gear ratio of the gearbox connected to the motor. Motors used in FRC do not have enough torque to overcome static friction without slowly destroying the motor. We put it through a gearing, which decreases the free speed of the motor and increases the torque. Usually, a gear ratio can be found on the gearbox manufacturer's website (we use versaplantaries generally). Here's the general formula, with definitions:     
   ### ${P_c \over P_r} = R_s$    
      > Returns shaft rotations.    
        $P_c =$ the pulse count of the encoder (returned by ```MotorObject.getSelectedSensorPosition()```)    
        $P_r =$ amount of pulses in a revolution (2048 in Falcon 500 motors)    
        $R_s =$ shaft rotations
   ### ${R_s \over G} = R_w$
      > Returns wheel rotations    
        $R_s =$ shaft rotations    
        $G =$ gearing    
        $R_w =$ wheel rotations
   ### $R_w * D_w * \pi = D_t$
      > Returns distance traveled in meters    
        $R_w =$ wheel rotations    
        $D_w =$ diameter of the wheel (in meters)    
        $D_t =$ distance traveled (in meters)    
   ### ${P_c \over P_rG} * D_w * \pi = D_t$    
      > What to put in your code, basically the most simplified version.
___
### Indexer Subsystem
```java
package frc.robot.subsystems;

// java base imports

// WPILib imports
import edu.wpi.first.wpilibj2.command.SubsystemBase;
import edu.wpi.first.wpilibj.DigitalInput;
import edu.wpi.first.wpilibj.shuffleboard.Shuffleboard;
import edu.wpi.first.wpilibj.shuffleboard.ShuffleboardTab;

// Vendor imports
import com.ctre.phoenix.motorcontrol.can.WPI_TalonFX;
import com.ctre.phoenix.motorcontrol.NeutralMode;


// In package imports
import frc.robot.Constants.*;

public class Indexer extends SubsystemBase {
  /** Creates a new Indexer. */

  public boolean testing = true;

  private WPI_TalonFX top;
  private WPI_TalonFX bottom;

  private DigitalInput beam;

  public Indexer(WPI_TalonFX top, WPI_TalonFX bottom, DigitalInput beam) {
    this.top = top;
    this.bottom = bottom;
    this.beam = beam;
    
    config();
    if (testing) {
      enableTesting();
    }
  }

  public void config() {
    // neutral mode essentially means what it does at 0% rpm.
    // does it keep going or is it gonna brake?
    // we want it to brake.
    top.setNeutralMode(NeutralMode.Brake);
    bottom.setNeutralMode(NeutralMode.Brake);
  }

  public void enableTesting() {
    // here is a bunch of shuffleboard code,
    // this is essentially how you view variables in real time.
    // that "() -> method call" is just a "variable supplier". it returns a value and is sent to shuffleboard
    ShuffleboardTab indexerTab = Shuffleboard.getTab("Indexer");
    indexerTab.addString("Top Belt Speed", () -> getBottomBeltSpeed() * 100 + "%");
    indexerTab.addString("Bottom Belt Speed", () -> getTopBeltSpeed() * 100 + "%");
    indexerTab.addBoolean("Beam Trip", () -> isTripped());
  }

  public void setBottomBeltSpeed(double percent) {
    bottom.setVoltage(INDEXER_CONSTANTS.MAX_VOLTS * percent);
  }

  public void setTopBeltSpeed(double percent) {
    top.setVoltage(INDEXER_CONSTANTS.MAX_VOLTS * percent);
  }

  public double getBottomBeltSpeed() {
    // get returns speed from -100% to 100% of it's maximum speed.
    return bottom.get(); // get the bottom belt speed in indices [-1, 1]
  }

  public double getTopBeltSpeed() {
    return top.get(); // get the top belt speed in indices [-1, 1]
  }

  public boolean isTripped() {
    return beam.get(); // if the beam is tripped, return true.
  }

  public void idle() {
    setTopBeltSpeed(0);
    setBottomBeltSpeed(0);
  }

  @Override
  public void periodic() {
    // This method will be called once per scheduler run
  }
}
```
This is our indexer subsystem. An indexer, in ball games specifically, takes an object from the intake and stores it within the boundaries of the robot. The indexer needs to be able to store the balls and then move them towards another position. That other position could be the shooter or the floor if we want to reject the ball. Usually, an indexer consists of pulleys and motors to drive those pulleys. They can also include hardware switches such as limit switches, or beam breaks to enumerate (to classify in number form) positions so we can easily decide where balls are present.
___
### Index Command
```java
package frc.robot.commands;

import edu.wpi.first.wpilibj2.command.CommandBase;

// java base imports

// WPILib imports

// Vendor imports

// In package imports
import frc.robot.subsystems.Indexer;

public class Index extends CommandBase {
  /** Creates a new Index. */
  Indexer indexSub;
  boolean reject;

  public Index(Indexer indexSub, boolean reject) {
    this.indexSub = indexSub;
    this.reject = reject;
    addRequirements(indexSub);
  }

  // Called when the command is initially scheduled.
  @Override
  public void initialize() {}

  // Called every time the scheduler runs while the command is scheduled.
  @Override
  public void execute() {
    /* here's some psuedocode:
       if (this is not the reject command) {
         if (beam is not broken) {
           set indexSub's top belt to 50% max power;
         } else {
           set indexSub's top belt to 0% max power; // not moving
         }
         set indexSub's bottom belt speed to 50% max power // we always run the bottom
       } else {
         // reverses belt to effectively "reject" the ball.
         set indexSub's top belt to -50% max power;
         set indexSub's bottom belt to -50% max power; 
       }
     }
    */
    if (!reject) {
      if(!indexSub.isTripped()) {
        // run top motors if it isnt tripped b/c we can store a ball in there
        indexSub.setTopBeltSpeed(.5);
      } else {
        indexSub.setTopBeltSpeed(0);
      }
      indexSub.setBottomBeltSpeed(.5);
    } else {
      indexSub.setTopBeltSpeed(-.5);
      indexSub.setBottomBeltSpeed(-.5);
    }
  }

  // Called once the command ends or is interrupted.
  @Override
  public void end(boolean interrupted) {}

  // Returns true when the command should end.
  @Override
  public boolean isFinished() {
    return false;
  }
}
``` 
The execute portion is the most critical piece of this sub. You can see all the logic that's present there. The "reject" boolean is set to true and false in two different copies of this command. You can see them in the RobotContainer. Two new Indexes are created, with one having "true" and the other "false." The "true" one is only run when the right bumper is depressed, effectively creating something like a shortcut (just like the "ctrl" in "ctrl+c" to copy. C on its own will type C on a keyboard, while "ctrl" will modify it to copy.)