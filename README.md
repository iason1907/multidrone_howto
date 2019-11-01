# Multidrone software

How to install ROS and build multidrone software guide

## Install ROS Kinetic for Ubuntu 16.04

Note: For other Ubuntu distros install the appropriate ROS distro

**Open Terminal (Ctrl+Alt+T) and execute the following commands:

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
curl -sSL 'http://keyserver.ubuntu.com/pks/lookup?op=get&search=0xC1CF6E31E6BADE8868B172B4F42ED6FBAB17C654' | sudo apt-key add -
sudo apt-get update
sudo apt-get install ros-kinetic-dekstop-full
sudo rosdep init
rosdep update
echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
sudo apt-get install cmake python-catkin-pkg python-empy python-nose python-setuptools libgtest-dev build-essential
sudo apt-get install python-catkin-tools python-rosinstall-generator -y
```

## Install prerequisites

* **Install libusbp 

```bash
mkdir ~/gen_ws
cd ~/gen_ws
git clone https://github.com/pololu/libusbp.git
cd libusbp && mkdir build && cd build
cmake ..
make 
sudo make install
```
* **Install gstreamer
```bash
sudo apt-get install gstreamer1.0-tools gstreamer1.0-gev
sudo apt-get install libgstreamer-plugins-base1.0-dev libgstreamer-plugins-good1.0-dev
```

* **Install more ros packages
```bash
sudo apt-get install ros-kinetic-mavros*
sudo apt-get install ros-kinetic-geodesy
sudo geographiclib-get-geoid egm96-5
sudo apt-get install ros-kinetic-gscam
```

## Create Multidrone workspace

```bash
mkdir ~/multidrone_ws
cd ~/multidrone_ws
mkdir src
```

## Clone/Get multidrone repo

* Clone
```bash
cd ~/multidrone_ws/src/
git clone https://url.to.multidrone.git/full
```

* Copy

copy and paste multidrone repo to ~/multidrone_ws/src/ 

## Clone GRVC UAL

```bash
cd ~/multidrone_ws/src/multidrone_full
git clone https://github.com/grvcTeam/grvc-ual.git
```

## Build

```bash
~/multidrone_ws/src/multidrone_full/ground/ground_visual_analysis/clipper/make_libs.sh
catkin build
```
