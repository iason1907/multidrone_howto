# Multidrone software

How to install ROS, build multidrone softwarguide

## Install ROS Kinetic for Ubuntu 16.04

Note: For other Ubuntu distros install the appropriate ROS distro

**Open Terminal (Ctrl+Alt+T) and execute the following commands:**

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

## Install build prerequisites

* **Install git and cmake**
```bash
sudo apt-get install git
sudo apt-get install cmake
```

* **Install libusbp**

```bash
mkdir ~/gen_ws
cd ~/gen_ws
git clone https://github.com/pololu/libusbp.git
cd libusbp && mkdir build && cd build
cmake ..
make 
sudo make install
```
* **Install gstreamer**
```bash
sudo apt-get install gstreamer1.0-tools gstreamer1.0-gev
sudo apt-get install libgstreamer-plugins-base1.0-dev libgstreamer-plugins-good1.0-dev
```

* **Install more ros packages**
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

## Prerequisites for running visual analysis

### Install caffe
* Clone caffe master from github
`git clone http://github.com/BVLC/caffe`
* Install dependencies
```bash
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo apt-get install libopenblas-dev
```
* Create Makefile.config
```bash
cd caffe
cp Makefile.config.example Makefile.config
```
  * For CPU only, uncomment line 8:
 ```bash
 # CPU_ONLY := 1
 -->
 CPU_ONLY := 1
 ```
  * For GPU, you must have nvidia drivers & CUDA & optionally cudnn installed, then uncomment line 5 if you want to use cudnn and leave line 8 commented:
 ```bash
 # USE_CUDNN := 1
 -->
 USE_CUDNN := 1
 ...
 # CPU_ONLY := 1
 ```
  * add hdf5 lib & include paths to INCLUDE_DIRS and LIBRARY_DIRS (lines 90-91):
 ```bash
 INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
 LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib/serial/
 -->
 INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/
 LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial/
 ```
 NOTE: For the TX2, the library dir is `/usr/lib/aarch64-linux-gnu/hdf5/serial/` instead of `/usr/lib/x86_64-linux-gnu/hdf5/serial/`
  * If you use OpenCV version 3+, uncomment line 23:
 ```bash
 # OPENCV_VERSION := 3
 -->
 OPENCV_VERSION := 3
 ```
* Make core:
`make -j4`
NOTE: The option '-j4' will use more threads, and the make command will finish faster.
NOTE: If you have anaconda installed, it is better to remove its paths from the LD_LIBRARY_PATH and PATH env variables both for the installation of caffe and to run the nodes. Otherwise errors with boost and opencv may occur.
* Install python dependencies:
`pip install -r python/requirements.txt`
* Make pycaffe:
`make pycaffe`
* Make caffe.pb.h:
`protoc src/caffe/proto/caffe.proto --cpp_out=.`
`mkdir include/caffe/proto`
`mv src/caffe/proto/caffe.pb.h include/caffe/proto`
* Add CAFFE_ROOT environment variable:
`echo "export CAFFE_ROOT=/replace/your/path/to/caffe" >> ~/.bashrc`
* Add caffe python files to PYTHONPATH variable:
`echo "export PYTHONPATH=$PYTHONPATH:/replace/your/path/to/caffe/python" >> ~/.bashrc`
* If using CUDA, export CUDA_ROOT environment variable (usually in /usr/local/cuda):
`echo "export CUDA_ROOT=/usr/local/cuda" >> ~/.bashrc`

Caffe is ready to use!

### Install TensorFlow
* For GPU (you must have nvidia drivers & CUDA & optionally cudnn installed):
```bash
pip install tensorflow-gpu
```
* For CPU:
```bash
pip install tensorflow
```

* For the TX2, GPU:
```bash
pip install --extra-index-url https://developer.download.nvidia.com/compute/redist/jp33 tensorflow-gpu
```

### Install darknet
Darknet will be build from sources upon catkin build darknet_ros. It will automatically detect whether you have CUDA or not.
To actually run darknet_ros, you need to download the models from https://drive.google.com/open?id=17gt-r9H5dyzmUyt-KH8lknDatJnv0bfu and place them into the darknet_ros/darknet_ros/models/weights directory.

To run ssd_tensorflow_ros, you need to download the modells from https://drive.google.com/open?id=1H2NrS3In8fY8Qr_WAXKQmdV7_aXpcWQU and place them into the ssd_tensorflow_ros/models directory.


