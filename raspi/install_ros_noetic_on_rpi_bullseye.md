# Install ROS Noetic on Raspberry Pi Bullseye OS

ROS Noetic is not officially supported on Raspbian Bullseye OS, but installation is possible from source. This tutorial is modified from [Install ROS from source](http://wiki.ros.org/Installation/Source) to address some issues that might arise during installation on the Raspbian Bullseye OS specifically.

## Step 1: Install dependencies
```sh
sudo apt-get install python3-rosdep python3-rosinstall-generator python3-vcstools build-essential
sudo pip3 install vcstool
```

## Step 2: Get source list
```sh
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo rm /etc/ros/rosdep/sources.list.d/20-default.list
```

## Step 3: Install rosdep
```sh
sudo rosdep init
# This step is needed because the default OS version on Raspbian Bullseye won't work
# Need to point out that we're using "debian"
export ROS_OS_OVERRIDE="debian:bullseye"
rosdep update
```

## Step 4: Create a catkin workspace for ROS
### Step 4.1: Create workspace and generate source
```sh
mkdir -p ~/ros_catkin_ws/src/; cd ros_catkin_ws/
rosinstall_generator perception --rosdistro noetic --deps --tar > noetic-perception.rosinstall
vcs import --input noetic-perception.rosinstall ./src
```

### Step 4.2: Resolve dependencies
```sh
# Note that the os is overridden
# Also the "-r" flag will skip packages that it fails to install
rosdep install --from-paths ./src --ignore-packages-from-source --rosdistro noetic -y --os=debian:bullseye -r
```

### Step 4.3: Install missing packages
```sh
# Some packages were skipped in step 4.2. Install them separately here.
sudo pip3 install catkin_pkg
sudo pip3 install catkin_pkg_modules
sudo pip3 install rosdep
sudo -H apt-get install -y python3-coverage
```

### Step 4.4: Build the ROS catkin workspace
```sh
./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release -DPYTHON_EXECUTABLE=/usr/bin/python3
```

### Step 4.5: Source the workspace
```sh
source ~/ros_catkin_ws/install_isolated/setup.bash
```

