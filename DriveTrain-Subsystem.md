## Breakdown
#### What to expect
This tab contains the thought process of developing a simple DriveTrain subsystem. Most people start with a differential drive type drivetrain to start learning actual FRC programming. I will start a Command-based project from scratch, so there will be no blanks to fill in terms of knowledge. There will also be some comments explaining some more in-depth thought processes that are going to appear in most drivetrain related projects. 
> Note: This DriveTrain uses _only_ falcons for driving. It is different for other types of motors; this is on purpose. You will not be able to find answers to every single task you will have just be looking up the docs. You need to learn the ideas behind it here and then be able to apply it anywhere.
___
### Starting point
Make sure you have 2023 of WPILib and create a new command-based project. When you do that you should end up with a file tree like the one shown.       

<img align="right" width="300" height="300" src="https://user-images.githubusercontent.com/93739747/205117736-2efb6ee1-4f2c-4c4d-ab53-dbc1ccddd58d.png">

There are some unneeded things in this file tree. First of all, ```ExampleCommand.java``` and ```ExampleSubsystem.java``` will not be used. However, first, we need to remove all references to those files so we remain errorless in the files we do want to keep. To do that, lets first move to RobotContainer. Hopefully we know that all Subsystems and controllers are decared in RobotContainer. It is where we set up all our commands too. When we first start this project, the only references to ```ExampleCommand.java``` and ```ExampleSubsystem.java``` will be in RobotContainer. This is RobotContainer before we remove unneeded lines of code:    
```java
// Copyright (c) FIRST and other WPILib contributors.
// Open Source Software; you can modify and/or share it under the terms of
// the WPILib BSD license file in the root directory of this project.

package frc.robot;

import edu.wpi.first.wpilibj.GenericHID;
import edu.wpi.first.wpilibj.XboxController;
import frc.robot.commands.ExampleCommand;
import frc.robot.subsystems.ExampleSubsystem;
import edu.wpi.first.wpilibj2.command.Command;

/**
 * This class is where the bulk of the robot should be declared. Since Command-based is a
 * "declarative" paradigm, very little robot logic should actually be handled in the {@link Robot}
 * periodic methods (other than the scheduler calls). Instead, the structure of the robot (including
 * subsystems, commands, and button mappings) should be declared here.
 */
public class RobotContainer {
  // The robot's subsystems and commands are defined here...
  private final ExampleSubsystem m_exampleSubsystem = new ExampleSubsystem();

  private final ExampleCommand m_autoCommand = new ExampleCommand(m_exampleSubsystem);

  /** The container for the robot. Contains subsystems, OI devices, and commands. */
  public RobotContainer() {
    // Configure the button bindings
    configureButtonBindings();
  }

  /**
   * Use this method to define your button->command mappings. Buttons can be created by
   * instantiating a {@link GenericHID} or one of its subclasses ({@link
   * edu.wpi.first.wpilibj.Joystick} or {@link XboxController}), and then passing it to a {@link
   * edu.wpi.first.wpilibj2.command.button.JoystickButton}.
   */
  private void configureButtonBindings() {}

  /**
   * Use this to pass the autonomous command to the main {@link Robot} class.
   *
   * @return the command to run in autonomous
   */
  public Command getAutonomousCommand() {
    // An ExampleCommand will run in autonomous
    return m_autoCommand;
  }
}
```