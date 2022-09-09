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
Notice the embedded classes, ```MOTOR_IO```, ```MISC```, and ```DRIVE_CONSTANTS```. Comments are included to explain the inclusion of an ID. 
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