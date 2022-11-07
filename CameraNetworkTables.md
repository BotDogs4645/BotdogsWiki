## CameraNetworkTables
CameraNetworkTables are our communicator between roboRIO, laptop and the Jetson-attached camera. The Jetson is, for the CameraNetworkTable's intents, an image processing system. It is integrated onto the robot. CameraNetworkTables allow us to be able to find, declare and control IP Cameras connected to the Jetson via networktable. There are a few settings you are able to change with CameraNetworkTables.
___
#### Example
Here's an example of a Shuffleboard layout containing some sliders, viewable settings and some request buttons.    

![image](https://user-images.githubusercontent.com/93739747/199523982-3d16b09b-10fb-4c83-aa5e-6c728b287026.png)

These values are then all ported over to the NetworkTables section for a specific camera, shown here.   

![image](https://user-images.githubusercontent.com/93739747/199524636-bd8dba99-0a0e-4b02-90e2-ca10d0545aba.png)    

These values are read by the Jetson, ported over to the camera as instructions, and then we receive them in the Jetson subfolder.