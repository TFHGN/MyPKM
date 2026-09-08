* bool 是否滥用?
* tf树
```
map ──────────────────────→ odom ──────────→ base_footprint ──→ chassis
│                            │                    │                │
│  small_gicp_relocalization │  OdometryPublisher │  tf_static     │  tf_static
│  (动态, 基于点云匹配)        │  (30Hz, 仿真GT)   │  (固定关节)     │
│                            │                    │                ├── gimbal_yaw_odom ──→ gimbal_pitch_odom
│                            │                    │                │        │                  │
│                            │                    │                │        │ tf_static        │ tf_static
│                            │                    │                │        │                  │
│                            │                    │                │        │           gimbal_pitch ──→ speed_monitor
│                            │                    │                │        │                  │
│                            │                    │                │        │                  │ tf_static
│                            │                    │                │        │                  │
│                            │                    │                │        │           front_industrial_camera
│                            │                    │                │        │                  │
│                            │                    │                │        │                  │ tf_static
│                            │                    │                │        │                  │
│                            │                    │                │        │           front_industrial_camera_optical_frame
│                            │                    │                │
│                            │                    │                ├── front_mid360 (LiDAR)
│                            │                    │                ├── front_rplidar_a2
│                            │                    │                ├── front_left_wheel
│                            │                    │                ├── front_right_wheel
│                            │                    │                ├── rear_left_wheel
│                            │                    │                ├── rear_right_wheel
│                            │                    │                └── light_indicator
│                            │                    │
│                            │                    ├── armor_support_frame_0 ── armor_0
│                            │                    ├── armor_support_frame_1 ── armor_1
│                            │                    ├── armor_support_frame_2 ── armor_2
│                            │                    └── armor_support_frame_3 ── armor_3
│                            │
│                            └── [缺失] gimbal_yaw ※ 需 robot_state_publisher 从joint_states 读 gimbal_yaw_joint
│                                                    │
│                                                    │ fake_vel_transform (动态)
│                                                    │
│                                               gimbal_yaw_fake ← costmap robot_base_frame

```
**两处关键动态 TF：**

|变换|发布者|数据源|
|---|---|---|
|`map → odom`|`small_gicp_relocalization`|当前点云 vs 先验 PCD 匹配|
|`odom → base_footprint`|`OdometryPublisher` (robot_base)|Gazebo ground truth odometry|
|`gimbal_yaw → gimbal_yaw_fake`|`fake_vel_transform`|速度补偿旋转|
|`gimbal_pitch_odom → gimbal_yaw`|`robot_state_publisher`|Gazebo joint_states|

**costmap 的视角：** `global_frame: odom`，`robot_base_frame: gimbal_yaw_fake`。所以它需要查 `gimbal_yaw_fake → odom` 变换——逆着树往上走：`gimbal_yaw_fake → gimbal_yaw → ... → base_footprint → odom`。