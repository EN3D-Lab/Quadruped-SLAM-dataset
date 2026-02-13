# Quadruped-SLAM-dataset
A quadruped SLAM dataset covering diverse locomotion behaviors in complex environments.

The corrsponding paper is published in 2026 ISPRS Journal of Photogrammetry and Remote Sensing, "[Gait-Aware Quadruped 3D Mapping in Challenging Environment with Complex Terrain](https://www.sciencedirect.com/science/article/abs/pii/S0924271626000614)".

Thank you for citing our paper.
```
@article{zhao2026gaitaware,
  title   = {Gait-Aware Quadruped 3D Mapping in Challenging Environments with Complex Terrain},
  author  = {Zhao, Xinge and Zhang, Xing and Ni, Yiqing and Li, Qingquan and Zeng, Wentao and Feng, Haixia and Zhou, Yibo},
  journal = {ISPRS Journal of Photogrammetry and Remote Sensing},
  year    = {2026},
  volume  = {233},
  pages   = {664--688},
  issn    = {0924-2716},
  doi     = {10.1016/j.isprsjprs.2026.02.010},
  url     = {https://www.sciencedirect.com/science/article/pii/S0924271626000614}
}

```
## Introduction
The dataset was acquired on the campus of Shenzhen University, using a Unitree Go2 quadruped robot equipped with a Hesai XT16 360° LiDAR and a 360° prism. A Leica TS60 total station was employed to track the prism in real time, providing millimeter-level ground-truth trajectories. To facilitate reproducibility and further research, the rosbag for each sequence is provided in link [Google Drive](https://drive.google.com/drive/folders/1iiKPCqm6VnbNZGPadHuzbj_0v0nYyc33?usp=sharing).

<p align="center">
  <img src="/Figure/Data collection equipment.jpg" width="400">
</p >

In total, 22 data sequences were collected, covering a variety of indoor, outdoor, and semi-outdoor scenes, including uneven natural terrains with abrupt and gradual ascents and descents, high soft grass, deformable forest leaf litter, rigid concrete pavements, slopes, confined staircases with limited geometric features, underground parking lots, narrow corridors, long straight roads, and semi-outdoor environments with moderate structural clutter, shown as follows:

<p align="center">
  <img src="/Figure/Scenarios covered in the dataset.jpg" width="800">
</p >

The quadruped robot states in these datasets encompass lying prone, walking, stair climbing, running, as well as locomotion disturbances and observational instabilities (e.g., foot slippage, loss of balance and recovery). The dataset details are summarized as follows:

<p align="center">
  <img src="/Figure/Descriptions of the dataset.jpg" width="1200">
</p >

## Dataset Information
The dataset includes liDAR, IMU, joint encoders, contact sensors, etc. Taking the sequence **Road00** as an example, the specific data format types are as follows:

```
path:        Road00.bag
version:     2.0
duration:    20:40s (1240s)
start:       May 25 2025 17:47:01.78 (1748166421.78)
end:         May 25 2025 18:07:42.71 (1748167662.71)
size:        12.1 GB
messages:    1872339
compression: none [12411/12411 chunks]
types:       sensor_msgs/Imu               [6a62c6daae103f4ff57a132d6f95cec2]
             sensor_msgs/PointCloud2       [1158d486dd51d683ce2f1be655c3c181]
             unitree_legged_msgs/HighState [470422f324a1822fc8bf6481d8aad1e4]
             unitree_legged_msgs/LowState  [e093969d892d2f4fe05774e959658b57]
topics:      /go2_imu_sensor     620024 msgs    : sensor_msgs/Imu              
             /low_state          620021 msgs    : unitree_legged_msgs/LowState 
             /sport_mode_state   619884 msgs    : unitree_legged_msgs/HighState
             /velodyne_points     12410 msgs    : sensor_msgs/PointCloud2
```
### /go2_imu_sensor : sensor_msgs/Imu
This is the data provided by quadruped robot’s built-in IMU at roughly 500Hz.
### /low_state : unitree_legged_msgs/LowState
```unitree_legged_msgs/LowState``` is the low-level state feedback message provided by Unitree GO2 (originally in ROS 2) at roughly 500 Hz. We ported the interface to ROS 1. Since the original message does not include timestamp information, we added a ```time``` field for data synchronization, following the convention used in [legkilo-dataset](https://github.com/ouguangjun/legkilo-dataset).

The specific content of ```LowState``` is as follows:
```
time stamp
uint8[2] head
uint8 levelFlag
uint8 frameReserve
uint32[2] SN
uint32[2] version
uint16 bandWidth
IMU imu
MotorState[20] motorState
BmsState bms
int16[4] footForce	
int16[4] footForceEst	
uint32 tick						
uint8[40] wirelessRemote 
uint32 reserve
uint32 crc       
```
### /sport_mode_state : unitree_legged_msgs/HighState
```unitree_legged_msgs/HighState``` is the high-level state feedback message from Unitree GO2 (provided via the ROS 2 ```SportModeState``` topic) at roughly 500 Hz. We also ported it to ROS 1 and added a ```time``` field for synchronization.

The specific content of ```HighState``` is as follows:
```
time stamp
uint8[2] head
uint8 levelFlag
uint8 frameReserve
uint32[2] SN
uint32[2] version
uint16 bandWidth
IMU imu
MotorState[20] motorState
BmsState bms
int16[4] footForce
int16[4] footForceEst
uint8 mode
float32 progress
uint8 gaitType		   
float32 footRaiseHeight		  
float32[3] position 
float32 bodyHeight			  
float32[3] velocity 
float32 yawSpeed				   
float32[4] rangeObstacle
Cartesian[4] footPosition2Body 
Cartesian[4] footSpeed2Body	
uint8[40] wirelessRemote
uint32 reserve
uint32 crc
```
### /velodyne_points : sensor_msgs/PointCloud2
This is the data provided by the Hesai XT16 360° LiDAR at roughly 10 Hz. For compatibility with common LiDAR processing pipelines, we convert the data to the Velodyne VLP-16 point format and publish it as ```sensor_msgs/PointCloud2```.

## Sensor Parameter
### leg kinematic parameters
The leg kinematic parameters is defined following the convention used in [legkilo-dataset](https://github.com/ouguangjun/legkilo-dataset).
```
legOffsetX: 0.19527
legOffsetY: 0.046492
legCalfLength: 0.213
legThighLength: 0.213
legThighOffset: 0.088508
```
### External parameters
The extrinsic parameters is defined as the LiDAR's pose (position and rotation matrix) in IMU body frame (i.e. the IMU is the base frame), following the convention used in [Fast-LIO2](https://github.com/hku-mars/FAST_LIO).
```
extrinsic_T: [ 0.171, 0.0 , 0.0908]    
extrinsic_R: [ 0, -1, 0, 
               1, 0, 0, 
               0, 0, 1]
```
## Trajectory Evaluation
We provide ground-truth trajectories measured by a Leica TS60 total station. For SLAM evaluation, you can use [evo](https://github.com/MichaelGrupp/evo) to compute standard trajectory metrics (e.g., absolute pose error) by comparing estimated trajectories against the ground truth.

**Example:**

```
evo_ape tum GT_dataset.txt estimated_trajectory.txt -v -a --plot --plot_mode xyz --t_max_diff 0.1
```
