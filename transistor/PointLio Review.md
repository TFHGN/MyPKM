• 审查这个库时，建议不要从“有没有编译错误”开始，而是从“状态是否属于
  正确的对象”开始。Point-LIO 的风险主要在数据流、时间和状态管理。

  一、先确定审查目标

  当前最重要的目标是：

  src 中不保留模块运行状态全局变量
  参数通过 Params 注入
  Lidar 持有 LiDAR 状态
  Imu 持有 IMU 状态
  ImuProcessor 持有 IMU 初始化状态
  Estimator 持有 EKF、地图和匹配缓存
  LaserMappingNode 只负责流程编排和 ROS 通信

  因此每看到一个全局变量，先问：

  1. 它是配置吗？
  2. 它是运行期状态吗？
  3. 它是某一帧的临时缓存吗？
  4. 它应该属于哪个对象？

  二、第一轮：查全局变量

  先执行：

  rg -n "^\s*extern " src/guga_localization/point_lio

  再查 .cpp 中的文件级定义：

  rg -n "^(bool|int|double|float|std::|PointCloud|V3D|M3D|IVox|
  esekf)" \
    src/guga_localization/point_lio/src/*.cpp

  发现变量后分类：

   类型                          应该放在哪里
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━  ━━━━━━━━━━━━━━━━━━
   YAML 参数                     Params 结构
  ────────────────────────────  ──────────────────
   IMU 初始化状态                ImuProcessor
  ────────────────────────────  ──────────────────
   IMU buffer                    Imu
  ────────────────────────────  ──────────────────
   LiDAR buffer                  Lidar
  ────────────────────────────  ──────────────────
   EKF 状态                      Estimator
  ────────────────────────────  ──────────────────
   地图和匹配缓存                Estimator
  ────────────────────────────  ──────────────────
   ROS publisher/subscription    LaserMappingNode
  ────────────────────────────  ──────────────────
   单帧临时变量                  函数局部变量

  Estimator.cpp 目前仍是主要问题区域。

  三、第二轮：查接口边界

  重点检查类之间有没有直接改成员：

  rg -n "processor_->|imu_->|lidar_->|estimator_->" \
    src/guga_localization/point_lio/src

  例如这种代码就应该警惕：

  processor_->gravity_ = ...
  processor_->imu_need_init_ = ...

  更好的形式是：

  processor_->configure(params);
  processor_->setNeedInit(true);

  审查原则：

  - 外部不能直接写另一个对象的内部状态
  - 状态变化必须通过命名明确的接口
  - 只读数据用 const 接口
  - 不要返回内部容器的可写引用，除非确实需要

  例如：

  std::deque<...>& buffer();

  这是一个风险接口。后续可以改成：

  bool empty() const;
  const ImuMessage& next() const;
  void popAndAdvance();

  让调用者无法绕过 Imu 的状态管理。

  四、第三轮：审查时间语义

  Point-LIO 最容易出现“代码能跑，但结果错”的地方是时间。

  建立一张时间表：

   字段                含义                单位
  ━━━━━━━━━━━━━━━━━━  ━━━━━━━━━━━━━━━━━━  ━━━━━━
   lidar_beg_time      LiDAR 帧起始时间    秒
  ──────────────────  ──────────────────  ──────
   lidar_last_time     LiDAR 帧结束时间    秒
  ──────────────────  ──────────────────  ──────
   点时间偏移          点相对帧首时间      毫秒
  ──────────────────  ──────────────────  ──────
   IMU header stamp    IMU 时间            秒
  ──────────────────  ──────────────────  ──────
   timestamp_offset    LiDAR-IMU 偏移      秒

  然后逐个检查：

  rg -n "header.stamp|lidar_beg_time|lidar_last_time|offset_time|
  timestamp_offset|curvature" \
    src/guga_localization/point_lio/src

  每处都问：

  - 单位是否一致？
  - 是否重复加了时间偏移？
  - 是否把绝对时间和相对时间混用了？
  - 是否在合帧/切帧后仍然单调？
  - 第一帧和 reset 后是否重新初始化？

  五、第四轮：审查 reset

  逐个查看：

  rg -n "void .*reset|resetSystem|reset\\(" \
    src/guga_localization/point_lio/src

  每个 reset 都要检查：

  - buffer 是否清空
  - cursor 是否复位
  - 时间戳是否复位
  - 初始化计数是否复位
  - EKF 协方差是否复位
  - 地图是否复位
  - 点云缓存是否清空
  - 发布计数器是否复位

  尤其要检查：

  第一次运行正常
  reset 后第二次运行
  rosbag 循环播放

  这三种场景的状态是否一致。

  六、第五轮：审查参数

  检查参数是否存在多个副本：

  rg -n "Params|declare_parameter|configureEstimatorParams" \
    src/guga_localization/point_lio/src

  重点看：

  - 同一个 YAML 参数是否被保存到多个结构
  - 多个副本是否可能不同步
  - 默认值是否一致
  - 参数是否在真正使用它的模块中消费
  - 是否还有同名全局变量

  例如 plane_thr 同时存在于 Mapping 和 Estimator 结构，就要明确这
  是：

  同一个配置的两个只读副本

  还是应该只保留一份。

  七、审查时如何记录问题

  每个问题按这个格式写：

  [严重程度] 文件:行号

  问题：
  具体说明错误行为或设计风险。

  原因：
  指出哪个状态、单位或接口导致问题。

  影响：
  说明会影响启动、reset、特定雷达或特定模式。

  建议：
  给出最小修复方向。

  严重程度建议：

  - P0：会崩溃、数据损坏、状态不可恢复
  - P1：主要模式结果错误
  - P2：特定雷达/特定配置下错误
  - P3：接口设计、可维护性或诊断问题

  不要把 clangd 的所有提示都当成真实 bug。先区分：

  编译数据库/头文件问题
  代码语法或类型问题
  运行逻辑问题
  架构问题

  你现在最适合的下一次审查顺序是：

  1. 审查 Estimator.h/.cpp 的所有 extern
  2. 将这些变量分成 Estimator 状态、单帧缓存、配置参数
  3. 先删除 k、idx、effct_feat_num 这类明显的帧内状态全局
  4. 再迁移 kf_input、kf_output 和 ivox_
  5. 最后才处理算法函数签名和地图逻辑

  这样每一步都能保持边界清楚，不会把整个 Point-LIO 一次性改成难以验
  证的状态。


p3: main函数持有下属各类数据,

LaserMapping: 中转站.
- 接受数据, 分发数据, 安排数据流, 交付数据.
- 主要函数: run()
	- 做什么:
	1. 主初始化
	2. 循环开始
		1. 准备同步雷达imu组
		2. ~~判断是否reset ~~(暂时去除)
		3. 初始化扫描
		4. 循环初始化
		5. 处理点云 *可能有越界问题
		6. 发布点云
		7. 更新地图
		8. 发布ROS消息与Log
		循环结束
	3. 保存pcd
	4. 结束

抽象: 
1. initialize
2. predict/process
3. update
4. publish

syncpackages O(Imu + Lidar)
	父函数: run.
	目的: 打包满足时间条件(一帧内)的imu与lidar数据.
	流程:
			
			
	副作用: 更新了帧的最后时间

下一步,每次都会执行prepareFrame
	目的: 轮次初始化.
	流程: 
		耗时统计归零. *注意是否将benchmark常态化*
		imu.process      使用准备好的 measures.   O(Imu) 
			meas的imu空,则退出.
			初始化(once)
				遍历measure里所有imu数据求平均
				初始化状态
				帧数不够直接退出
			使用原始点云
		imu就绪检查
		地图初始化并检查
		下采样参数初始化
		eskf参数准备
		eskf参数准备
至此初始化完成
				