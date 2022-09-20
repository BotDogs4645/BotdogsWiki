#### Where to start
To start, go and read the official [WPILib Wiki](https://docs.wpilib.org/en/stable/docs/software/what-is-wpilib.html) intro page that overviews general stuff about the program.   
WPILib is a collection of extensions in VS Code that allow us the best way to write code for the robot.  
___
#### Installing at home
1. Visit the [GitHub Page for WPILib](https://github.com/wpilibsuite/allwpilib/releases/tag/v2022.4.1).
2. Scroll to the bottom and install the correct download for your OS.
3. Open the ISO or equivalent and run the WPILib_Installer.exe or equivalent.
4. Complete the download with your preferred options, but choose "Download for this Computer Only".
5. To access, open the Start Menu and search for 2022 WPILib VS Code.   

> *NOTE:* WPILib installation on school computers should be done preferably by the programming or electronics leads.
___
#### Creating a Project
1. Open "2022 WIPLib VS Code" from either the Start Menu or the icon on your desktop screen.
2. Use *CTRL+SHIFT+P*. This opens a start menu within VS Code that is extremely useful. We use it a lot.
3. Type "Create a new Project" and select the WPILib option (has WPILib: next to it.)
4. A new screen has popped up. For the options, choose:
     * template
     * java
     * Command Robot
5. Select a directory; it can be any folder.
6. Choose a project name (it can be named anything)
7. Type in our team number, 4645. 
8. Create a project.
___
#### Parts of a command robot
* **Constants**    
    The constants file is full of values and objects that do not change. Values like wheel sizes or max velocities of parts should be stored here. All of the values should start with the "Constant" modifier (```public final static```), which allows us to access it anywhere and make sure it doesn't change.
* **Main**    
    This is where the Robot starts. We usually don't touch it at all.
* **Robot**    
     Some fundamental, crucial pieces of the robot are stored here. Like an Arduino or Argon, a forever while loop constantly runs on the robot's hardware. This while loop always calls on these functions in Robot every 20 ms. They all have self-explanatory names such as ```RobotInit``` (runs only once, at the initialization of the robot) or ```RobotPeriodic``` (always runs at the 20 ms time frame). A minor fraction of our code will be written here, so it's probably best not to mess with it unless you know what you're doing.
* **RobotContainer**    
    The file that holds most of our declarations of objects and subsystems. It can contain a part declaration, subsystem declaration, command setup, and command binding to controllers. Method names inside are pretty self-explanatory.     
* **Subsystems**    
    Subsystems group together different parts. For example, the DriveTrain subsystem usually has four motors inside of it. The DriveTrain subsystem allows us to group parts and concisely manipulate them. There is no logic in the subsystems, just methods to gather and set up information about the details.
* **Commands**    
    Commands are closely related to subsystems. Commands usually manipulate the subsystems by running their methods. Subsystems generally contain methods that allow you to control the individual parts. The commands contain the logic. For example, I could have the DriveTrain subsystem that includes my motors, but my method within the DriveTrain class is only to set the voltage of those motors. Note how there is no logic present within the DriveTrain subsystem. In my Drive command, I have logic to determine when I should use my voltage method. This is a perfect example of a Command and Subsystem working together.
* **A quick review:** To start, the ```Main``` class is activated by the natural Java starter method (main). You usually write in this method when learning in AP CS A. Then, the ```Main``` class activates the ```Robot``` class. The ```Robot``` class begins to loop through methods that fit the robot's state. Also, the RobotInit method (the first method that's called) initializes the ```RobotContainer``` class. The ```RobotContainer``` only runs once, starting with the constructor and then through the methods. You can track where it goes and when by following the constructor and seeing which method is called within it.   
___
#### Building & Deploying 
*Building* a program is interpreting and compiling the code. The code is checked and then approved by the interpreter. This catches all syntax errors but no logic errors. You can build a program at home by using the *CTRL+SHIFT+P* window and then typing Build and choosing the "WPILib:" option.    

*Deploying* a program means deploying the code to the Robot. This is the only way to detect logic errors other than through the simulator. Deploying builds the program first, so any syntax errors not highlighted will be seen. You can deploy a program using the *CTRL+SHIFT+P* window and then typing "Deploy Robot Code" while connected to the Robot. Refer to the RoboRIO section for information about debug messages.
___
#### Driver Station (DS) 
Driver Station is a utility that allows us to upload code, control the states of the robot, and receive analytics.    
To open DS, go to your Start Menu and type Driver Station.    
To review the DS sections and specific overview, please visit this guide's [DS page](https://github.com/Aidan747/FRC-Offseason-2022/wiki/Tools#driverstation-ds).
___
#### roboRIO
The roboRIO is this grey box on the robot. It contains our code that runs on the robot. It has a bunch of I/O, such as an I2C port, PWM, and analog. It is recommended that the roboRIO is handled by electronics, but programming should also be informed about it. CORE team members might be asked to flash the roboRIO or radios.    

##### roboRIO lights
Programming is all about giving instructions and receiving a response. A basic form of that is in the form of the lights on the roboRIO.    
We look at DS for specific errors; however, if something much more fundamental goes wrong, consult the roboRIO lights.    
    
Here are some to look out for:    
* Assume ${\color{green}\text{green}}$ is good.
* **Power**: 
    * ${\color{orange}\text{Amber}}$: Brownout    
    Restart the robot and replace the battery. If the problem persists, consider high power consumption parts on the robot. If it continues, consult a lead.
    * ${\color{red}\text{Red}}$: Power fault    
    Disconnect the circuit breaker on the robot and consult a lead.
* **Status**:
    The status light is special. It activates only while booting. If it stays on for too long (greater than a minute) or is blinking AT ALL:
    1. Leave it ON.
    2. Tell a coach or lead immediately.
* **Comm**:
    * ${\color{grey}\text{Grey}}$: No Communications    
    There is a lot that could be wrong. Check cables, connections, and firmware on both the RIO and laptop.
    * ${\color{red}\text{Red Solid}}$: No Code    
    Deploy code (instructions in Building & Deploying)
    * ${\color{red}\text{Red Blinking}}$: E-stop triggered    
    Emergency stop or E-stop triggers when the robot decides that conditions or instructions are too dangerous. Using too much of the battery can cause this. Most times, someone might've pressed space on the laptop while in DS, which automatically triggers the E-Stop mode. Redeploy to get rid of it.
* **Mode**:
    * Each color depicts what mode the robots are in, but there is not necessarily a bad mode light.
    * ${\color{orange}\text{Orange}}$: auto
    * ${\color{green}\text{Green}}$: teleop
    * ${\color{red}\text{Red}}$: test
___
#### Vendors
Vendors create free software that pairs with their hardware. For example, a Falcon 500 motor- which we constantly use in FRC- already has code for it. We NEVER want to remake code that is already written. Therefore, we use vendor libraries that give us already created code for their product. Some specific vendor libraries that you might want to use:
* CTRE (Cross the Road Electronics)   
    ```https://maven.ctr-electronics.com/release/com/ctre/phoenix/Phoenix-frc2022-latest.json```
* Kauai Labs    
    ```https://www.kauailabs.com/dist/frc/2022/navx_frc.json```
* REV Robotics    
    ```https://software-metadata.revrobotics.com/REVLib.json```    

Usually, you'll want to use all three of these vendors. Sometimes one of our parts may use a different vendor; make sure to look for a library or similar before trying to program with that item.
    
##### Adding a vendor to WPILib
Follow these steps to add a vendor to WPILib:
1. Get the vendor online install link (like the ones in the previous section).
2. Use *CTRL+SHIFT+P* to access the WPILib Start Menu.
3. Type "Manage Vendor Libraries."
4. Choose "Install new libraries (online)."
5. Paste your online install link.
6. Say yes to the pop-up or rebuild program.    

Our repo usually has these automatically installed, but for personal projects, this is required.
___
#### Shuffleboard
Shuffleboard is the primary way to get I/O from the robot. We can put different tabs onto Shuffleboard, which can correlate to other subsystems. We can also have a general tab for most things we'd need to see during the competition. Shuffleboard has some pretty easy and rather tricky setups, so refer to the Shuffleboard in Coding Conventions. 