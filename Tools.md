#### What are tools?    
Tools are software from the NI or Vendor libraries that allow us to interact with the robot outside of code. These tools can help identify devices, test PID values, or test configs. Usually, our tools are our best friends because they provide crucial information that lets us fill in the blanks.
___
## Essential Tools
### Driver Station (DS)
Driver Station is the most prominent communicator between the drivers and the robot. It provides statuses, error logs, and information regarding the control systems used on the robot. To open Driver Station, type "FRC Driver Station" into your search bar after installing the [NI Game Tools](https://www.ni.com/en-us/support/downloads/drivers/download.frc-game-tools.html). On the left of DS is the tab section; the robot status lights are on the right, and the error log is one further.
1. **Operation**     
   This tab is mainly for selecting what mode you want to use. The options are TeleOp, Autonomous, Practice, and Test. The main ones you'll use are TeleOp and autonomous. Also, on this tab, there are the enable and disable options. To make the robot move or to give it inputs, you have to enable it. Warn people when you enable. Disable makes the robot stop in its tracks. Pressing the spacebar activates E-Stop, which prevents all motor inputs and won't let you reenable the robot. You need to push the code for it to work again.    
2. **Diagnostics**    
   This tab is used to see your connection to the robot and diagnose what's wrong with it. Most of the lights are self-explanatory. If you can't seem to connect to the robot, check if the Robot Radio light is illuminated. If it is, then it's a problem with the radio. If it isn't, it's most likely a problem with the roboRIO. You can reboot the roboRIO and restart the robot's code from this tab. Make sure the firewall on your laptop is down if you see the firewall light illuminated.
3. **Setup**    
   This tab allows us to change some settings with DS. The main one you'll want to change is the Team # for your computer. If your roboRIO and Driver Station do not have the team number, you will not be able to have communications. Change the Dashboard type to Shuffleboard. We don't use SmartDashboard anymore. We don't usually use the test timings, but they will be helpful later when we want to test the robot fully. 
4. **USB Devices**    
   This tab allows us to see what devices are connected to the laptop. When we declare a ```new XboxController(0);```, that 0 refers to the USB Devices tab. You can see the device's index and move it around to match what you have in the code. You can also click on controllers and see which button refers to which number, which refers to the number in a ```new JoystickButton(num);```.
5. **Power & CAN**    
   Provides information about the power draws and CAN bus signals being produced on the robot. Good for debugging big problems.
### Shuffleboard
Shuffleboard is a huge grid that lets us put widgets onto it. These widgets can show any amount of information that we want. The primary things that you'll use Shuffleboard with are as follows:
1. **Variable Supplier**    
    A variable supplier takes a method's output and then puts it into a shuffleboard tab.
    ```java
    ShuffleboardTab tab = Shuffleboard.getTab("tab"); // gets the tab named tab. if it doesn't exist, it creates it.
    tab.addNumber("name of widget", () -> methodThatReturnsNumber()); // the variable type has to match the add method (tab.add<VariableType>)
    ```
2. **Command or misc**    
    If what you want to give Shuffleboard is not a primitive type, then you have to use add(). Putting commands in here shows you the status of its scheduling. Putting subsystems shows you the default command and the currently scheduled command.
    ```java
    ShuffleboardTab tab = Shuffleboard.getTab("tab");
    tab.add("command", myCommand); // adds myCommand and its scheduling info onto that tab.
    ```
___
## Non-Essential Tools
### Phoenix Tuner
Phoenix Tuner tests CTRE devices, which usually consist of Talon SRXs and Falcon 500s (Talon FXs). You don't have to have Phoenix Tuner unless updating the firmware on some CTRE devices. Check the updating devices tab for more information about how to do that. Phoenix tuner is installed on all computers with labels on the lid. To use Phoenix Tuner, connect your USB-A port to the roboRIO's USB-B port. Open Phoenix Tuner on a laptop in the lab, and under the Tuner Setup page, press "Run Temporary Diagnostics Server." You can view all the can devices on the CAN Devices page and click a button to light them up so you can identify them. When you select a device, you can go to the Control page to run a specific CAN ID. Make sure to have Driver Station open because to run a motor with the Control page, you must: enable the robot on DS and click the button at the bottom. We don't usually use any other controls on Phoenix Tuner.

### REV Hardware Client
The REV Hardware Client is essentially Phoenix Tuner but for REV Products (SparkMAXs, NEOs). The typical use case will be for a SPARKMax. To use REV Hardware Client, ensure you have a laptop with it installed. Likely, it isn't on most computers; if you need to install it, go to [here](https://docs.revrobotics.com/rev-hardware-client/) and download the latest. Connectivity is a bit different for REV. First, you'll need to find a special orange cable which we usually have at least one of in the Electronics cabinet. Make sure to check the Programming shelf near the Robot Bay for one. Plug that into one of the SparkMAXs on the robot, and all the different connectivity options should appear. You can make it blink, but not much else without removing it from the roboRIO, which the Leads probably won't appreciate.

### GitHub Desktop
For beginners, you should probably use GitHub Desktop. More information about that is in the GitHub section. There is also a mini section about doing everything from the command line, so if you are up for the task, you can go to the next level.

### MATLAB / Simulink    
MATLAB & Simulink are pieces of software that allow us to import CAD directly into the software and simulate physics. We also use Simulink to simulate entire subsystems. That means that even before we test it out on the field, we can see how game objects react to their environments. Simulink is not required to be known by programmers but is useful nonetheless.