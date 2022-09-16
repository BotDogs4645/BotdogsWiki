#### What are tools?    
Tools are software from the NI or Vendor libraries that allow us to interact with the robot outside of code. This can be useful for identifying devices, testing PID values or testing configs. Usually, our tools are our best friends because they provide crucial information that lets us fill in blanks.
___
## Essential Tools
### Driver Station (DS)
Driver Station is the biggest communicator between the drivers and the robot. It provides statuses, error logs and information regarding the control systems that are in use on the robot. To open Driver Station, type "FRC Driver Station" into your search bar after installing the [NI Game Tools](https://www.ni.com/en-us/support/downloads/drivers/download.frc-game-tools.html). On the left of DS is the tab section, to the right of that are the robot status lights and one more over is the error log.
1. **Operation**     
   This tab is mainly for selecting what mode you want to use. The options are TeleOp, Autonomous, Practice and Test. The main ones you'll use are TeleOp and autonomous. Also, on this tab there are the enable and disable options. To make the robot move or to give it inputs, you have to enable. Warn people when you enable. Disable makes the robot stop in its tracks. Pressing spacebar activates E-Stop, which stops all motor inputs and won't let you reenable the robot. You need to repush the code for it to work again.    
2. **Diagnostics**    
   This tab is used to see what connection you have to the robot and to diagnose what's wrong with it. Most of the lights are self-explanatory. If you can't seem to connect to the robot, check if the Robot Radio light is illuminated. If it is, then it's a problem with the radio. If it isn't, then it's most likely a problem with the roboRIO. You can also reboot the roboRIO and restart the robot's code from this tab. Make sure the firewall on your laptop is down if you see the firewall light illuminated.
3. **Setup**    
   This tab allows us to change some settings with DS. The main one you'll want to change is the Team # for your computer. If your roboRIO and Driver Station do not have the team number, you will not be able to have communications. Change the Dashboard type to Shuffleboard. We don't use SmartDashboard anymore. We don't usually use the test timings, but it will be useful for later on when we want to fully test the robot. 
4. **USB Devices**    
   This tab allows us to see what devices are connected to the laptop. When we declare a ```new XboxController(0);```, that 0 refers to the USB Devices tab. You can see what index the device is and move it around to match what you have in code. You can also click on controllers and see which button refers to which number, which refers to the number in a ```new JoystickButton(num);```.
5. **Power & CAN**    
   Provides information about the power draws and CAN bus signals being produced on the robot. Good for debugging big problems.
### Shuffleboard
Shuffleboard is basically a huge grid that lets us put widgets onto it. These widgets can show any amount of information that we want. The primary things that you'll use shuffleboard with are as follows:
1. **Variable Supplier**    
    Variable supplier basically takes the output of a method and then puts it into a shuffleboard tab.
    ```java
    ShuffleboardTab tab = Shuffleboard.getTab("tab"); // gets the tab named tab. if it doesn't exist, it creates it.
    tab.addNumber("name of widget", () -> methodThatReturnsNumber()); // the variable type has to match the add method (tab.add<VariableType>)
    ```
2. **Command or misc**    
    If what you what you want to give Shuffleboard is not a primitive type, then you need to just have add(). Putting commands in here shows you the status of its scheduling. Putting subsystems shows you the default command and the current scheduled command.
    ```java
    ShuffleboardTab tab = Shuffleboard.getTab("tab");
    tab.add("command", myCommand); // adds myCommand and its scheduling info onto that tab.
    ```
___
## Non-Essential Tools
### Phoenix Tuner
Phoenix Tuner is used to test CTRE devices, which usually consist of Talon SRXs and Falcon 500s (Talon FXs). It's not essential that you have Phoenix Tuner unless you are updating the firmware on some CTRE devices. Check the updating devices tab for more information about how to do that. Phoenix tuner is installed on all computers with labels on the lid. To use Phoenix Tuner, make sure you connect your USB-A port to the roboRIO's USB-B port. Open Phoenix Tuner on a laptop in the lab, and under the Tuner Setup page press "Run Temporary Diagnostics Server." You can view all the can devices on the CAN Devices page, as well as click a button to light them up so you can identify them. When you select a device, you can go to the Control page to run a specific CAN ID. Make sure to have Driver Station open because to run a motor with the Control page you must: enable the robot on DS and click the button that appears at the bottom. We don't usually use any other controls on Phoenix Tuner.

### REV Hardware Client
The REV Hardware Client is essentially Phoenix Tuner but for REV Products (SparkMAXs, NEOs). The typical use case will be for a SPARKMax. To use REV Hardware Client, make sure you have a laptop with it installed. It's likely it isn't on most computers, so if you need to install go to [here](https://docs.revrobotics.com/rev-hardware-client/) and download the latest. Connectivity is a bit different for REV. First, you'll need to find a special orange cable which we usually have at least one of in the Electronics cabinet. Make sure to check the Programming shelf near the Robot Bay as well for one. Plug that into one of the SparkMAXs on the robot and all the different connectivity options should appear. You can make it blink, but not much else without removing it from the roboRIO which the leads probably won't appreciate.

### GitHub Desktop
For beginners, you should probably use GitHub Desktop. More information about that in the GitHub section. There is also a mini section about doing everything from the command line, so if you are up for the task, you are able to go to the next level.

