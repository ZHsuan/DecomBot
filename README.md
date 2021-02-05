# DecomBot
# Run RTAB-Map with DecomBot bag file
## Setup
ROS installation: http://wiki.ros.org/ROS/Installation  
rtabmap-ros installation: https://github.com/introlab/rtabmap_ros

## Notes
1. Originally recorded bag file is a large file with totally 975s. To test with shorter bag file, you can split bag file at first.
2. Because depth images are not registered in recorded bag file, we use registration.launch to register depth images to RGB images. 
3. Use rtabmap-databaseViewer to regenerate 3D maps.

## Split bag file
### command
1. rosbag filter input.bag output.bag "t.secs >= [t1] and t.secs <= [t2]"
2. rosbag record -a --split --duration=[seconds]

### bag file   
from start time: 1610938942 (  0s)  
  to   end time: 1610939917 (975s)

### example
Split bag file during 250s~550s (pipe):  
rosbag filter [input.bag] [output.bag] "t.secs >= 1610939191 and t.secs <= 1610939492"

## Execute
1. run RTAB-Map  
roslaunch rtabmap_ros rtabmap.launch \
    rtabmap_args:="--delete_db_on_start" \
    depth_topic:=/depth_registered/image_rect \
    rgb_topic:=/camera/color/image_raw \
    camera_info_topic:=/camera/color/camera_info \
    approx_sync:=true \
    use_sim_time:=true

2. run registration   
roslaunch registration.launch

3. run rosbag  
rosbag play --clock [bag file]

## Change 3D reconstruction
rtabmap-databaseViewer ~/.ros/rtabmap.db


# Discussion and problems
1. accumulated errors
Odometry errors will accumulate as time pass by, it may need other odometry methods or seperate a long process to several short ones.  
2. lose tracking
RTAB-Map will lose tracking in sometimes, causing 3D reconstruction stopped.
3. meshing
Meshing results depend on the parameters and methods. It can be improved by change paramters provided by RTAB-Map or use PCL.


# Basics and references
ROS: http://wiki.ros.org/ROS/Tutorials  
RTAB-Map ros: http://wiki.ros.org/rtabmap_ros  
PCL: https://pcl.readthedocs.io/projects/tutorials/en/latest/index.html




