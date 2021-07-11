# Formula Student Driverless Simulation

![Banner](docs/images/banner.png)

## Overview

This is a Formula Student Driverless Simulator (FSDS).
FSDS is a community project with the goal to provide an end-to-end simulation for FS Driverless teams. It simulates all commonly used sensors and is compatible with ROS.

On this guide you will setup your computer to run FSDS and perform testing. This includes setting up WSL (Windows Subsystem for Linux), installing ROS and setup FSDS with the ROS bridge.

[FSDS Documentation](https://fs-driverless.github.io/Formula-Student-Driverless-Simulator/)

## Getting Started

To run the simulation smoothly you need quite a fast Windows computer with a modern videocard. 

# WSL2

### Step 1 - Enable the Windows Subsystem for Linux

You must first enable the "Windows Subsystem for Linux" optional feature before installing any Linux distributions on Windows.

Open PowerShell as Administrator and run:

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

### Step 2 - Enable Virtual Machine feature

Before installing WSL 2, you must enable the Virtual Machine Platform optional feature. Your machine will require virtualization capabilities to use this feature.

Open PowerShell as Administrator and run:

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Restart your machine to complete the WSL install and update to WSL 2.

### Step 3 - Download the Linux kernel update package

[WSL2 Linux kernel update package for x64 machines](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

Run the update package downloaded in the previous step. (Double-click to run - you will be prompted for elevated permissions, select ‘yes’ to approve this installation.)

### Step 4 - Set WSL 2 as your default version

Open PowerShell and run this command to set WSL 2 as the default version when installing a new Linux distribution:

```
wsl --set-default-version 2
```

### Step 5 - Install your Linux distribution of choice

Open the Microsoft Store and select [Ubuntu 20.04 LTS](https://www.microsoft.com/store/apps/9n6svws3rx71).

From the distribution's page, select "Get".

The first time you launch a newly installed Linux distribution, a console window will open and you'll be asked to wait for a minute or two for files to de-compress and be stored on your PC. All future launches should take less than a second.

You will then need to [create a user account and password for your new Linux distribution](https://docs.microsoft.com/en-us/windows/wsl/user-support).

## Installing ROS and other depencencies

### Step 1 - Install git

```
sudo apt-get install git
```

### Step 2 - Make python 3 default

```
sudo apt-get install python-is-python3
sudo apt remove python2
sudo apt autoremove --purge
```

### Step 3 - Setup your sources.list

Setup your computer to accept software from packages.ros.org.

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

### Step 4 - Set up your keys

```
sudo apt install curl # if you haven't already installed curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

### Step 5 - Installation

First, make sure your Debian package index is up-to-date:

```
sudo apt update
```

Now install ROS Desktop-Full Install

```
sudo apt install ros-noetic-desktop-full
```

### Step 6 - Environment setup

You must source the setup script in every bash terminal you use ROS in. It can be convenient to automatically source this script every time a new shell is launched. These commands will do that for you.

```
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### Step 7 - Dependencies for building packages

Up to now you have installed what you need to run the core ROS packages. To create and manage your own ROS workspaces, there are various tools and requirements that are distributed separately. For example, [rosinstall](http://wiki.ros.org/rosinstall) is a frequently used command-line tool that enables you to easily download many source trees for ROS packages with one command.

To install this tool and other dependencies for building ROS packages, run:

```
sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential
```

Before you can use many ROS tools, you will need to initialize rosdep. rosdep enables you to easily install system dependencies for source you want to compile and is required to run some core components in ROS. If you have not yet installed rosdep, do so as follows.

```
sudo apt install python3-rosdep
```

With the following, you can initialize rosdep.

```
sudo rosdep init
rosdep update
```

### Step 8 - Create a ROS Workspace

Let's create and build a [catkin workspace](http://wiki.ros.org/catkin/workspaces):

```
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
```

The [catkin_make](http://wiki.ros.org/catkin/commands/catkin_make) command is a convenience tool for working with [catkin workspaces](http://wiki.ros.org/catkin/workspaces). Running it the first time in your workspace, it will create a CMakeLists.txt link in your 'src' folder.

Additionally, if you look in your current directory you should now have a 'build' and 'devel' folder. Inside the 'devel' folder you can see that there are now several setup.*sh files. Sourcing any of these files will overlay this workspace on top of your environment. To understand more about this see the general catkin documentation: catkin. Before continuing source your new setup.*sh file:

```
source devel/setup.bash
```

# FSDS

The simulator will load settings from the file `Formula-Student-Driverless-Simulator/settings.json` in your **home directory**.
This file is required for the simulator to work and contains the sensor configuration of the car.
If you clone the repo you will already have this file in place.
If not, copy-paste the contents of the [settings.json file at the root of this repository](https://github.com/FS-Driverless/Formula-Student-Driverless-Simulator/blob/master/settings.json) into the `~/Formula-Student-Driverless-Simulator`.

Go to [releases](https://github.com/FS-Driverless/Formula-Student-Driverless-Simulator/releases) and download the latest one.
Unzip it to anywhere on your computer and launch FSDS.exe.
A window with a car should popup!
Try driving the car around using the arrowkeys.
If you get a black screen with some buttons, make sure the folder with the binary is in your user folder (`C:\Users\username\Formula-Student-Driverless-Simulator`)

## Connecting to the simulator with ROS

The ROS bridge will publish the sensordata from the simulator into ROS topics.
Your autonomous system will be able to publish car-control messages which the ROS bridge will send to the simulator.

### Step 1 - Requirements

The ROS bridge requires [ROS Noetic to be installed](software-install-instructions.md), as well as the following dependencies:
```
sudo apt-get install ros-noetic-tf2-geometry-msgs python3-catkin-tools ros-noetic-rqt-multiplot ros-noetic-joy ros-noetic-cv-bridge ros-noetic-image-transport libyaml-cpp-dev libcurl4-openssl-dev
```

### Step 2 - Cloning the repository

Clone the repo into your home directory:

```
git clone https://github.com/FS-Driverless/Formula-Student-Driverless-Simulator.git --recurse-submodules
```

**THE REPO HAS TO BE CLONED IN THE HOME DIRECTORY!**. So the repo location should be `$HOME/Formula-Student-Driverless-Simulator`.

Now, checkout the version equal to the simulator. 
```
git checkout tags/v2.0.0
```

### Step 3 - Preparing AirLib

AirLib is the shared code between the ROS wrapper and the AirSim Unreal Engine plugin.
We need to stage the source before we can compile it together with the wrapper.

Download the nessesary libraries required to compile AirLib.

```
AirSim/setup.sh
```

### Step 4 - Building the workspace

```
cd ros
catkin init
catkin build
```

### Step 5 - Windows firewall configuration

Open TCP port 41451 inbound (public) on Windows firewall.

### Step 6 - Launching the ros bridge

The ROS bridge consists of a few nodes to achieve the highest performance and keep the codebase clean.
Everything can be launched using the `fsds_ros_bridge.launch` launchfile.
```
cd ros
source devel/setup.bash
roslaunch fsds_ros_bridge fsds_ros_bridge.launch rviz:=true host:=$(awk '/nameserver / {print $2; exit}' /etc/resolv.conf 2>/dev/null)
```

The ROS bridge will read the settings from `~/Formula-Student-Driverless-Simulator/settings.json`.
Make sure this is the same configuration file as the simulator uses.

[Read all about configuring the ROS bridge here.](ros-bridge.md)

## Connecting your autonomous system

The ROS bridge of this simulator had to make use of several custom msgs (for control commands, the groundtruth track, etc). 
These messages are defined in a ROS package called `fs_msgs` which is located in a separate, light [repository](https://github.com/FS-Driverless/fs_msgs). 
To implement publishers and subscibers for these messages types in your autonomous pipeline, you will have to add the `fs_msgs` repository as a submodule in your codebase (inside de `src` directory of an existing **catkin workspace** as done in this repository) or clone it and build it somewhere else in your system.

Now, all that is left to do is subscribe to the following topics to receive sensordata

- `/fsds/gps`
- `/fsds/imu`
- `/fsds/camera/CAMERA_NAME`
- `/fsds/camera/CAMERA_NAME/camera_info`
- `/fsds/lidar/LIDAR_NAME`
- `/fsds/testing_only/odom`
- `/fsds/testing_only/track`

and publish to the following topic `/fsds/control_command` to publish the vehicle control setpoints.

## Multiple computers
The ROS bridge should preferable run on the same computer as the simulator to ensure low latency and high bandwidth.
However, it is possible to run the ROS bridge on a different computer than the simulator.
To get this to work you should use the `host` argument in the `fsds_ros_bridge.launch` file.
The ROS bridge will connect to the simulator on port 41451.
