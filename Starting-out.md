### Where to start
To start, go and read the official [WPILib Wiki](https://docs.wpilib.org/en/stable/docs/software/what-is-wpilib.html) intro page that overviews general stuff about the program.   
WPILib is a collection of extensions in VS Code that allow us the best way to write code for the robot.  
___
#### Installing at home
1. Visit the [GitHub Page for WPILib](https://github.com/wpilibsuite/allwpilib/releases/tag/v2022.4.1).
2. Scroll to the bottom and install the correct download for your OS.
3. Open the ISO or equivalent and run the WPILib_Installer.exe or equivalent.
4. Complete download with your preferred options, but choose "Download for this Computer Only".
5. To access, open the Start Menu and search for 2022 WPILib VS Code.   

*NOTE:* WPILib installation on school computers should be done preferably by the programming or electronics leads.
___
#### Creating a Project
1. Open 2022 WIPLib VS Code from either the Start Menu or the icon on your desktop screen.
2. Use *CTRL+SHIFT+P*. This opens a start menu within VS Code that is extremely useful. We use it a lot.
3. Type "Create a new Project" and select the WPILib option (has WPILib: next to it.)
4. A new screen has popped up. For the options choose:
     * Java
     * example
     * Command Based
5. Select a directory, can be any folder.
6. Type in our team number, 4645. 
7. Create project.
___
#### Parts of a command robot
* **Constants**    
    The constants file is full of values and objects that do not change. Values like wheel sizes or max velocities of parts should be stored here. All of the values should start with the Constant tag or "public final static" which allows us to access it anywhere and make sure it doesn't change.
* **Main**    
    This is where the Robot starts. We usually don't touch it at all.
* **Robot**    
     Some very basic, crucial pieces of the robot are stored here. Like an Arduino or Argon, there is a forever while loop that constantly runs on the robot's hardware. This while loop calls on these functions in Robot constantly, every 20 ms or so. They all have self explanatory names such as ```RobotInit``` (runs only once, at the initialization of the robot) or ```RobotPeriodic``` (runs always, at the 20 ms time frame). A very minor fraction of our code will be written here, so it's probably best not to mess with it unless you know what you're doing.
* **RobotContainer**    
    The file that holds most of our declarations of objects and subsystems. It "contains" most of the robot's counterparts to hardware. A lot of code of yours will be used here, but no logic. This is mainly for declaration and setting up the rest of the robot's software. Method names inside are pretty self explanatory.     
* **Subsystems**    
    Subsystems are a very, very crucial part of our robot. Subsystems group together different parts. For example, the DriveTrain subsystem has usually four motors inside of it. The DriveTrain subsystem class allows us to put our declared motors into a class that basically creates functionality for all the motors to work together.
* **Commands**    
    Commands are closely related to subsystems. Commands usually manipulate the subsystems by running their methods. Subsystems usually contain methods that allow you to control the individual parts of the subsystem, but the logic does not exist. The commands contain the logic. For example, I could have the DriveTrain subsystem that contains my motors, but my method within the DriveTrain class is only to set the voltage of those motors. Note how there is no logic present within the DriveTrain subsystem. In my Drive command, I have logic to determine when I should use my voltage method. This is a really good example of a Command and Subsystem working together.
* **Note**    
    It can be confusing at first. This section is just to help you understand everything, but if you need help just ask either Dave, Aidan or Conor. Here's a quick review: First, the ```Main``` class is activated by the natural Java starter method (main). You usually write in this method when learning in AP CS A. Then, the ```Main``` class activates the ```Robot``` class. The ```Robot``` class begins to loop through methods that fit the state of the robot. Also in the RobotInit method (the first method that's called), it initializes the ```RobotContainer``` class. The ```RobotContainer``` only runs once, starting with the constructor and then going through the methods. You can track where it goes and when by following the constructor and seeing which method is called within it.   
___
#### DriverStation (DS) 
