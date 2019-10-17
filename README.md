# software_integration

Integration repository for all relevant software pieces/submodules.
This project normally runs on Nvidia JetsonTX2 Dev-Board on the cars (already installed).
If you would like to run some of the packages on your PC, please refer to the Build section.


## Questions/TODOs in/about submodules/directories

2. How to run simulation? Extend Readme for the simulation environments.
3. Readme low_level_steering_controller
4. Readme morse_teleop
5. Readme rc_msgs
6. arduino has TODO in Readme(Ege knows it better)
7. jetsonTX2Power TODOS in Readme 
8. Morse basicspeed.py is missing code docu, currently mostly boilerplate docu.
9. TODO add the end in all of your code either document why codes are commented or remove commented code.
10. TODO all of your packages require a README that exlains what it is
11. low_level_speed_controller fails during compilation.


## Structure

* software_integration

    * buildLibrealsense2TX(submodule) : Custom package to install Librealsens to TX2

    * Firmwares: Firmwares for different environments

        * arduino : Arduino scripts for interfacing the remote receiver

        * FourWheelCarConfigurations(VESC): VESC Configurations

        * SixWheelCar_Firmware: MCU Codes and compiled HEX files

    * JetsonPower: Module and user-space application for integrating LiPo battery readings into Linux 

* morse: Morse simulation environments
    * fourwd: Environment for the 4WD car 
    * sixwd: Environment for the 6WD car

* Six_Wheeled_Motor_Controller: The files in the CD sent with Motor Controller

* src : All catkin based packages

    * ackermann_rc: Interface between an RC receiver connected to arduino and ROS

    * conde (3rd party submodule): **[optional]** Simple lane following Autonomous Driving Simulator for the Portuguese Robotics Open

    * gscam(3rd party submodule): **TODO where is this used?** Broadcasting any GStreamer camera source as ROS image

    * hector_slam(3rd party submodule): 2D Lidar SLAM package that we use

    * imu_tools(custom submodule): Contains IMU filters 				

    * low_level_speed_controller: Speed Controllers 

    * low_level_starter: The launch file to easily start six wheeled platform

    * low_level_steering_controller : Steering Controller 

    * morse_teleop : **TODO** 

    * odometry_agent : Configurations of sensor integrations

    * pid(3rd party submodule): General use PID package

    * pose_follower: **TODO**

    * racecar(3rd party submodule): **TODO**

    * racecar_gazebo(3rd party submodule): **TODO**

    * racecar_simulator(3rd party submodule): **TODO**

    * rc_msgs: **TODO**

    * rf2o_laser_odometry(3rd party submodule): The package that publishes odometry messages from Hector Slam

    * robot_localization(3rd party submodule): The package that contains EKF integrations that we use

    * rplidar_ros(3rd party submodule): ROS package of Laser Scanner

    * rs_cam_and_imu_filter_launch: Launch file to start imu filter and sensor

    * serial_6w: Serial Communication node to communicate with motor controller

    * sixwd_msgs : Custom message to interface six wheeled car's Controller

    * six_wheel_odom: The package that publishes odometry by six wheeled car's motor controllers

    * vesc(custom submodule): VESC interface for ROS and odometry 


## Clone and Build

First, create your self a SSH key to access the repository. To do so click to your avatar at the top right then click Settings after that choose SSH keys section at the right of the screen. There you will see a tutorial to generate a SSH key provided by Git-Lab.

After that you will be able to clone the repository 

`git clone git@gitlab-edu.aot.tu-berlin.de:small_autonomous_driving/software_integration.git `

Change to our development branch

`git checkout patch-1`

Then also get submodules

`git submodule update --init --recursive`


**TODO Ege:  This should also reflect the different environments such as Arduino, ROS, Linux Kernel modules, AVR for 6WD motorcontoller ...**

There are several environments the project consists of.

1. ROS/catkin: Under 'src' folder we have many integrations for ROS. To properly install ROS please check https://wiki.ros.org/ROS/Tutorials/InstallingandConfiguringROSEnvironment link. Please choose your version properly Jetson uses Ubuntu 18.04 and therefore the Melodic version.
2. Arduino: **TODO**
3. Linux Kernel: **TODO**
4. AVR: Note that to code the MCU's on Motor Controller of six wheeled platform we needed to change the codes on the motor controller. To do so I strongly recommend Atmel Studio. There you can write your code and at the build menu you can compile them to .hex format. You can also upload these codes to the MCU's by using the Device Programming Menu. Check Firmwares/SixWheelCar_Firmware folder for additional information.
5. VESC Tool: VESC tool is the GUI of the VESC. There you can change motor controlling parameters easily. Check Firmwares/FourWheeledCarConfigurations(Vesc) Folder for additional information.


### Installation

#### Dependendies

Automatic resolving of ROS dependencies

```bash
rosdep install --from-paths src --ignore-src -r -y
```

To install the ros project, some packages that might be missing (may not come with full desktop installation):
- Ackermann (`sudo apt install ros-<version>-ackermann*`)
- gstreamer-1.0
- teleop_twist_joy (`sudo apt install ros-<version>-teleop-twist-joy`)
- teleop_twist_keyboard (`sudo apt install ros-<version>-teleop-twist-keyboard`)
- geographic-msgs (`sudo apt install ros-<version>-geographic-msgs`)
- serialConfig (`sudo apt install ros-<version>-serial*`)
- opencv

#### Installation

```bash
catkin_make
```

## Run

For executing different software setups please refer to the linked platform specific Readmes.

1. **[4WD platform](RUNNING_FourWd.md)**
1. **[6WD platform](RUNNING_SixWD.md)**  


## Sensors Setup

In our platforms we are using following sensors

- Wheel Encoder:

  Six Wheeled platform has its own wheel encoders for each wheels but they are only one phase encoders. Therefore they can't sense rotation direction, we solved this issue at odometry node assuming wheels never slides and rotates in commanded direction. Four wheeled system uses ERPM readings from BLDC motor. We also integrated an additional wheel encoder on it but not integrated it to ROS environment.

- Laser Scanner:

  Both cars have a RPLidar A3 laser scanner.

- IMU (Integrated on Camera):

  Intel D435i cameras have integrated IMU sensors on them. However, this IMU features only accelerometer and gyroscope and is missing a magnetometer.

- Camera: Front camera is the Intel D435i RGBD camera. Backcamera is the onboard MP CSI camera module on the Jetson (Omnivision OV5693)


## Open Issues and improvement Ideas
1. For VESC we applied Encoder that we can read it from VESC Firmware. To more accurate speed readings, it should also be implemented for ROS. Note that VESC only reads
 encoder as rotor position. Therefore, two steps are required. First, add position reading to VESC_Driver node. Secondly, Convert it into speed.
2. Motor controller for six wheeled platform' motor controller node (serial_6w) needs a launch file to easily change parameters especially USB device name.
3. 6 Wheeled platform's encoder readings is not that reliable. For now we are using rf2o node for odometry. Wheel odometry data may be improved 
4. 6 Wheeled platform's one motor has damaged. It should be changed. Please check the issue.
5. PID performances can be improved to see the interface please look interface section.
7. Adding additional visual odometry to the cars.

#FAQ

**TODO** if you have, please collect solutions for common pitfalls (maybe things you experienced yourself)**
