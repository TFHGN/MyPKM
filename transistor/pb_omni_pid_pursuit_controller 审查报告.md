## Context

第一轮已修复：rule-of-five、死代码移除、PathHandler 成员化、treshold typo、日志错误、curvature/approach 限速 bug、include guard 统一、`<math.h>` → `<cmath>`。

本轮聚焦架构分层合规和解耦。

---

## 剩余问题（按优先级）

### P0: 命名空间不一致 — `PID` 和 `PathHandler` 在全局命名空间

架构文档要求全部在 `pb_omni_pid_pursuit_controller` 下。当前：

- `class PID` [pid.hpp:17] — 全局
- `class PathHandler` [pathhandler.hpp:16] — 全局

影响：符号污染，可能与其他库冲突。

**修复**：将 PID 和 PathHandler 移入 `pb_omni_pid_pursuit_controller` 命名空间。需要同步更新所有引用处（.cpp 实现、测试、插件注册宏等）。

### P1: Layer 2 纯净性 — `visualise.hpp` 依赖 `rclcpp_lifecycle`

架构文档要求 Layer 2（Core）为"纯计算，无副作用"。但 `visualizeCurvaturePoints` 接受 `rclcpp_lifecycle::LifecyclePublisher::SharedPtr`，并直接调用 `pub->publish()`。

**修复**：将发布操作移到调用方（Controller），visualizeCurvaturePoints 改为只构建 MarkerArray 返回给调用方。

### P1: 死 include — `geometry_utils.hpp` 包含未使用的 `<std_msgs/msg/u_int8.hpp>`

该类型在 geometry_utils.hpp 和 geometry_utils.cpp 中都没有使用。

### P2: 死 include — `pathhandler.hpp` 包含未使用的 `<nav2_core/controller.hpp>`

PathHandler 不继承 Controller，这个 include 是遗留。

### P2: 代码重复 — `getCostmapMaxExtent` 在 Node 和 PathHandler 中各有一份

[omni_pid_pursuit_controller_node.cpp:432] 和 [pathhandler.cpp:15] 实现相同。

**修复**：Node 中移除，使用 `path_handler_->getCostmapMaxExtent()`。

### P2: 代码重复 — `transformPose` 在 Node 和 PathHandler 中各有一份

[omni_pid_pursuit_controller_node.cpp:438-454] 和 [pathhandler.cpp:22-37] 实现几乎相同（差异仅在 transform_tolerance 来源）。

**修复**：Node 的方法直接委托给 PathHandler，或删除 Node 的版本（如果仅 PathHandler 使用）。

### P3: `PathHandler::transform_tolerance_` 硬编码 1.0s

[pathhandler.cpp:11]：应改为使用 `config_.transform_tolerance`。

---

## 修改计划

### 步骤 1: 命名空间收拢

- [pid.hpp]：包裹 `namespace pb_omni_pid_pursuit_controller { ... }`
- [pid.cpp]：包裹 `namespace pb_omni_pid_pursuit_controller { ... }`
- [pathhandler.hpp]：包裹 `namespace pb_omni_pid_pursuit_controller { ... }`
- [pathhandler.cpp]：包裹 `namespace pb_omni_pid_pursuit_controller { ... }`
- [omni_pid_pursuit_controller_node.hpp]：更新 `PID` 引用为 `pb_omni_pid_pursuit_controller::PID`（已在同一命名空间，可直接用）
- [test_pid.cpp]：更新引用
- [test_pathhandler.cpp]：更新引用
- [test_approach_scaling.cpp]：更新引用
- [visualise.cpp]：这个文件里没有用到 PID 或 PathHandler，但 `visualization_helper` 命名空间保持不变

### 步骤 2: visualise 解耦

- [visualise.hpp]：`visualizeCurvaturePoints` 改为返回 `visualization_msgs::msg::MarkerArray`，去掉 publisher 参数
- [visualise.cpp]：去掉 `pub->publish()`，改为 `return marker_array`
- [omni_pid_pursuit_controller_node.cpp]：`calculateCurvature` 中改为接收返回值并发布

### 步骤 3: 清理死 include

- [geometry_utils.hpp]：删除 `#include "std_msgs/msg/u_int8.hpp"`
- [pathhandler.hpp]：删除 `#include "nav2_core/controller.hpp"` 和 `#include "nav2_util/node_utils.hpp"`

### 步骤 4: 消除代码重复

- [omni_pid_pursuit_controller_node.hpp]：移除 `getCostmapMaxExtent` 和 `transformPose` 声明
- [omni_pid_pursuit_controller_node.cpp]：
    - `getCostmapMaxExtent` 调用改为 `path_handler_->getCostmapMaxExtent()`
    - `checkCollision` 中的 `transformPose` 调用需要通过 PathHandler（PathHandler 有私有的 transformPose 需要公开或通过其他方式暴露）

### 步骤 5: PathHandler transform_tolerance 可配置

- [pathhandler.hpp/cpp]：构造函数增加 `double transform_tolerance` 参数
- [omni_pid_pursuit_controller_node.cpp]：`make_unique<PathHandler>` 时传入 `config_.transform_tolerance`

---

## 验证

```bash
colcon build --packages-select pb_omni_pid_pursuit_controller
./test_pid && ./test_geometry_utils && ./test_visualise && ./test_pathhandler && ./test_approach_scaling && ./test_types
```