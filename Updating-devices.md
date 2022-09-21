#### Where do I start?
This is the device update guide. If you need help, find the section with the device you're trying to update. If anything doesn't make sense or continues to have problems, consult a lead.
___
## Firmware Updating Guide
### Falcon 500s
Falcon 500s are the general use motor of the team. In encoder use cases we are using Falcons. In high performance cases we are using Falcons. Therefore, we need to keep their firmware up to date to make sure they run the best they can. Updating firmware also gives us access to more features present.    
Here's a guide to flash CTRE Products in general:
1. To start, make sure you have a Phoenix Tuner laptop which should be any of the main Driver laptops.
   * Make sure that you are using the latest version of Phoenix Tuner.
2. Then, connect to the robot via USB-A to USB-B.
3. Open Phoenix Tuner and run the "Temporary Diagnostic Server."
4. Move over to CAN Devices; in the bottom left, you should see "Field-Upgrade Device Firmware."
5. If you have the latest version of Phoenix Tuner, it should already have the latest firmware selected for install.
6. Make sure to select the "Update all devices with matching type" option. 
7. Update Device.

### Radios
**Don't flash radios.** It's really annoying and you shouldn't have to deal with it.    

### roboRIOs
Although it can be a little annoying, flashing a roboRIO can be really pivotal if you're having constant issues with trying to communicate with the robot. Here are the steps to flashing a roboRIO:
1. Determine if it's a roboRIO or roboRIO 2. If it's a roboRIO 1 then ask a lead to help. If it's a roboRIO 2, then continue these steps.
2. Determine if there is an SD card already in the roboRIO. There is a small port on the roboRIO that reads "SD" within an SD drawing.
3. If there isn't, grab a new one. If there is, eject the miniSD card. If you don't have nails, use a screwdriver but be extremely careful.
4. Now that you have a miniSD card, either retrieve a SD card writer from the Programming box or grab a Driver laptop and plug the miniSD card directly into the laptop's miniSD port.
5. Now, open balenaEtcher. All driver laptops should have this software.
6. Open "roboRIO Imaging Tool" and click the little SD card next to the "Select Image" title. This will open the FRC Images directory.
7. Click on "SD Images," and copy the directory at the top of the page. It should be something like this:    
   ```C:\Program Files (x86)\National Instruments\LabVIEW 2020\project\roboRIO Tool\FRC Images\SD Images```
8. Reopen balenaEtcher and click "Flash from file," and paste the directory you have. Click on the roboRIO2 .img file.
9. Select the SD Card target.
10. Flash!
11. When it's finished, take your SD card and put it into the roboRIO.
12. To finish, plug your USB-A to USB-B into the roboRIO. 
13. Reopen the roboRIO imaging tool and power on the roboRIO.
14. Wait for an option to appear in the section in the top left to select a roboRIO target.
15. Select that target, enter in our team number (4645) into the top right and select the "Edit Startup Settings" option.
16. Select Apply at the bottom.    
    > Do not unplug the cable until it is finished.