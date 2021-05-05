# WVU Robotics Pollination Hardware Workspace

Use this repository to initialize a workspace containing packages used in
Pollination robot hardware. The goal is requiring no installation steps beyond what
is listed here (assuming a working version of ROS Melodic). At the moment,
the Husky ROS packages are also required to spawn Bramblebee
(`apt install ros-melodic-husky-*`), which will be addressed.

### Prerequisites
See the README files in each of the following repositories, which are pulled by ```wstool``` for a list of required packages.
<https://github.com/wvu-robotics/bramblebee_autonomy>
<https://github.com/wvu-robotics/pollination-drive-base>
<https://github.com/wvu-robotics/pollination-slam>
<https://github.com/wvu-robotics/pollination-manipulation>

### Cloning Workspace and Compiling
To begin:
```shell
wstool init src pollination.rosinstall
```
`wstool init` simply accesses the git URIs provided in the `pollination.rosinstall` file,
cloning their contents to the `src` directory. **If wstool is installed and
fails here, you likely don't have access to the repositories it tries to clone.
Please notify the `#simulation` Slack channel to be added to the
`wvu-robotics/simulation` team.**

The workspace can then be built:
```shell
catkin build
source devel/setup.bash
```

### Running Combined Robot.
Run the following, each in separate terminals (remember to source each):
```
roslaunch combined hardware.launch //hardware interfaces for sensors and actuators
roslaunch j2n6s300_moveit_config bramblebee_arm.launch 
roslaunch combined combined_control.launch
roslaunch combined sensor_fusion.launch
roslaunch bramblebee_navigation move_base_mapless_demo.launch
roslaunch manipulation_mapping flower_mapper.launch
rosrun manipulation_control ee_go_to_pose_action_node
rosrun manipulation_mapping pre_pose_mapping_ros.py
rosrun manipulation_state_machine planning_ga_ros.py
rosrun manipulation_state_machine state_machine_pollinating.py
rosrun manipulation_state_machine approach_action_server.py
rosrun manipulation_pollinator pollinator_control_node
roslaunch combined combined_viz.launch
rosrun autonomy mission_planning_node // Send drive waypoints and commands manipulation sequence at stops
```

### TODO
1. Provide gtsam installation (either prebuilt or source)
2. (?) Provide a small wrapper for starting SLAM navigation so we can work with
less than 6 windows (4 terminals, Gazebo, and RViz).
