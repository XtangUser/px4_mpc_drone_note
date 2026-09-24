# MPC 无人机 → 机器人运动控制：从视频到复现到创新 · 全程学习手册

**时间范围：2026 年 9 月 → 2028 年 7 月（研一上 → 研三暑期实习/秋招）**

> **适用对象**：济南大学计算机研一（2026 级、3 年制、2029 届），已学嵌入式和 ROS 2，正在做 MPC 无人机控制，不读博，毕业后找飞控/运控/规控算法或机器人软件岗位。如果你是 2025 级，把所有日期往前挪一年。
>
> **配套文件**：`学习计划打卡表.xlsx`，里面有 W1–W80 每周的任务、资源、验收标准，状态可以下拉选择，另附资源总表。
>
> **链接说明**：文中 B 站、YouTube、GitHub 链接均已于 2026-09-24 核对。B 站搬运视频可能失效，失效时用文中给出的关键词重新搜索。

---

## 目录

- 第一部分　使用说明：学习方法、时间节奏、环境与硬件、总路线图
- 第二部分　分阶段步骤
  - 阶段 0　启动：搭好环境，第一次让无人机飞起来（W1–W2）
  - 阶段 1　工程基础：C++ / Linux / ROS 2（W3–W10）
  - 阶段 2　控制理论与 MPC：从零实现到 acados（W9–W16）
  - 阶段 3　四旋翼 + PX4 + MPC 仿真闭环（W17–W25）
  - 阶段 4　真机实验 + 论文 1（W23–W40）
  - 阶段 5　强化学习：从理论到 RL 与 MPC 对比（W38–W49）
  - 阶段 6　创新：MPC × 学习（W50–W62）
  - 阶段 7　二选一：嵌入式部署 或 全链路自主（W60–W66）
  - 阶段 8　求职冲刺（W64–W80，之后是暑期实习和秋招）
- 第三部分　附录：模板、数据规范、创新点子库、常见问题、简历写法、面试问答、资源总表、周计划总表

---

# 第一部分　使用说明（先读这一部分）

## 1. 学习方法：每个模块都按同样的 6 步走

| 步骤 | 做什么 | 完成的标志 |
|---|---|---|
| **看** | 用视频建立直觉。1.25–1.5 倍速，边看边记下“我没看懂的 3 个问题” | 笔记里有问题清单 |
| **读** | 用官方文档、书和论文把细节补严谨，回答上一步的问题 | 问题清单全部有答案 |
| **跑** | 复现 GitHub 项目，写复现日志：环境、版本、报错和解决办法 | 复现报告（附录 A） |
| **改** | 至少改动一处：换模型、换任务、加约束、换语言或加评价指标 | 改动前后的对比图 |
| **创** | 在改动的基础上提出一个能验证的小想法，做对比实验 | 实验表格和结论 |
| **写** | 写 README、画图表、写博客或周报，全部推到 GitHub | 可以放进简历的链接 |

> 原则：**验收标准比看完视频更重要**。每个 Step 都有【验收】清单，全部打勾才算完成。只看不写代码，等于没学。

## 2. 时间与节奏

- **周历**：W1 = 2026-09-28（周一）。每个 Step 都标了周次和日期，逐周任务见 xlsx 或附录 I。
- **投入**：平时每周 20–25 小时，实验室科研时间也算在内，因为大部分内容本来就是你的课题；寒暑假每周 35–45 小时。
- **固定动作**：
  - 每天：刷题 30–45 分钟，用 C++ 写
  - 每周六：写周报（附录 B），15 分钟
  - 每月最后一个周日：复盘，更新 GitHub README 和简历
- **卡住怎么办**：某一步超出计划时间的 50%，先跳过，在周报里记下卡点，再去问师兄、导师或者到 GitHub issue 里提问。不要在一个坑里停一周。
- **快进规则**：阶段 0 的自测里得满分的方向，对应的 Step 只做【验收】和【改】。

## 3. 环境与硬件选择

### 3.1 系统：Ubuntu 22.04 + ROS 2 Humble，装双系统

选这套组合的理由：

- 无人机机载电脑常用 Jetson Orin，主流的 JetPack 6 基于 Ubuntu 22.04。电脑和机载电脑用同一套环境，问题最少。
- PX4 官方推荐 Ubuntu 24.04 + Jazzy，同时明确支持 22.04 + Humble。
- Isaac Lab 官方系统要求是 Ubuntu 22.04 或 Windows 11。
- 古月居、鱼香 ROS 的中文教程主要基于 Humble。
- 注意：Humble 在 2027 年 5 月停止维护，之后可以平滑迁到 Jazzy（API 差别很小）。面试时能讲清两者的区别就够了。
- 不建议用虚拟机跑 Gazebo 或 Isaac，会卡，显卡直通也麻烦。只支持 ROS 1 的老项目（Fast-Drone-250、EGO-Planner 原版）用 Docker 跑。

### 3.2 硬件分支：按实验室条件选一条

| 条件 | 仿真 | 强化学习训练 | 真机 |
|---|---|---|---|
| **A　基础**（普通笔记本或无独显） | Gazebo、RotorPy、gym-pybullet-drones | CPU 就能跑：RotorPy、gym-pybullet-drones、learning-to-fly | 实验室无人机，或 Crazyflie |
| **B　中等**（RTX 3060–4070，8–12 GB 显存） | 同上 | 另加 MuJoCo Playground（JAX/MJX）、CleanRL | 同上 |
| **C　充足**（≥16 GB 显存 + 32 GB 内存） | 同上，另加 Isaac Sim | 另加 Isaac Lab、OmniDrones、unitree_rl_lab | 同上 |

说明：Isaac Lab 官方建议 32 GB 以上内存、16 GB 以上显存，达不到就走 A 或 B 分支，完全不影响主线。

**真机三选一：**

1. **实验室现成的 PX4 无人机**：最好，直接用。
2. **自己组装**：按浙大 FAST Lab 开源的 Fast-Drone-250 清单组装，有免费视频课。
3. **小型室内平台 Crazyflie 2.x**：需要定位扩展板，比如 Flow deck 或 Lighthouse。适合阶段 7 在单片机上部署 MPC 或 RL。

## 4. 总路线图

| 阶段 | 周次 | 日期 | 主题 | 关键产出 |
|---|---|---|---|---|
| 0 | W1–W2 | 2026-09-28 ～ 10-11 | 环境搭建，第一次飞起来 | 仿真里用 offboard 模式起飞；建好学习仓库 |
| 1 | W3–W10 | 2026-10-12 ～ 12-06 | C++ / Linux / ROS 2 工程基础 | 3 个 C++ 小项目；ROS 2 画圆控制节点 |
| 2 | W9–W16 | 2026-11-23 ～ 2027-01-17 | 控制理论与 MPC 从零实现 | 依次写出线性 MPC、NMPC、acados 版本；一篇博客 |
| 3 | W17–W25 | 2027-01-18 ～ 03-21 | 四旋翼 + PX4 + MPC 仿真闭环 | 复现并改造 px4-mpc；抗扰对比实验 |
| 4 | W23–W40 | 2027-03-01 ～ 07-04 | 真机实验 + 论文 1 | 实机视频；**论文 1 定稿** |
| 5 | W38–W49 | 2027-06-14 ～ 09-05 | 强化学习 + RL 与 MPC 对比 | RL 与 MPC 对比报告 |
| 6 | W50–W62 | 2027-09-06 ～ 12-05 | 创新：MPC × 学习 | **W52 前论文 1 投出**；论文 2 或毕业论文核心章节 |
| 7 | W60–W66 | 2027-11-15 ～ 2028-01-02 | 嵌入式部署 或 全链路自主（二选一） | 项目 3 + 演示视频 |
| 8 | W64–W80 | 2027-12-13 ～ 2028-04-09 | 求职冲刺 | 简历、面试准备、暑期实习 offer |
| — | — | 2028-06 ～ 08 | 暑期实习 | 争取转正 |
| — | — | 2028-07 ～ 10 | 秋招（提前批 + 正式批） | offer |

---

# 第二部分　分阶段步骤

## 阶段 0　启动：搭好环境，第一次让无人机飞起来（W1–W2｜2026-09-28 → 10-11）

> 国庆假期正好用来装环境。目标：两周内看到仿真里的无人机被你运行的 ROS 2 程序控制起飞，先建立信心。

### Step 0.1　自测与定位（半天）

每题打 0–2 分：0 = 不会，1 = 看教程能做，2 = 不看教程能做。

| 方向 | 自测题 | 得分 |
|---|---|---|
| C++ | 不看资料写出：用 unique_ptr 管理资源的类；用 std::thread、mutex、condition_variable 实现生产者-消费者 | |
| C++ | 讲清左值和右值、std::move 到底做了什么、虚函数表 | |
| Linux | 用 gdb 定位段错误；写一个带参数的 bash 脚本 | |
| ROS 2 | 不看教程写出 C++ 的发布/订阅、launch 文件和参数 YAML | |
| ROS 2 | 解释 QoS 的 reliability、durability、history，以及 executor 是什么 | |
| 控制 | 手推离散 LQR；把 MPC 写成 QP 形式（H、f 矩阵） | |
| 数学 | KKT 条件；矩阵求导；四元数和旋转矩阵互转 | |
| Python | numpy 向量化写法；matplotlib 画多子图 | |

规则：某个方向两题都是 2 分，对应的 Step 只做【验收】和【改】；有 0 分，就按计划完整学。

### Step 0.2　安装系统和工具（2–3 天）

【看】

- B 站 · 鱼香ROS《ROS 2 机器人开发从入门到实践》第 1.2.1–1.2.4 节（安装 Ubuntu 和 ROS 2）：https://www.bilibili.com/video/BV1GW42197Ck/

【跑】

```bash
# 1) 基础工具
sudo apt update && sudo apt install -y git curl build-essential cmake python3-pip python3-venv tmux

# 2) ROS 2 Humble
#    方式 A：鱼香ROS 一键安装（按提示选“一键安装 ROS”，再选 Humble）
wget http://fishros.com/install -O fishros && . fishros
#    方式 B：按官方步骤安装 https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html

# 3) 验证：开两个终端分别运行
ros2 run demo_nodes_cpp talker
ros2 run demo_nodes_py listener

# 4) pip 换国内镜像（下载慢时使用）
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

- 显卡驱动：在“软件和更新 → 附加驱动”里选推荐的专有驱动。
- VSCode 插件：C/C++（或 clangd）、Python、CMake Tools、ROS。
- 配置 GitHub SSH key。GitHub 访问慢时，先找项目官方提供的 Gitee 镜像。

【验收】

- [ ] talker 和 listener 能互相通信
- [ ] `nvidia-smi` 正常输出（有独显时）
- [ ] 能 `git push` 到自己的 GitHub 仓库

### Step 0.3　第一次飞起来：PX4 SITL + ROS 2 Offboard（3–4 天）

【看】

- PX4 官方 ROS 2 User Guide：https://docs.px4.io/main/en/ros2/user_guide
- PX4 官方 ROS 2 Offboard 控制示例：https://docs.px4.io/main/en/ros2/offboard_control
- 中文图文教程：阿木实验室《最新 PX4×ROS2 保姆级仿真部署教程》：https://www.amovlab.com/news/detail?id=329

【跑】

```bash
# ① 下载 PX4 源码并安装工具链（会自动安装 Gazebo）
cd ~ && git clone https://github.com/PX4/PX4-Autopilot.git --recursive
bash ./PX4-Autopilot/Tools/setup/ubuntu.sh        # 装完重启一次
cd ~/PX4-Autopilot && make px4_sitl               # 首次编译比较久

# ② 建 ROS 2 工作空间：px4_msgs + 官方示例 + XRCE-DDS Agent（Humble 对应 v2.4.2）
pip install --user -U empy==3.3.4 pyros-genmsg setuptools
mkdir -p ~/ros2_px4_ws/src && cd ~/ros2_px4_ws/src
git clone https://github.com/PX4/px4_msgs.git
git clone https://github.com/PX4/px4_ros_com.git
git clone -b v2.4.2 https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
cd ~/ros2_px4_ws && source /opt/ros/humble/setup.bash && colcon build

# ③ 先打开 QGroundControl，再开三个终端
# 终端 1：Agent
source ~/ros2_px4_ws/install/setup.bash && MicroXRCEAgent udp4 -p 8888
# 终端 2：PX4 仿真
cd ~/PX4-Autopilot && make px4_sitl gz_x500
# 终端 3：官方 offboard 示例（自动解锁并升到 5 m）
source ~/ros2_px4_ws/install/setup.bash && ros2 run px4_ros_com offboard_control
```

> **版本建议**：想要稳定，就把 PX4 切到最新的 release 标签，然后执行 `git submodule update --init --recursive`；px4_msgs 和 px4_ros_com 切到对应的 release 分支（用 `git branch -a` 查看）。三者版本不一致时，最常见的现象是消息对不上。

【验收】

- [ ] Gazebo 里的 x500 自动解锁，升到 5 m 后悬停
- [ ] `ros2 topic list` 能看到 `/fmu/out/...` 和 `/fmu/in/...` 话题
- [ ] 能讲清三件事：
  - Agent 和 Client 分别运行在哪里
  - 为什么订阅 PX4 的话题必须用 sensor-data QoS
  - PX4 用 NED 坐标系，ROS 用 ENU 坐标系

【常见坑】

- **飞机不解锁**：没有连 QGC。PX4 默认要求连着地面站或遥控器。
- **订阅不到 `/fmu/out` 的数据**：QoS 不匹配，改用 `rclcpp::SensorDataQoS()`。
- **消息字段对不上**：px4_msgs 的分支和 PX4 固件版本不一致。PX4 v1.16 起可以运行官方的消息翻译节点（translation node）解决。

### Step 0.4　建学习仓库和周报（半天）

在 GitHub 新建仓库 `robotics-learning-log`，目录结构如下：

```text
robotics-learning-log/
├── 00_weekly/        # 周报，每周六一篇
├── 01_cpp/           # C++ 练习与小项目
├── 02_ros2/          # ROS 2 练习
├── 03_control_mpc/   # LQR / MPC 从零实现
├── 04_px4_mpc/       # PX4 + MPC（仿真和实机）
├── 05_rl/            # 强化学习
├── 06_research/      # 论文复现与创新
├── 07_embedded/      # 嵌入式部署
└── papers/           # 论文笔记，一篇一页
```

- 周报模板见附录 B，复现报告模板见附录 A。
- 从本周开始每天刷题：研一上按代码随想录的顺序刷（https://www.programmercarl.com/ ），寒假起换成 LeetCode 热题 100（https://leetcode.cn/studyplan/top-100-liked/ ）。

---

## 阶段 1　工程基础：C++ / Linux / ROS 2（W3–W10｜10-12 → 12-06）

> 目标：把面试一定会问的 C++ 和 ROS 2 一次打牢，同时写出第一个能控制 PX4 的 ROS 2 节点。

### Step 1.1　现代 C++ 与并发（W3–W6）

【看】以一个为主，另一个用来查漏补缺：

- B 站 · 小彭老师《高性能并行编程与优化》第一季第 1–5 讲，内容依次是 CMake/Git、STL 与 RAII、模板、编译器优化、C++11 多线程。
  - 频道：https://space.bilibili.com/263032155
  - 课件与代码：https://github.com/parallel101/course
- YouTube · The Cherno C++ 系列，挑着看：智能指针、移动语义、左值右值、线程、模板。https://www.youtube.com/playlist?list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb

【读】

- 《Effective Modern C++》第 4 章（智能指针）、第 5 章（右值引用、移动语义、完美转发）、第 7 章（并发 API）
- 小彭老师主编的现代 C++ 中文百科，平时查阅用：https://github.com/parallel101/cppguidebook

【跑 + 做】三个小项目放进 `01_cpp/`，统一用 CMake + GoogleTest：

```bash
sudo apt install -y libgtest-dev valgrind linux-tools-common linux-tools-$(uname -r)
```

```cmake
# 01_cpp/CMakeLists.txt（模板）
cmake_minimum_required(VERSION 3.16)
project(cpp_practice LANGUAGES CXX)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
find_package(GTest REQUIRED)
find_package(Threads REQUIRED)
enable_testing()
add_executable(test_ring_buffer tests/test_ring_buffer.cpp)
target_include_directories(test_ring_buffer PRIVATE include)
target_link_libraries(test_ring_buffer GTest::gtest_main Threads::Threads)
add_test(NAME ring_buffer COMMAND test_ring_buffer)
```

1. **线程安全环形缓冲区**：模板类，push 和 pop 各写阻塞版和非阻塞版。机器人里缓存传感器数据用的就是它。
2. **线程池**：用 std::function 做任务队列，配合条件变量，通过 future 返回结果。
3. **异步日志器**：前台线程只写队列，后台线程负责落盘，每条带时间戳。用 perf 找热点。

【验收】

- [ ] 三个项目的单元测试全部通过，`valgrind --leak-check=full` 没有内存泄漏
- [ ] 不看资料手写一个简化版 shared_ptr（引用计数，加上拷贝和移动）
- [ ] 能讲清 mutex、condition_variable、atomic 的区别和各自适用的场景

【改】把环形缓冲区改成单生产者、单消费者的无锁版本（std::atomic 加内存序），和有锁版本做性能对比。

### Step 1.2　Linux 与工具链（W3–W4，和 1.1 同时进行）

【看】MIT《计算机教育中缺失的一课》，重点看 Shell、Vim 基础、Git、调试与性能分析、元编程（make）。

- 中文讲义：https://missing-semester-cn.github.io/
- B 站中文字幕版（UP 主“刘黑黑a”）：https://space.bilibili.com/518734451

【做】写一个脚本 `tools/sim_up.sh`，用 tmux 一键启动 Agent、PX4 SITL 和你自己的节点。以后每次仿真都能省几分钟。

【验收】

- [ ] 会用 gdb 看 backtrace，会用 `perf record` 和 `perf report`
- [ ] Git：会建分支、rebase、解决冲突，能写规范的 commit message

### Step 1.3　深入 ROS 2，写第一个 PX4 控制节点（W5–W10）

【看】已经会 ROS 2 基础的话，1.5 倍速快过：

- B 站 · 鱼香ROS《ROS 2 机器人开发从入门到实践》第 2–6 章：https://www.bilibili.com/video/BV1GW42197Ck/
- 或者 B 站 · 古月居《ROS2 入门 21 讲》，DDS、TF、Launch 这几讲一定要看：https://www.bilibili.com/video/BV16B4y1Q7jQ/
- YouTube · Articulated Robotics，看 ros2_control 相关的几集：https://www.youtube.com/playlist?list=PLunhqkrRNRhYAffV8JDiFOatQXuU-NnxT

【读】ROS 2 Humble 官方文档 https://docs.ros.org/en/humble/

- Concepts 部分：Executors、QoS settings
- Tutorials 部分：Composition，以及 Demos 里的 intra-process 和 lifecycle

【跑】

- ros2/demos 里的 composition、intra_process_demo、lifecycle：https://github.com/ros2/demos
- ros2_control_demos 的 example_1（RRBot）：https://github.com/ros-controls/ros2_control_demos

【做】C++ 画圆 offboard 节点，放在 `02_ros2/circle_offboard`：

```bash
cd ~/ros2_px4_ws/src
ros2 pkg create --build-type ament_cmake --dependencies rclcpp px4_msgs \
  --node-name circle_offboard circle_offboard
```

把生成的 `src/circle_offboard.cpp` 替换成下面的骨架，再自己补全：

```cpp
#include <rclcpp/rclcpp.hpp>
#include <px4_msgs/msg/offboard_control_mode.hpp>
#include <px4_msgs/msg/trajectory_setpoint.hpp>
#include <px4_msgs/msg/vehicle_odometry.hpp>
#include <cmath>
using namespace std::chrono_literals;
using px4_msgs::msg::OffboardControlMode;
using px4_msgs::msg::TrajectorySetpoint;
using px4_msgs::msg::VehicleOdometry;

class CircleOffboard : public rclcpp::Node {
public:
  CircleOffboard() : Node("circle_offboard") {
    radius_ = declare_parameter("radius", 2.0);  // 半径 m
    omega_  = declare_parameter("omega", 0.5);   // 角速度 rad/s
    mode_pub_ = create_publisher<OffboardControlMode>(
        "/fmu/in/offboard_control_mode", 10);
    sp_pub_ = create_publisher<TrajectorySetpoint>(
        "/fmu/in/trajectory_setpoint", 10);
    // 订阅 PX4 话题必须用 sensor-data QoS
    odom_sub_ = create_subscription<VehicleOdometry>(
        "/fmu/out/vehicle_odometry", rclcpp::SensorDataQoS(),
        [this](VehicleOdometry::UniquePtr msg) { odom_ = *msg; });
    t0_ = now();
    // 50 Hz 持续发送（低于约 2 Hz 会退出 offboard）
    timer_ = create_wall_timer(20ms, [this] { step(); });
  }

private:
  void step() {
    // PX4 的时间戳单位是微秒
    const uint64_t ts = get_clock()->now().nanoseconds() / 1000;
    OffboardControlMode mode{};
    mode.position = true;
    mode.timestamp = ts;
    mode_pub_->publish(mode);

    const double t = (now() - t0_).seconds();
    const double c = std::cos(omega_ * t), s = std::sin(omega_ * t);
    const double r = radius_, w = omega_;
    TrajectorySetpoint sp{};
    // NED 坐标系：z = -3 表示离地 3 m
    sp.position = {float(r * c), float(r * s), -3.0f};
    // 速度前馈：圆轨迹对时间求导
    sp.velocity = {float(-r * w * s), float(r * w * c), 0.0f};
    // 不使用的量填 NaN
    sp.acceleration = {NAN, NAN, NAN};
    sp.yaw = 0.0f;
    sp.timestamp = ts;
    sp_pub_->publish(sp);
  }

  double radius_, omega_;
  rclcpp::Time t0_;
  VehicleOdometry odom_{};
  rclcpp::Publisher<OffboardControlMode>::SharedPtr mode_pub_;
  rclcpp::Publisher<TrajectorySetpoint>::SharedPtr sp_pub_;
  rclcpp::Subscription<VehicleOdometry>::SharedPtr odom_sub_;
  rclcpp::TimerBase::SharedPtr timer_;
};

int main(int argc, char** argv) {
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<CircleOffboard>());
  rclcpp::shutdown();
  return 0;
}
```

运行步骤：

1. 启动 Agent 和 `make px4_sitl gz_x500`。
2. 运行 `ros2 run circle_offboard circle_offboard`。
3. 在 PX4 终端（`pxh>`）依次输入 `commander arm` 和 `commander mode offboard`。

新版 PX4 的部分话题名带版本后缀，以 `ros2 topic list` 显示的实际名字为准。

【验收】

- [ ] 无人机在 3 m 高度画半径 2 m 的圆
- [ ] 半径和角速度通过参数 YAML 和 launch 文件配置
- [ ] 用 rosbag2 录下 `/fmu/out/vehicle_odometry` 和设定点，在 PlotJuggler 里画出位置误差曲线。PlotJuggler：https://github.com/facontidavide/PlotJuggler
- [ ] 能回答：
  - OffboardControlMode 为什么要一直发？（发送频率低于约 2 Hz，PX4 会退出 offboard 模式）
  - NED 坐标系下 z = -3 是什么意思？

【改】

- 删掉速度前馈，再补上加速度前馈（圆周运动的向心加速度），对比三种情况的误差
- 把节点改写成 component，和一个“轨迹生成” component 放进同一个进程，开启进程内通信

### Step 1.4　刷题（W3 开始，每天 30–45 分钟）

- **W3–W16**：按代码随想录的顺序刷：数组、链表、哈希表、字符串、栈与队列、二叉树、回溯、贪心、动态规划。GitHub：https://github.com/youngyangyang04/leetcode-master
- **W17 起**：LeetCode 热题 100 第一遍；**W67 起**刷第二遍。
- 每道题记三样：一句话思路、复杂度、易错点。

---

## 阶段 2　控制理论与 MPC：从零实现到 acados（W9–W16｜11-23 → 2027-01-17）

> 目标：MPC 能手推、能从零写，还能用工业级求解器 acados 写出实时 NMPC，并讲清每个设计选择的理由。这是你简历上最核心的能力。

### Step 2.1　补控制理论（W9–W10）

【看】

- B 站 · DR_CAN，现代控制理论部分：状态空间、可控性与可观性、LQR。https://space.bilibili.com/230105574
- YouTube · Steve Brunton《Control Bootcamp》，看 LQR 和 Kalman Filter 相关的几集：https://www.youtube.com/playlist?list=PLMrJAkhIeNNR20Mz-VpzgfQs5zrYi085m

【做】用 Python 实现倒立摆（或 2D 四旋翼）的线性化和离散 LQR（`scipy.linalg.solve_discrete_are`），画出状态收敛曲线。然后故意把模型参数改错 20%，看看效果有什么变化。

【验收】

- [ ] 手推离散 LQR 的 Riccati 递推
- [ ] 用对比图说明 Q、R 权重如何影响响应

### Step 2.2　MPC 原理，从零写线性 MPC（W11–W12）

【看】

- B 站 · DR_CAN【MPC 模型预测控制器】第 1–4 集，从最优控制的基本概念讲到数学推导、例子和代码：https://www.bilibili.com/video/BV1cL411n7KV/
- MathWorks《Understanding Model Predictive Control》，共 10 集，先看第 1–5 集：https://www.mathworks.com/videos/understanding-model-predictive-control-part-1-why-use-mpc--1526484715269.html
- B 站《你还在用 PID？MPC 模型预测控制，从公式到代码！》，包含 Matlab 和 C++ 控制云台电机的实现：https://www.bilibili.com/video/BV1U54y1J7wh/

【读】Rawlings、Mayne、Diehl《Model Predictive Control: Theory, Computation, and Design》第 1 章，作者官网提供免费 PDF：https://sites.engineering.ucsb.edu/~jbraw/mpc/

【做】从零写一个线性 MPC。先用 CVXPY 把问题写清楚，再自己构造 QP：

```python
# pip install cvxpy osqp numpy matplotlib
import numpy as np, cvxpy as cp

dt, N = 0.05, 20
# 双积分器：状态 x = [位置, 速度]，输入 u = 加速度
A = np.array([[1, dt], [0, 1]])
B = np.array([[0.5 * dt**2], [dt]])
Q = np.diag([10.0, 1.0]); R = np.array([[0.1]])
x = cp.Variable((2, N + 1)); u = cp.Variable((1, N))
x0 = cp.Parameter(2); xref = cp.Parameter(2)

cost, cons = 0, [x[:, 0] == x0]
for k in range(N):
    cost += cp.quad_form(x[:, k] - xref, Q) + cp.quad_form(u[:, k], R)
    cons += [x[:, k + 1] == A @ x[:, k] + B @ u[:, k],
             cp.abs(u[:, k]) <= 2.0,          # 输入约束
             cp.abs(x[1, k + 1]) <= 1.5]      # 状态约束（速度）
cost += cp.quad_form(x[:, N] - xref, 10 * Q)  # 终端代价
prob = cp.Problem(cp.Minimize(cost), cons)

state, xref.value = np.zeros(2), np.array([1.0, 0.0])
for t in range(100):
    # 滚动时域：每一步只执行第一个控制量
    x0.value = state
    prob.solve(solver=cp.OSQP, warm_start=True)
    state = A @ state + B.flatten() * u.value[0, 0]
```

跑通之后继续三步：

1. 把模型换成 2D 四旋翼（y、z、θ）的线性化模型。
2. 不用 CVXPY，自己推 condensed 形式的 H 和 f，直接调用 osqp，比较求解时间。
3. 和 LQR 做对比：有约束时 LQR 会违反约束，MPC 不会。

【验收】

- [ ] 一张“MPC 与 LQR 对比（有约束）”的图
- [ ] 能讲清预测时域 N、控制周期、终端代价各自的影响

### Step 2.3　数值最优控制：从 CasADi 到 acados（W13–W14）

【看】

- YouTube · Mehrez《MPC and MHE implementation using CasADi》工作坊：https://www.youtube.com/playlist?list=PLK8squHT_Uzej3UCUHjtOtm5X7pMFSgAL
  - 配套代码：https://github.com/MMehrez/MPC-and-MHE-implementation-in-MATLAB-using-Casadi
- 弗莱堡大学《Model Predictive Control and Reinforcement Learning》（Diehl & Boedecker），看 Lecture 2–4。
  - 课程主页：https://www.syscop.de/teaching/ss2022/model-predictive-control-and-reinforcement-learning
  - B 站有双语字幕搬运，搜“弗莱堡大学 模型预测控制 强化学习”，例如 https://www.bilibili.com/video/BV1EQ2TY5EZt/

【跑】

```bash
# 安装 acados（官方文档：https://docs.acados.org/installation/）
cd ~ && git clone https://github.com/acados/acados.git && cd acados
git submodule update --recursive --init
mkdir -p build && cd build && cmake -DACADOS_WITH_QPOASES=ON .. && make install -j4

# Python 接口：Ubuntu 22.04 可以直接装到用户目录，
# 这样 ROS 2 的 Python 节点也能 import
pip install --user -e ~/acados/interfaces/acados_template
echo 'export ACADOS_SOURCE_DIR=$HOME/acados' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/acados/lib' >> ~/.bashrc
source ~/.bashrc

# 跑官方入门例子
cd ~/acados/examples/acados_python/getting_started && python3 minimal_example_ocp.py
```

> 第一次运行会自动下载 t_renderer。下载失败的话，到 https://github.com/acados/tera_renderer/releases 手动下载，重命名为 `t_renderer`，放进 `~/acados/bin` 并执行 `chmod +x`。

- 同时跑通 PythonRobotics 的 MPC 路径跟踪：https://github.com/AtsushiSakai/PythonRobotics/tree/master/PathTracking/model_predictive_speed_and_steer_control

【做】

1. 用 CasADi（Opti + IPOPT，multiple shooting）写差速小车或 2D 四旋翼的 NMPC。
2. 用 acados 重写同一个问题（SQP_RTI + PARTIAL_CONDENSING_HPIPM），比较两者的求解时间。

【验收】

- [ ] 求解时间对比表：IPOPT 与 acados SQP_RTI 的均值和最大值
- [ ] 能解释：single shooting 与 multiple shooting 的区别、RTI（实时迭代）、warm start、acados 为什么快

### Step 2.4　MPC 进阶：约束、稳定性、无偏跟踪（W15–W16，期末周，任务较轻）

【读】Rawlings 第 2 章（稳定性：终端代价和终端约束），第 3 章鲁棒 MPC 选读。

【做】给 NMPC 加上两样东西：

- **软约束**（slack 变量）：避免约束互相冲突导致无解。
- **扰动估计 + 积分作用**，实现 offset-free MPC。验证方法：模型质量设错 20% 时，稳态误差应该收敛到 0。

【产出】写一篇博客《从零实现 MPC：LQR → 线性 MPC → NMPC → acados》，发在知乎、CSDN 或 B 站专栏，把链接放进 GitHub README。

【验收】

- [ ] 有扰动估计和没有扰动估计时的稳态误差对比图
- [ ] 能回答面试题：“MPC 求解超时或者无解，你怎么处理？”要点：warm start、RTI、缩短预测时域、软约束、兜底控制器

---

## 阶段 3　四旋翼 + PX4 + MPC 仿真闭环（W17–W25｜2027-01-18 → 03-21）

> 寒假是黄金期。目标：在 PX4 SITL 里跑通一个 ROS 2 + acados 的 MPC 并加以改造，做出论文 1 的仿真实验。

### Step 3.1　四旋翼动力学与控制（W17–W18）

【看】

- B 站 · 北航全权《多旋翼飞行器设计与控制》[官方原版]，重点看第 5 讲（坐标系与姿态表示）、第 6 讲（动态模型）、第 11 讲（底层飞行控制）：https://www.bilibili.com/video/BV1my4y1v7yC/
- 宾大 Vijay Kumar《Robotics: Aerial Robotics》（原 Coursera 课程，页面已下线，B 站有 42 集完整搬运），看第 2–3 周的内容：https://www.bilibili.com/video/BV1Xa4y1h7Xt/

【读】

- Lee, Leok, McClamroch, “Geometric tracking control of a quadrotor UAV on SE(3)”, CDC 2010
- Mellinger & Kumar, “Minimum snap trajectory generation and control for quadrotors”, ICRA 2011

【跑】RotorPy 是一个 Python 多旋翼仿真器，自带 SE(3) 控制器和轨迹生成：

```bash
git clone https://github.com/spencerfolk/rotorpy.git && cd rotorpy
pip install -e ".[learning]"      # 连同 RL 依赖（stable-baselines3）一起装，阶段 5 还会用到
# 按 README 的 Usage 部分运行基础仿真例程
```

【做】在 RotorPy 里把控制器换成你的 acados NMPC，输入取集体推力和机体角速度。在同一条 minimum-snap 轨迹上，和自带的 SE(3) 控制器对比。

【验收】

- [ ] 手推四旋翼的平动方程和转动方程（四元数形式）
- [ ] NMPC 与 SE(3) 控制器的跟踪误差、求解时间对比图

### Step 3.2　读懂 PX4 的控制架构（W18–W19，春节周任务较轻）

【读】

- PX4 Controller Diagrams：https://docs.px4.io/main/en/flight_stack/controller_diagrams
- Offboard 模式和 ROS 2 Offboard 示例：https://docs.px4.io/main/en/ros2/offboard_control
- 源码：`src/modules/mc_pos_control`、`mc_att_control`、`mc_rate_control`

【做】画一张 PX4 串级控制图：位置 P → 速度 PID → 加速度转换为推力和姿态 → 姿态 P → 角速度 PID。在图上标出外部 MPC 可以接入的层级：

- 位置、速度或加速度设定点
- 姿态设定点，或角速度 + 推力设定点

写出每种接法的优缺点。

【验收】

- [ ] 一页架构笔记，放进 `04_px4_mpc/notes/`

### Step 3.3　复现 px4-mpc（W20）

项目：DISCOWER/px4-mpc，基于 ROS 2 + acados + PX4，由 KTH 团队维护。https://github.com/DISCOWER/px4-mpc

【跑】

```bash
mkdir -p ~/ros2_ws/src && cd ~/ros2_ws/src
git clone https://github.com/PX4/px4_msgs.git            # 和 PX4 固件版本保持一致
git clone https://github.com/Jaeyoung-Lim/px4-offboard.git
git clone https://github.com/DISCOWER/px4-mpc.git
cd ~/ros2_ws && source /opt/ros/humble/setup.bash
colcon build --packages-up-to px4_mpc && source install/setup.bash

# 终端 1：Agent（用阶段 0 从源码编译的 MicroXRCEAgent）
source ~/ros2_px4_ws/install/setup.bash && MicroXRCEAgent udp4 -p 8888
# 终端 2：PX4 SITL
cd ~/PX4-Autopilot && make px4_sitl gz_x500
# 终端 3：MPC
source ~/ros2_ws/install/setup.bash && ros2 launch px4_mpc mpc_quadrotor_launch.py
# 回到终端 2（pxh>）
commander arm
commander mode offboard
```

不开 QGC 时，按该仓库 README 最后一节设置仿真专用参数（COM_RC_IN_MODE 等）。这些参数只能在仿真里用。

【读代码】回答下面的问题，写进复现报告：

- 状态量和控制量分别是什么？模型定义在哪个文件？
- acados 求解器的参数是多少？包括预测步数 N、时域 Tf、QP 求解器、积分器。
- 订阅和发布了哪些 PX4 话题？用的是哪一种 offboard 设定点？

【验收】

- [ ] 在 RViz 里拖动目标点，无人机能跟过去
- [ ] 按附录 A 模板写完复现报告

### Step 3.4　改造：轨迹跟踪、改写成 C++、数据闭环（W21–W22）

【改】

1. **目标点改成时变参考轨迹**（圆、8 字、minimum-snap），把整段参考序列送进 MPC 的预测时域。
2. **写 `eval_bag.py`**：读 rosbag，自动计算位置 RMSE、最大误差和求解时间分布（均值、P99、最大值），并画图。
3. **Python 节点改成 C++ 节点**：用 acados 生成的 C 代码，通过 rclcpp 调用。C++ 接口写法可以参考 https://github.com/RPS98/mpc
4. **做基线**：同一条轨迹，用 PX4 自带的位置控制器再跑一遍。

【验收】

- [ ] C++ 节点稳定运行在 50 Hz 以上，求解时间的 P99 小于控制周期
- [ ] 一个命令出图：MPC 和 PX4 默认控制器的误差曲线与对比表

### Step 3.5　创新雏形：抗扰 NMPC（W23–W25，论文 1 的核心）

【跑】加入扰动：

```bash
PX4_GZ_WORLD=windy make px4_sitl gz_x500     # PX4 自带的有风世界
```

同时在 MPC 模型里故意把质量设错 20%，模拟挂载负载。

【创】二选一，也可以都做：

- **扰动观测器（DOB）**：估计外力，作为 NMPC 模型参数实时更新，实现 offset-free NMPC。
- **残差学习**：用飞行数据拟合残差加速度（先用线性回归或小 MLP），加进 NMPC 模型。这一条也为阶段 6 铺路。

【验收】

- [ ] 对比表：PX4 PID、NMPC、NMPC+DOB（或 NMPC+残差）在无风、有风、质量失配三种工况下的 RMSE
- [ ] 仿真章节初稿（图、表、文字）


---

## 阶段 4　真机实验 + 论文 1（W23–W40｜2027-03-01 → 07-04）

> 目标：把仿真里的 NMPC 安全地搬到真机上，拿到实机数据和视频，7 月初完成论文定稿。
>
> **安全第一**：
> - 在室外空旷场地或网笼里飞。
> - 必须有安全员手持遥控，随时能切回手动。
> - 先系绳，先低空、低速。
> - 每次起飞前走一遍检查清单（附录 E）。

### Step 4.1　硬件平台（W23–W27）

【看】B 站 · 浙大 FAST Lab《从 0 制作自主空中机器人》第 1–8 课，内容包括硬件选型、焊接、飞控接线与设置、机载电脑、环境配置。

- 视频：https://www.bilibili.com/video/BV1WZ4y167me/
- 文档与物料清单：https://github.com/ZJU-FAST-Lab/Fast-Drone-250

【读】PX4 ROS 2 User Guide 中的 “Using Flight Controller Hardware” 一节：真机上 XRCE-DDS Client 跑在飞控上，Agent 跑在机载电脑上，两者通过串口或以太网连接。

【做】

- 实验室有 PX4 无人机就直接用；没有就按 Fast-Drone-250 清单自己组装。注意：这门课的软件部分基于 ROS 1，只借鉴硬件和飞控设置部分。
- 按手动 → 定高 → 定点的顺序把飞机飞稳。用 PX4 Flight Review（https://review.px4.io/ ）分析日志，调 PID。
- 机载电脑（Jetson Orin 用 JetPack 6，即 Ubuntu 22.04）安装 ROS 2 Humble 和 Agent，并连上飞控。

【验收】

- [ ] 定点悬停稳定，日志里没有明显的振动问题
- [ ] 在机载电脑上执行 `ros2 topic echo /fmu/out/vehicle_odometry` 有数据输出

### Step 4.2　定位（W27–W28）

按实验室条件选一种：

- 室内动作捕捉（效果最好）
- VIO：可参考 Fast-Drone-250 课程的 VINS 部分
- 室外 GPS

外部位姿通过 PX4 的外部视觉接口送进 EKF2。参考文档（含坐标系约定）：https://docs.px4.io/main/en/ros/external_position_estimation

【验收】

- [ ] 统计悬停位置误差的标准差

### Step 4.3　真机：从 offboard 到 NMPC（W29–W33）

按顺序一步一步来，每一步都保存 rosbag 和视频：

1. 用 px4_ros_com 的 offboard 例程在真机上悬停。
2. 用 PX4 自带的位置控制跑低速圆，作为基线数据。
3. 用你的 NMPC 悬停。必须做好三件事：
   - 输出限幅
   - 求解失败或超时时，立即切回 PX4 位置模式
   - 遥控器急停
4. 用 NMPC 跑圆和 8 字，速度按 0.5、1、2 m/s 逐步提高。
5. 抗扰实验：挂偏心负载，用风扇吹侧风。

【验收】

- [ ] 真机 NMPC 跟踪曲线，以及机载求解时间直方图
- [ ] 1 分钟演示视频，可以放在 B 站和 GitHub README 里

### Step 4.4　写论文 1（W33–W40）

- **结构**：引言 → 相关工作 → 四旋翼建模 → 控制器设计（NMPC + DOB 或残差）→ 仿真实验 → 实机实验 → 结论。
- **投稿目标**：
  - 先对照学院的学位要求（CCF-C 及以上、SCI 或 EI 期刊），和导师商量确定。
  - 控制方向常见的期刊有《控制与决策》《控制理论与应用》（EI 中文期刊），以及 Drones、IEEE Access（SCI）等。
  - 投稿前核对期刊最新的检索情况。
- **工具**：用 Overleaf 写 LaTeX，用 Zotero 管理文献，图统一用 matplotlib 画（字体、线宽、配色保持一致）。
- **截止时间**：★ W40（2027-07-04 前）定稿，★ W52（2027-09-26 前）投出。

---

## 阶段 5　强化学习：从理论到 RL 与 MPC 对比（W38–W49｜2027-06-14 → 09-05）

> 目标：补上行业最看重的第二项能力。重点不是追论文，而是做一个“同一任务下 RL 和 MPC 的公平对比”。

### Step 5.1　RL 理论（W38–W41）

【看】主线从前两个里选一个：

- B 站 · 西湖大学赵世钰《强化学习的数学原理》：从零讲起，重数学推导，例子多，**推荐作为主线**。
  - 视频：https://www.bilibili.com/video/BV1sd4y167NS/
  - 书和课件：https://github.com/MathFoundationRL/Book-Mathmatical-Foundation-of-Reinforcement-Learning
- B 站 · 王树森《深度强化学习》：节奏快，讲解直观。
  - B 站：https://www.bilibili.com/video/BV12o4y197US
  - YouTube 原版：https://www.youtube.com/playlist?list=PLvOO0btloRnsiqM72G4Uid0UWljikENlU
- 进阶：YouTube · UC Berkeley CS285，看 Lecture 4–6 和 9：https://www.youtube.com/playlist?list=PL_iWQOsE6TfVYGEGiAOMaOzzv41Jfm_Ps
- 进阶：弗莱堡《MPC and RL》课程的后半部分，讲 RL 和 MPC 之间的联系（链接见 Step 2.3）

【读】

- PPO 原论文（Schulman et al., 2017）
- 《The 37 Implementation Details of Proximal Policy Optimization》：https://iclr-blog-track.github.io/2022/03/25/ppo-implementation-details/

【验收】

- [ ] 手推贝尔曼方程和策略梯度定理
- [ ] 能讲清 PPO 里的 clip 和 GAE，以及为什么要对 advantage 做归一化

### Step 5.2　动手：PPO + 无人机 RL（W42–W44）

【跑】

- **CleanRL**：单文件的 PPO 实现。把连续动作版逐行加注释，然后自己重写一遍。
  - 代码：https://github.com/vwxyzjn/cleanrl
  - 文档：https://docs.cleanrl.dev/rl-algorithms/ppo/
- **gym-pybullet-drones**（PyBullet + Gymnasium + SB3）：

```bash
git clone https://github.com/learnsyslab/gym-pybullet-drones.git && cd gym-pybullet-drones
conda create -n drones python=3.12 -y && conda activate drones
sudo apt install -y build-essential           # Python 3.10 以上需要现场编译 pybullet
pip3 install -e .
cd gym_pybullet_drones/examples
python3 pid.py                                 # 先看 PID 控制的效果
python3 learn.py                               # 用 PPO 训练悬停
LATEST_MODEL=$(ls -t results | head -n 1)
python3 play.py --model_path "results/${LATEST_MODEL}/best_model.zip"
```

- **RotorPy** 的 PPO 悬停例程：examples 目录下的 `ppo_hover_train.py` 和 `ppo_hover_eval.py`。
- **learning-to-fly**：在笔记本上几十秒就能训练出可以上真机的四旋翼策略。
  - 运行 `docker run -it --rm -p 8000:8000 arpllab/learning_to_fly`，然后用浏览器打开 http://localhost:8000
  - 代码：https://github.com/arplaboratory/learning-to-fly

【改】

- 任务从悬停改成轨迹跟踪。奖励由位置误差、姿态和控制量平滑度组成。
- 加入 domain randomization，随机化质量、推力系数和电机延迟。

【验收】

- [ ] 自己写的 PPO 在 Pendulum 上能收敛
- [ ] 无人机轨迹跟踪策略的训练曲线和回放视频

### Step 5.3　RL 与 MPC 的公平对比（W45–W47）

【读】Song et al., “Reaching the limit in autonomous racing: Optimal control versus reinforcement learning”, Science Robotics 2023：https://www.science.org/doi/10.1126/scirobotics.adg1462

【做】对比条件保持一致：同一个仿真器（RotorPy 或 gym-pybullet-drones）、同一条 8 字轨迹、同样的风扰和质量失配。

| 指标 | RL（PPO） | NMPC | NMPC+DOB |
|---|---|---|---|
| 位置 RMSE（无扰动 / 有扰动） | | | |
| 模型失配 ±20% 时的鲁棒性 | | | |
| 每一步的计算时间 | | | |
| 训练或调参成本 | | | |
| 约束满足情况（推力、倾角） | | | |

【产出】写一篇技术报告或博客《同一任务下的 RL 与 MPC：各自适合什么场景？》。这是面试时最好讲的故事之一。

### Step 5.4　（可选）GPU 并行 RL 与足式机器人（W48–W49）

**显存 16 GB 以上：**

- 按官方文档安装 Isaac Lab：https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/index.html
- 跑 unitree_rl_lab 的 Go2 或 G1 速度跟踪任务：https://github.com/unitreerobotics/unitree_rl_lab

```bash
# 在 unitree_rl_lab 目录下，先按其 README 安装，再查看可用任务名
python scripts/list_envs.py
python scripts/rsl_rl/train.py --headless --task Unitree-Go2-Velocity
python scripts/rsl_rl/play.py --task Unitree-Go2-Velocity
```

- 训练完后，用 unitree_mujoco 做 sim2sim 验证。
- 无人机方向可以试 OmniDrones（基于 Isaac Sim）：https://github.com/btx0424/OmniDrones

**显存不够：**

- 用 MuJoCo Playground（JAX/MJX，单张显卡几分钟就能训练完）。
  - 主页：https://playground.mujoco.org/
  - GitHub：https://github.com/google-deepmind/mujoco_playground

**想从 MPC 平滑过渡到足式机器人：**

- 看 legged_control（OCS2 NMPC + WBC）：https://github.com/qiayuanl/legged_control

---

## 阶段 6　创新：MPC × 学习（W50–W62｜2027-09-06 → 12-05）

> 目标：研二上学期把论文 1 投出去，同时做出第二个有创新点的工作，作为论文 2 或毕业论文的核心章节。

### Step 6.1　精读并复现一篇（W50–W53）

按下表顺序读。每篇写一页笔记，包括：问题、方法、实验、局限、我能改什么。

| 论文 / 项目 | 核心思想 | 代码 |
|---|---|---|
| Data-Driven MPC for Quadrotors（RA-L 2021） | 用高斯过程学习残差（气动）动力学，嵌入 acados NMPC；高速时跟踪误差最多降低约 70% | https://github.com/uzh-rpg/data_driven_mpc （ROS 1） |
| Real-time Neural MPC（RA-L 2023） | 把 PyTorch 大网络作为 MPC 的动力学模型，同时保证实时性 | https://github.com/TUM-AAS/neural-mpc ；框架 https://github.com/Tim-Salzmann/l4casadi |
| Policy Search for MPC / high_mpc（IROS 2020，T-RO） | 用策略搜索学习 MPC 的高层决策变量 | https://uzh-rpg.github.io/high_mpc/ |
| Actor-Critic MPC（ICRA 2024） | 把可微 MPC 放进 actor-critic RL 框架；实机飞到 14 m/s | https://rpg.ifi.uzh.ch/docs/ICRA24_Romero.pdf |
| safe-control-gym | 在同一个基准上比较 LQR、GP-MPC、PPO/SAC 和安全层 | https://github.com/learnsyslab/safe-control-gym |

【跑】推荐路线：先跑通 L4CasADi 的 acados 例子（`examples/acados.py`，把 PyTorch 模型作为 acados 的动力学），再把“NN 残差动力学”接进你自己的 px4-mpc 模型，先在仿真里验证。

```bash
# L4CasADi 安装（CPU 版，按官方 README）
pip install "torch>=2.0" --index-url https://download.pytorch.org/whl/cpu
pip install "setuptools>=68.1" "scikit-build>=0.17" "cmake>=3.27" "ninja>=1.11"
pip install l4casadi --no-build-isolation
```

> 小网络（隐藏层少于 3 层 × 64）建议用 `NaiveL4CasADiModule`。它会生成纯 C 的计算图，适合在机载电脑上实时运行。

### Step 6.2　选题（W54–W55）

从附录 D 的创新点子库里选一个，写一页纸，内容包括：

- 要解决的问题
- 方法
- 基线
- 评价指标
- 需要的硬件
- 风险和退路

写完和导师确认。

### Step 6.3　实验与写作（W56–W62）

- 按顺序推进：基线 → 方法 v1 → 仿真 → 实机或 HIL → 消融实验 → 写作。
- ★ W52 前论文 1 必须投出；论文 2 初稿在 W62 前完成。

---

## 阶段 7　二选一：嵌入式部署 或 全链路自主（W60–W66｜2027-11-15 → 2028-01-02）

> 这个阶段做出第三个项目，决定你的差异化标签：选 A 突出“嵌入式 + 控制”，选 B 突出“完整的自主系统”。

### Track A：把 MPC 或 RL 策略装进单片机（推荐给有嵌入式背景的你）

【看】

- YouTube · Shawn Hymel / Digi-Key《Introduction to RTOS》共 12 集，讲任务调度、队列、互斥锁、优先级反转等。视频链接和代码都在：https://github.com/ShawnHymel/introduction-to-rtos
- 或者 B 站 · 正点原子《手把手教你学 FreeRTOS》：https://www.bilibili.com/video/BV1DA411b75v/

【跑】

- **TinyMPC**：面向单片机的 MPC 求解器，获 ICRA 2024 最佳自动化论文奖。先跑 Python 接口的例子，再做代码生成。https://tinympc.org
- **Crazyflie 上的 TinyMPC 固件**：https://github.com/RoboticExplorationLab/tinympc-crazyflie-firmware 或 https://github.com/A2R-Lab/tinympc-crazyflie
- **Crazyswarm2**：Crazyflie 的 ROS 2 软件栈。https://imrclab.github.io/crazyswarm2/
- （可选）**micro-ROS**：按官方 micro_ros_setup 仓库的 README 入门，https://github.com/micro-ROS/micro_ros_setup

【做】

- 在 STM32（Crazyflie 或你自己的板子）上跑 MPC，测控制频率、单次求解耗时、RAM 和 Flash 占用。
- 进阶：用 NMPC 生成数据，训练一个小 MLP 来近似 MPC（模仿学习），部署到单片机上，和 TinyMPC 比较精度与耗时。

【验收】

- [ ] 单片机上的 MPC 达到 100 Hz 以上；达不到就给出瓶颈分析
- [ ] 资源占用表和演示视频

### Track B：全链路自主飞行（规划 + 定位 + 你的 NMPC）

【看】

- B 站 · EGO-Planner 代码框架精讲，系列里还有 A* 和 minimum snap：https://www.bilibili.com/video/BV1JL4116731/
- Fast-Drone-250 课程第 9–11 课：EGO-Planner 参数、VINS 标定、实机实验
- （付费，可选）深蓝学院 · 高飞《移动机器人运动规划》：https://www.shenlanxueyuan.com/course/575

【跑】

- **EGO-Planner 仿真**：基于 ROS 1，3 分钟就能跑起来；Ubuntu 22.04 上用 Docker 跑 Noetic。
  - GitHub：https://github.com/ZJU-FAST-Lab/ego-planner
  - Gitee 镜像：https://gitee.com/iszhouxin/ego-planner
- **ROS 2 版本**：EGO-Swarm 的 ros2_version 分支：https://github.com/ZJU-FAST-Lab/ego-planner-swarm/tree/ros2_version
- **定位**：激光用 FAST-LIO（https://github.com/hku-mars/FAST_LIO ），视觉用 VINS-Fusion（https://github.com/HKUST-Aerial-Robotics/VINS-Fusion ）

```bash
# 在 Ubuntu 22.04 上用 Docker 跑 ROS 1 Noetic（给 EGO-Planner 原版用）
xhost +local:root
docker run -it --net=host \
  -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 \
  -v /tmp/.X11-unix:/tmp/.X11-unix -v ~/docker_ws:/root/ws \
  osrf/ros:noetic-desktop-full bash
# 以下在容器内执行
apt update && apt install -y libarmadillo-dev git
cd /root/ws && git clone https://github.com/ZJU-FAST-Lab/ego-planner.git
cd ego-planner && catkin_make && source devel/setup.bash
roslaunch ego_planner simple_run.launch
```

【做】整条链路：规划器输出轨迹 → 你的 NMPC 跟踪 → 通过 uXRCE-DDS 发给 PX4。先在仿真里跑通，再上真机穿越障碍。

【验收】

- [ ] 仿真中能在未知环境里自主避障并到达目标
- [ ] （条件允许时）实机穿越障碍的视频

---

## 阶段 8　求职冲刺（W64–W80｜2027-12-13 → 2028-04-09，之后是暑期实习和秋招）

| 时间 | 任务 |
|---|---|
| W64–W66 | 写简历 v1（写法见附录 F）；精修 GitHub 上三个项目的 README：架构图、GIF 或视频、复现步骤、实验数据 |
| W65–W67 | 准备面试基础知识。C++：内存模型、智能指针、虚函数、STL、并发；操作系统和网络：小林 coding（https://xiaolincoding.com/ ）；ROS 2：DDS、QoS、executor、零拷贝；控制：附录 G 的问答 |
| W67 起 | LeetCode 热题 100 刷第二遍；每周做 2 次限时模拟 |
| W68 | 投寒假日常实习（BOSS 直聘、牛客、内推），济南本地的机器人和无人机公司也可以投 |
| W69–W73 | 寒假（2028-01-26 春节）：去日常实习，或者集中冲项目；找师兄或同学模拟面试 |
| W74 | 整理暑期实习信息表：公司、岗位、网申截止、笔试时间、内推码 |
| W75–W78 | 3 月：暑期实习网申、笔试、面试（大厂集中在 3–4 月） |
| W79–W80 | 4 月：复盘面经，补短板 |
| 2028-06 ～ 08 | 暑期实习，争取转正 |
| 2028-07 ～ 10 | 秋招：7–8 月提前批，9–10 月正式批 |


---

# 第三部分　附录

## 附录 A　复现报告模板（每个 GitHub 项目写一份）

```markdown
# 复现报告：<项目名>
- 仓库 / commit：<url>@<hash>
- 论文：<标题, 会议或期刊, 年份>
- 环境：Ubuntu 22.04 / ROS 2 Humble / Python 3.x / GPU 型号 / 关键依赖版本

## 1. 目标：要复现哪张图、哪个指标
## 2. 步骤：从 clone 到出结果的完整命令
## 3. 报错与解决（最有价值的部分）
| 现象 | 原因 | 解决 |
## 4. 结果：我的结果与论文结果对比（表或图），分析差异
## 5. 代码结构笔记：核心文件、数据流、关键参数
## 6. 能改进的地方：列 3 个想法（进入“改 / 创”环节）
```

## 附录 B　周报模板（每周六写，15 分钟）

```markdown
# 第 W__ 周（yyyy-mm-dd ～ mm-dd）
- 计划：Step __（完成 / 进行中 / 未完成）
- 本周产出：代码链接 / 图 / 笔记
- 卡点：问题描述 + 已经试过的办法 + 下周怎么解决
- 刷题：本周 __ 题，累计 __ 题
- 用时：科研 __ h / 学习 __ h
- 下周计划：不超过 3 条
```

## 附录 C　实验数据规范

- **rosbag 命名**：`日期_平台_控制器_轨迹_工况_序号`，例如 `20270415_x500_nmpc-dob_fig8_wind_03`。
- **每次实验都要记录**：参数 YAML、固件版本、电池电压、风况、备注。
- **统一的评价指标**：位置 RMSE、最大误差、求解时间（均值 / P99 / 最大值）、CPU 占用、控制输入的平滑度。
- **统一出图**：所有图都用同一个 `eval_bag.py` 生成，同一个控制器在所有图里用同一种颜色。

## 附录 D　创新点子库（按稳妥程度排序）

| # | 题目 | 核心做法 | 需要 | 难度 | 适合的产出 |
|---|---|---|---|---|---|
| 1 | 残差学习 NMPC 抗扰跟踪 | 用 GP 或小 MLP 学习未建模的气动和挂载扰动，通过 L4CasADi 嵌入 acados | 仿真 + 真机 | ★★ | 论文 1 或 2（最稳） |
| 2 | 扰动观测器 + offset-free NMPC | 用 DOB 估计外力，实时更新模型参数；和积分型、无偏 MPC 做对比 | 仿真 + 真机 | ★★ | 论文 1 |
| 3 | 神经网络近似 MPC，部署到单片机 | 用 NMPC 生成的数据做模仿学习，部署到 STM32；和 TinyMPC 比较精度与耗时 | Crazyflie 或 STM32 | ★★★ | 论文 + 嵌入式项目 |
| 4 | 学习 MPC 的权重和时域 | 参考 high_mpc，用策略搜索或贝叶斯优化自动调 Q、R、N，适应不同任务 | 仿真 | ★★★ | 论文 2 |
| 5 | 用 MPC 给 RL 做安全滤波 | RL 输出动作，MPC 或 CBF 把它投影到安全集，保证推力、倾角、禁飞区等约束 | 仿真 | ★★★ | 论文 2 |
| 6 | 事件触发或变时域 MPC | 误差小的时候降低求解频率或缩短时域，减少机载算力占用 | 仿真 + 机载电脑 | ★★ | 工程类论文 |

> 选题建议：第一篇做 1 或 2，稳妥地拿到毕业要求的论文；第二篇再从 3、4、5 里挑一个，冲创新点。

## 附录 E　常见问题速查 + 飞前检查清单

| 现象 | 原因 | 解决 |
|---|---|---|
| Gazebo 卡顿，Isaac 启动不了 | 用的虚拟机，或者显卡驱动有问题 | 装双系统；安装推荐的专有驱动；Isaac 需要 RTX 显卡 |
| ROS 2 订阅不到 PX4 的话题 | QoS 不匹配 | 用 `rclcpp::SensorDataQoS()` |
| 话题名或消息字段对不上 | px4_msgs 和固件版本不一致 | 切到同一个 release；PX4 v1.16 起可以用 translation node |
| 位置或姿态方向反了 | NED/FRD 和 ENU/FLU 坐标系混用 | 统一坐标系，用 px4_ros_com 的 frame_transforms |
| 仿真里无法解锁 | 没有连 QGC 或遥控器 | 打开 QGC；或者按 px4-mpc README 设置仿真参数 |
| 自动退出 offboard 模式 | OffboardControlMode 发送频率低于约 2 Hz | 用定时器持续发送 |
| acados 找不到库或 t_renderer | 环境变量没设，或者网络问题 | 设置 ACADOS_SOURCE_DIR 和 LD_LIBRARY_PATH；手动下载 t_renderer |
| pip 装包慢或失败 | 网络问题 | 用清华镜像；用 conda 或 venv 隔离环境 |
| ROS 1 老项目编译不过 | Ubuntu 22.04 装不了 Noetic | 用 Docker 镜像 `osrf/ros:noetic-desktop-full` |
| Isaac Lab 显存不足 | 官方建议 16 GB 以上显存 | 减少并行环境数；或者换 MuJoCo Playground |
| 真机炸机 | 急着提速，没有兜底机制 | 系绳或网笼、输出限幅、超时兜底、安排安全员、起飞前检查 |

**飞前检查清单（每次都要过一遍）：**

- [ ] 电池电压足够，电池固定牢靠
- [ ] 桨叶方向正确，已拧紧
- [ ] 遥控器的手动、定点、急停开关都已测试
- [ ] 失控保护参数设置正确
- [ ] 定位质量正常（EKF 状态健康）
- [ ] 机载程序的限幅和超时兜底已开启
- [ ] 场地内没有无关人员，安全员就位

## 附录 F　简历项目写法（示例，数字要换成你自己的实测值）

> 基于 acados 实现四旋翼 NMPC（RTI，20 步预测时域），部署在 Jetson Orin NX 上，以 100 Hz 运行，平均求解时间 3.2 ms、P99 为 5.1 ms；通过 uXRCE-DDS 与 PX4 通信；加入扰动观测器后，挂载 200 g 偏心负载时，实机 8 字轨迹的 RMSE 降低 35%。

写法要点：

- 一句话讲清“做了什么 + 用了什么技术 + 量化结果”。
- 技术关键词要在简历里出现：PX4、ROS 2、NMPC、acados、CasADi、C++17、Isaac Lab、MuJoCo、sim2real、实机部署、实时性。
- 每个项目附上 GitHub 链接和视频链接。

## 附录 G　控制方向面试高频问答（每题都写一份书面答案）

1. MPC 相比 PID、LQR 的优势和代价分别是什么？
2. 预测时域 N 和采样时间怎么选？它们和计算量是什么关系？
3. 求解超时或者无解，你怎么处理？
4. 怎么保证稳定性？终端代价和终端约束是什么？
5. 模型误差和外部扰动怎么处理？offset-free MPC 怎么实现？
6. RTI 是什么？为什么它能做到实时？warm start 怎么做？
7. 你的 MPC 输出给了 PX4 的哪一层？为什么这样选？
8. 真机和仿真的差别在哪里？你遇到的最大问题是什么，怎么定位出来的？
9. RL 和 MPC 各自适合什么场景？两者怎么结合？
10. 系统时延怎么测量？怎么补偿？

C++ 和 ROS 2 方向的高频题可以参考牛客网的《C++ 机器人方向常考面试题汇总》：https://www.nowcoder.com/discuss/886917509725663232

## 附录 H　资源总表

### H.1　B 站

| 名称 | 用在哪一步 | 链接 |
|---|---|---|
| 鱼香ROS《ROS 2 机器人开发从入门到实践》 | 0.2、1.3 | https://www.bilibili.com/video/BV1GW42197Ck/ |
| 古月居《ROS2 入门 21 讲》 | 1.3 | https://www.bilibili.com/video/BV16B4y1Q7jQ/ |
| 小彭老师（现代 C++、并行编程） | 1.1 | https://space.bilibili.com/263032155 |
| MIT Missing Semester 中文字幕（刘黑黑a） | 1.2 | https://space.bilibili.com/518734451 |
| DR_CAN（控制理论、MPC） | 2.1、2.2 | https://space.bilibili.com/230105574 |
| DR_CAN【MPC 模型预测控制器】第 1 集 | 2.2 | https://www.bilibili.com/video/BV1cL411n7KV/ |
| MPC 从公式到代码（Matlab + C++） | 2.2 | https://www.bilibili.com/video/BV1U54y1J7wh/ |
| 弗莱堡 MPC 与 RL 课程（双语搬运） | 2.3、5.1 | https://www.bilibili.com/video/BV1EQ2TY5EZt/ |
| CasADi 教程（手搓 OCS2 类似物系列，C++ 向） | 2.3（选看） | https://www.bilibili.com/video/BV1nwd6YFErJ/ |
| 北航全权《多旋翼飞行器设计与控制》 | 3.1 | https://www.bilibili.com/video/BV1my4y1v7yC/ |
| 宾大 Vijay Kumar《Robotics: Aerial Robotics》（Coursera 课程搬运） | 3.1 | https://www.bilibili.com/video/BV1Xa4y1h7Xt/ |
| 浙大 FAST Lab《从 0 制作自主空中机器人》 | 4.1、7B | https://www.bilibili.com/video/BV1WZ4y167me/ |
| 赵世钰《强化学习的数学原理》 | 5.1 | https://www.bilibili.com/video/BV1sd4y167NS/ |
| 王树森《深度强化学习》 | 5.1 | https://www.bilibili.com/video/BV12o4y197US |
| 正点原子《手把手教你学 FreeRTOS》 | 7A | https://www.bilibili.com/video/BV1DA411b75v/ |
| EGO-Planner 代码框架精讲 | 7B | https://www.bilibili.com/video/BV1JL4116731/ |
| LeetCode 热题 100 逐题讲解（C++） | 1.4 | https://www.bilibili.com/video/BV1xb4y1b7KM/ |

### H.2　YouTube 与课程网站

| 名称 | 用在哪一步 | 链接 |
|---|---|---|
| The Cherno C++ | 1.1 | https://www.youtube.com/playlist?list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb |
| MIT Missing Semester | 1.2 | https://missing.csail.mit.edu/ |
| Articulated Robotics（ROS 2 移动机器人） | 1.3 | https://www.youtube.com/playlist?list=PLunhqkrRNRhYAffV8JDiFOatQXuU-NnxT |
| Steve Brunton《Control Bootcamp》 | 2.1 | https://www.youtube.com/playlist?list=PLMrJAkhIeNNR20Mz-VpzgfQs5zrYi085m |
| MathWorks《Understanding MPC》 | 2.2 | https://www.mathworks.com/videos/understanding-model-predictive-control-part-1-why-use-mpc--1526484715269.html |
| Mehrez CasADi MPC 工作坊 | 2.3 | https://www.youtube.com/playlist?list=PLK8squHT_Uzej3UCUHjtOtm5X7pMFSgAL |
| 弗莱堡《MPC and RL》课程主页 | 2.3、5.1 | https://www.syscop.de/teaching/ss2022/model-predictive-control-and-reinforcement-learning |
| MIT Underactuated Robotics（讲义 + 视频） | 拓展 | https://underactuated.csail.mit.edu/ |
| 王树森 DRL（YouTube 原版） | 5.1 | https://www.youtube.com/playlist?list=PLvOO0btloRnsiqM72G4Uid0UWljikENlU |
| UC Berkeley CS285（2023） | 5.1 | https://www.youtube.com/playlist?list=PL_iWQOsE6TfVYGEGiAOMaOzzv41Jfm_Ps |
| Shawn Hymel《Introduction to RTOS》 | 7A | https://github.com/ShawnHymel/introduction-to-rtos |
| 深蓝学院 · 高飞《移动机器人运动规划》（付费） | 7B | https://www.shenlanxueyuan.com/course/575 |

### H.3　GitHub 项目（按学习顺序排列）

| 项目 | 用途 | 链接 |
|---|---|---|
| PX4-Autopilot / px4_msgs / px4_ros_com | PX4 仿真与 ROS 2 接口 | https://github.com/PX4/PX4-Autopilot |
| Micro-XRCE-DDS-Agent | PX4 与 ROS 2 之间的桥 | https://github.com/eProsima/Micro-XRCE-DDS-Agent |
| parallel101/course | 现代 C++ 课件和代码 | https://github.com/parallel101/course |
| ros2/demos | 组件、进程内通信、生命周期节点 | https://github.com/ros2/demos |
| ros2_control_demos | ros2_control 入门 | https://github.com/ros-controls/ros2_control_demos |
| leetcode-master | 刷题顺序 | https://github.com/youngyangyang04/leetcode-master |
| MPC-and-MHE（Mehrez） | CasADi MPC 入门 | https://github.com/MMehrez/MPC-and-MHE-implementation-in-MATLAB-using-Casadi |
| acados | 实时 NMPC 求解器 | https://github.com/acados/acados |
| PythonRobotics | MPC 路径跟踪 | https://github.com/AtsushiSakai/PythonRobotics |
| RotorPy | 四旋翼动力学与控制，也能做 RL | https://github.com/spencerfolk/rotorpy |
| DISCOWER/px4-mpc | ROS 2 + acados + PX4 的 MPC（**阶段 3 主复现对象**） | https://github.com/DISCOWER/px4-mpc |
| px4-offboard | ROS 2 offboard 示例与 RViz 可视化 | https://github.com/Jaeyoung-Lim/px4-offboard |
| RPS98/mpc | acados 四旋翼 MPC（Python + C++） | https://github.com/RPS98/mpc |
| nmpc_acados_px4 | 另一个 ROS 2 + PX4 + acados 的 NMPC 参考 | https://github.com/evannsmc/nmpc_acados_px4 |
| Fast-Drone-250 | 自主无人机软硬件全套（ROS 1） | https://github.com/ZJU-FAST-Lab/Fast-Drone-250 |
| CleanRL | 单文件 PPO | https://github.com/vwxyzjn/cleanrl |
| gym-pybullet-drones | 无人机 RL 入门 | https://github.com/learnsyslab/gym-pybullet-drones |
| learning-to-fly | 几十秒训练四旋翼策略 | https://github.com/arplaboratory/learning-to-fly |
| OmniDrones | Isaac Sim 无人机 RL | https://github.com/btx0424/OmniDrones |
| Isaac Lab | GPU 并行机器人学习框架 | https://github.com/isaac-sim/IsaacLab |
| unitree_rl_lab | 宇树机器人 RL（基于 Isaac Lab） | https://github.com/unitreerobotics/unitree_rl_lab |
| MuJoCo Playground | JAX/MJX 版 RL，显存要求低 | https://github.com/google-deepmind/mujoco_playground |
| legged_control | 足式机器人 NMPC + WBC | https://github.com/qiayuanl/legged_control |
| data_driven_mpc | GP 残差 MPC（UZH） | https://github.com/uzh-rpg/data_driven_mpc |
| neural-mpc / l4casadi | 神经网络 MPC（TUM） | https://github.com/Tim-Salzmann/l4casadi |
| safe-control-gym | 安全学习控制基准 | https://github.com/learnsyslab/safe-control-gym |
| TinyMPC | 单片机上的 MPC | https://tinympc.org |
| tinympc-crazyflie-firmware | Crazyflie 上的 TinyMPC 固件 | https://github.com/RoboticExplorationLab/tinympc-crazyflie-firmware |
| Crazyswarm2 | Crazyflie 的 ROS 2 软件栈 | https://github.com/IMRCLab/crazyswarm2 |
| EGO-Planner / EGO-Swarm（含 ROS 2 分支） | 无人机局部规划 | https://github.com/ZJU-FAST-Lab/ego-planner-swarm |
| FAST-LIO / VINS-Fusion | 激光 / 视觉里程计 | https://github.com/hku-mars/FAST_LIO |
| PlotJuggler | 数据可视化 | https://github.com/facontidavide/PlotJuggler |

### H.4　文档与书

| 名称 | 链接 |
|---|---|
| PX4 ROS 2 User Guide | https://docs.px4.io/main/en/ros2/user_guide |
| PX4 Controller Diagrams | https://docs.px4.io/main/en/flight_stack/controller_diagrams |
| PX4 Gazebo 世界（含 windy 有风世界） | https://docs.px4.io/main/en/sim_gazebo_gz/worlds |
| ROS 2 Humble 文档 | https://docs.ros.org/en/humble/ |
| acados 安装 / Python 接口 | https://docs.acados.org/installation/ |
| Rawlings《Model Predictive Control》（免费 PDF） | https://sites.engineering.ucsb.edu/~jbraw/mpc/ |
| 赵世钰《强化学习的数学原理》（书 + 课件） | https://github.com/MathFoundationRL/Book-Mathmatical-Foundation-of-Reinforcement-Learning |
| 37 Implementation Details of PPO | https://iclr-blog-track.github.io/2022/03/25/ppo-implementation-details/ |
| Isaac Lab 安装 | https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/index.html |
| 代码随想录 | https://www.programmercarl.com/ |
| 小林 coding（操作系统 / 网络） | https://xiaolincoding.com/ |
| 《Effective Modern C++》 / 《C++ Concurrency in Action》 | 纸质书或电子书 |

## 附录 I　周计划总表（W1–W80）

> 每周固定动作：每天刷题 30–45 分钟；每周六写周报。逐周打勾请用配套的 `学习计划打卡表.xlsx`。

| 周次 | 日期 | 阶段 / Step | 本周任务 | 主要资源 | 验收 |
|---|---|---|---|---|---|
| W1 | 2026-09-28～10-04 | 阶段0 · 0.1–0.2 | 国庆：自测定位；装 Ubuntu 22.04 双系统、显卡驱动、VSCode、Git/SSH；鱼香一键装 ROS 2 Humble | 鱼香ROS 1.2.4 安装视频；fishros 一键脚本 | talker/listener 互通；自测表填完 |
| W2 | 2026-10-05～10-11 | 阶段0 · 0.3–0.4 | 装 PX4 + Gazebo + XRCE-DDS Agent + QGC，跑通官方 offboard 起飞；建学习仓库和周报模板；开始刷题 | PX4 ROS 2 User Guide；px4_ros_com | Gazebo 中飞机升到 5 m；提交第一份周报 |
| W3 | 2026-10-12～10-18 | 阶段1 · 1.1 / 1.2 | 小彭老师 L1（CMake/Git）+ L2（STL/RAII）；Missing Semester：Shell、Git；搭 CMake + GTest 工程模板 | parallel101/course；missing-semester-cn | 模板工程 ctest 通过 |
| W4 | 2026-10-19～10-25 | 阶段1 · 1.1 / 1.2 | The Cherno：智能指针、移动语义；实现线程安全环形缓冲区 + 单测；Missing Semester：调试与性能分析 | The Cherno 播放列表；gdb / valgrind | 单测全过，valgrind 无泄漏 |
| W5 | 2026-10-26～11-01 | 阶段1 · 1.1 / 1.3 | 小彭 L5 多线程；实现线程池；鱼香 ROS 2 第 2–3 章（节点、话题）快速过 | parallel101 L5；鱼香 BV1GW42197Ck | 线程池 benchmark；不看教程手写 pub/sub |
| W6 | 2026-11-02～11-08 | 阶段1 · 1.1 / 1.3 | 异步日志器 + perf 找热点；《Effective Modern C++》第 4/5/7 章；鱼香第 4–5 章（服务、launch、参数、rosbag） | EMC++；鱼香 BV1GW42197Ck | 日志器吞吐数据；讲清三种智能指针 |
| W7 | 2026-11-09～11-15 | 阶段1 · 1.3 | 官方文档：Executors、Callback groups、QoS；跑 ros2/demos 的 composition、intra_process_demo | docs.ros.org Humble；ros2/demos | 能解释 QoS 不匹配为什么收不到数据 |
| W8 | 2026-11-16～11-22 | 阶段1 · 1.3 | lifecycle 示例；ros2_control_demos 的 example_1；Articulated Robotics 的 ros2_control 几集 | ros2_control_demos；Articulated Robotics | RRBot 在 RViz 中动起来 |
| W9 | 2026-11-23～11-29 | 阶段1/2 · 1.3 / 2.1 | 写 C++ 画圆 offboard 节点（SensorDataQoS、参数、launch）；DR_CAN 现代控制（状态空间、可控可观） | 手册 Step 1.3 骨架代码；DR_CAN 频道 | SITL 中无人机画圆 |
| W10 | 2026-11-30～12-06 | 阶段1/2 · 1.3 / 2.1 | rosbag2 录制 + PlotJuggler 画误差曲线 + 写 README；Brunton Control Bootcamp（LQR/Kalman）+ Python 倒立摆 LQR | PlotJuggler；Brunton 播放列表 | 误差曲线图；LQR 仿真收敛 |
| W11 | 2026-12-07～12-13 | 阶段2 · 2.2 | DR_CAN MPC 第 1–4 集；MathWorks Understanding MPC 第 1–5 集；Rawlings 第 1 章 | BV1cL411n7KV；MathWorks；Rawlings PDF | 手推 MPC 转 QP 的形式，拍照放进笔记 |
| W12 | 2026-12-14～12-20 | 阶段2 · 2.2 | 从零写线性 MPC（CVXPY/OSQP）：先双积分器，再 2D 四旋翼线性化模型；和 LQR 对比；加输入和状态约束 | 手册 Step 2.2 代码；MPC 从公式到代码视频 | 有约束时 MPC 与 LQR 的对比图 |
| W13 | 2026-12-21～12-27 | 阶段2 · 2.3 | Mehrez CasADi 工作坊；用 multiple shooting 写差速小车 NMPC；跑通 PythonRobotics 的 MPC | Mehrez 播放列表 + 仓库；PythonRobotics | NMPC 小车跟踪曲线 |
| W14 | 2026-12-28～01-03 | 阶段2 · 2.3 | Freiburg Lecture 3 数值优化；安装 acados，跑 minimal_example_ocp.py；用 acados SQP_RTI 重写 NMPC；做求解时间对比表 | syscop 课程页；acados 文档 | IPOPT 与 SQP_RTI 的求解时间表 |
| W15 | 2027-01-04～01-10 | 阶段2 · 2.4 | 期末周（任务较轻）：Rawlings 第 2 章，终端代价/终端约束与稳定性，写笔记 | Rawlings PDF | 一页稳定性笔记 |
| W16 | 2027-01-11～01-17 | 阶段2 · 2.4 | 期末周（任务较轻）：软约束 + 扰动估计，实现 offset-free MPC；写博客《从零实现 MPC》 | 手册 Step 2.4 | 质量失配 20% 时稳态误差收敛到 0 |
| W17 | 2027-01-18～01-24 | 阶段3 · 3.1 | 寒假开始：全权《多旋翼》第 5、6、11 讲；安装 RotorPy，跑 SE(3) 控制和 minimum snap 例程 | BV1my4y1v7yC；RotorPy | RotorPy 例程跑通 |
| W18 | 2027-01-25～01-31 | 阶段3 · 3.1 / 3.2 | 读 Lee 2010 SE(3) 和 Mellinger 2011；在 RotorPy 中接入 acados NMPC，和 SE(3) 控制器对比；读 PX4 Controller Diagrams | 两篇论文；PX4 文档 | NMPC 与 SE(3) 对比图 |
| W19 | 2027-02-01～02-07 | 阶段3 · 3.2 | 春节周（任务较轻）：读 PX4 源码 mc_pos_control / mc_att_control / mc_rate_control；画控制级联图，标出 MPC 可以接入的层级 | PX4-Autopilot 源码 | 一张架构图 + 一页笔记 |
| W20 | 2027-02-08～02-14 | 阶段3 · 3.3 | 复现 DISCOWER/px4-mpc（SITL + RViz 目标点）；通读模型、求解器、节点代码，写复现报告 | px4-mpc README | SITL 中 MPC 跟踪目标点 |
| W21 | 2027-02-15～02-21 | 阶段3 · 3.4 | 目标点改成时变轨迹（圆、8 字）；写 eval_bag.py 自动评估（RMSE、求解时间分布） | 手册 Step 3.4 | 一个命令出图 |
| W22 | 2027-02-22～02-28 | 阶段3 · 3.4 | Python 节点改写成 C++ 节点（acados 生成的 C 代码 + rclcpp）；和 PX4 默认位置控制器对比 | acados C 接口；RPS98/mpc | C++ 节点稳定运行在 50 Hz 以上 |
| W23 | 2027-03-01～03-07 | 阶段3/4 · 3.5 / 4.1 | 开学：windy 有风世界 + 质量失配；实现扰动观测器 / offset-free NMPC；清点实验室硬件，确定真机方案 | PX4 Gazebo Worlds | 抗扰对比的初步结果 |
| W24 | 2027-03-08～03-14 | 阶段3/4 · 3.5 / 4.1 | 对比实验（PID / NMPC / NMPC+DOB），出图出表；看 Fast-Drone-250 第 1–4 课 | BV1WZ4y167me | 仿真结果表 |
| W25 | 2027-03-15～03-21 | 阶段3/4 · 3.5 / 4.1 | 写仿真章节初稿；组装、检查机架，刷 PX4 固件，设置遥控和失控保护 | Fast-Drone-250 文档 | 仿真章节 v0 |
| W26 | 2027-03-22～03-28 | 阶段4 · 4.1 | 按手动、定高、定点的顺序试飞；用 Flight Review 分析日志，调 PID | PX4 Flight Review | 定点悬停稳定 |
| W27 | 2027-03-29～04-04 | 阶段4 · 4.1 / 4.2 | 机载电脑安装 JetPack 6 / Ubuntu 22.04 + ROS 2 Humble + Agent；通过串口或以太网连通飞控；确定定位方案 | PX4 ROS 2 User Guide 硬件部分 | 机载电脑上能 echo 到 /fmu/out 话题 |
| W28 | 2027-04-05～04-11 | 阶段4 · 4.2 | 动捕、VIO 或 GPS 接入 EKF2（外部位姿）；测试悬停精度 | PX4 外部位置估计文档 | 悬停误差统计 |
| W29 | 2027-04-12～04-18 | 阶段4 · 4.3 | 真机 offboard 悬停（px4_ros_com 例程；安全员 + 保护网或系绳） | 附录 E 飞前检查清单 | 真机 offboard 悬停成功 |
| W30 | 2027-04-19～04-25 | 阶段4 · 4.3 | 用 PX4 位置控制跑真机低速圆，采集基线数据 | — | 基线 rosbag |
| W31 | 2027-04-26～05-02 | 阶段4 · 4.3 | 真机 NMPC 悬停（输出限幅、超时兜底、急停都要做） | — | NMPC 真机悬停 |
| W32 | 2027-05-03～05-09 | 阶段4 · 4.3 | 真机 NMPC 跑圆和 8 字，逐步提速；统计机载求解时间 | — | 实机跟踪曲线 + 求解时间直方图 |
| W33 | 2027-05-10～05-16 | 阶段4 · 4.3 / 4.4 | 抗扰实验（偏心挂载、风扇侧风）；写论文大纲，整理相关工作文献表 | Zotero | 大纲 + 文献表 |
| W34 | 2027-05-17～05-23 | 阶段4 · 4.3 / 4.4 | 补做实验，剪演示视频；写方法章节 | — | 方法章节 v0 |
| W35 | 2027-05-24～05-30 | 阶段4 · 4.4 | 写实验章节，统一图表格式 | matplotlib 统一风格 | 实验章节 v0 |
| W36 | 2027-05-31～06-06 | 阶段4 · 4.4 | 写引言、摘要、结论；导师改第一轮 | Overleaf | 全文 v1 |
| W37 | 2027-06-07～06-13 | 阶段4 · 4.4 | 按意见修改；和导师确定投稿目标 | 学院学位要求 | 全文 v2 |
| W38 | 2027-06-14～06-20 | 阶段4/5 · 4.4 / 5.1 | 论文继续修改；赵世钰 RL 第 1–3 章 | BV1sd4y167NS | 第 1–3 章笔记 |
| W39 | 2027-06-21～06-27 | 阶段4/5 · 4.4 / 5.1 | 论文定稿冲刺；赵世钰第 4–6 章 | 同上 | 第 4–6 章笔记 |
| W40 | 2027-06-28～07-04 | 阶段4/5 · 4.4 / 5.1 | ★ 论文 1 定稿；赵世钰第 7–8 章 | 同上 | 定稿 PDF |
| W41 | 2027-07-05～07-11 | 阶段5 · 5.1 | 暑假：赵世钰第 9–10 章；读 PPO 论文和 37 Implementation Details | ICLR Blog | 手推策略梯度 |
| W42 | 2027-07-12～07-18 | 阶段5 · 5.1 / 5.2 | 对照 CleanRL 的 ppo_continuous_action.py 逐行注释并重写；安装 gym-pybullet-drones，跑 pid.py 和 learn.py | CleanRL；gym-pybullet-drones | 自己写的 PPO 在 Pendulum 上收敛 |
| W43 | 2027-07-19～07-25 | 阶段5 · 5.2 | RotorPy 的 PPO 悬停例程；用 Docker 跑 learning-to-fly，体验快速训练（原生约 18 秒，Docker 里一两分钟） | RotorPy；learning-to-fly | 两个悬停策略 |
| W44 | 2027-07-26～08-01 | 阶段5 · 5.2 | 自定义轨迹跟踪奖励；加 domain randomization（质量、推力系数、电机延迟） | — | 跟踪策略的训练曲线 |
| W45 | 2027-08-02～08-08 | 阶段5 · 5.3 | 设计 RL 与 MPC 的对比实验：同一仿真器、同一条 8 字轨迹、同样的风扰 | Science Robotics 2023 | 实验设计文档 |
| W46 | 2027-08-09～08-15 | 阶段5 · 5.3 | 跑对比实验；精读 Science Robotics 2023 | — | 原始数据 |
| W47 | 2027-08-16～08-22 | 阶段5 · 5.3 | 分析结果，写技术报告或博客《同一任务下的 RL 与 MPC》 | — | 博客发布 |
| W48 | 2027-08-23～08-29 | 阶段5 · 5.4 | （可选）Isaac Lab + unitree_rl_lab 训练 Go2；显存不够就用 MuJoCo Playground | Isaac Lab 文档；unitree_rl_lab | 训练曲线 |
| W49 | 2027-08-30～09-05 | 阶段5 · 5.4 | （可选）用 unitree_mujoco 做 sim2sim，或跑 OmniDrones 悬停；整理 RL 笔记 | — | sim2sim 视频 |
| W50 | 2027-09-06～09-12 | 阶段6 · 6.1 | 研二开学：精读 data_driven_mpc、neural-mpc 两篇论文；论文 1 按目标期刊格式排版 | 论文 + 代码 | 两页精读笔记 |
| W51 | 2027-09-13～09-19 | 阶段6 · 6.1 | 跑通 L4CasADi 的 examples/acados.py；准备论文 1 投稿材料 | l4casadi | 例程跑通 |
| W52 | 2027-09-20～09-26 | 阶段6 · 6.1 | 把 NN 残差动力学接入自己的 NMPC（仿真）；★ 论文 1 投出 | — | 投稿回执 |
| W53 | 2027-09-27～10-03 | 阶段6 · 6.1 | 精读 high_mpc、Actor-Critic MPC、safe-control-gym | 三篇论文 | 三页笔记 |
| W54 | 2027-10-04～10-10 | 阶段6 · 6.2 | 从附录 D 点子库选题，和导师确认；写实验设计文档 | 手册附录 D | 一页纸方案 |
| W55 | 2027-10-11～10-17 | 阶段6 · 6.2 | 实现基线，搭数据采集流程 | — | 基线结果 |
| W56 | 2027-10-18～10-24 | 阶段6 · 6.3 | 实现方法 v1 | — | 方法代码 |
| W57 | 2027-10-25～10-31 | 阶段6 · 6.3 | 仿真实验 | — | 仿真结果 |
| W58 | 2027-11-01～11-07 | 阶段6 · 6.3 | 实机或 HIL 实验 | — | 实机结果 |
| W59 | 2027-11-08～11-14 | 阶段6 · 6.3 | 消融实验 + 对比实验 | — | 结果表 |
| W60 | 2027-11-15～11-21 | 阶段6/7 · 6.3 / 7 | 写论文 2 或毕业论文章节；选定 Track A 或 B，准备环境 | — | — |
| W61 | 2027-11-22～11-28 | 阶段6/7 · 6.3 / 7 | 继续写作；A：RTOS 视频第 1–6 集 / B：EGO-Planner 代码精讲 | ShawnHymel；BV1JL4116731 | — |
| W62 | 2027-11-29～12-05 | 阶段6/7 · 6.3 / 7 | 论文 2 初稿；A：TinyMPC Python 例子 + 代码生成 / B：ego-planner-swarm 的 ros2_version 仿真 | tinympc.org；ego-planner-swarm | 论文 2 初稿 |
| W63 | 2027-12-06～12-12 | 阶段7 · 7 | A：Crazyflie + TinyMPC 固件 / B：接入 FAST-LIO 或 VINS 定位 | tinympc-crazyflie-firmware；FAST_LIO | — |
| W64 | 2027-12-13～12-19 | 阶段7/8 · 7 / 8 | A：测单片机上 MPC 的频率和内存 / B：规划轨迹接到 NMPC 跟踪；写简历 v1 | — | 简历 v1 |
| W65 | 2027-12-20～12-26 | 阶段7/8 · 7 / 8 | A：部署神经网络近似 MPC / B：仿真跑通全链路；面试准备：C++ | — | — |
| W66 | 2027-12-27～01-02 | 阶段7/8 · 7 / 8 | Track 收尾，录演示视频；面试准备：操作系统和网络 | 小林 coding | 项目 3 视频 |
| W67 | 2028-01-03～01-09 | 阶段8 · 8 | 面试准备：ROS 2 + 控制（MPC 深挖问答）；LeetCode 热题 100 第二遍 | 附录 G | 问答文档 |
| W68 | 2028-01-10～01-16 | 阶段8 · 8 | 投寒假日常实习（BOSS 直聘、牛客、内推）；精修 GitHub | — | 投递表 |
| W69 | 2028-01-17～01-23 | 阶段8 · 8 | 寒假（2028-01-26 春节）：日常实习或集中冲项目；模拟面试 | — | — |
| W70 | 2028-01-24～01-30 | 阶段8 · 8 | 寒假（2028-01-26 春节）：日常实习或集中冲项目；模拟面试 | — | — |
| W71 | 2028-01-31～02-06 | 阶段8 · 8 | 寒假（2028-01-26 春节）：日常实习或集中冲项目；模拟面试 | — | — |
| W72 | 2028-02-07～02-13 | 阶段8 · 8 | 寒假（2028-01-26 春节）：日常实习或集中冲项目；模拟面试 | — | — |
| W73 | 2028-02-14～02-20 | 阶段8 · 8 | 寒假（2028-01-26 春节）：日常实习或集中冲项目；模拟面试 | — | — |
| W74 | 2028-02-21～02-27 | 阶段8 · 8 | 整理暑期实习信息表（公司、岗位、网申截止、笔试时间、内推码） | — | 信息表 |
| W75 | 2028-02-28～03-05 | 阶段8 · 8 | 3 月：暑期实习网申、笔试、面试 | — | — |
| W76 | 2028-03-06～03-12 | 阶段8 · 8 | 3 月：暑期实习网申、笔试、面试 | — | — |
| W77 | 2028-03-13～03-19 | 阶段8 · 8 | 3 月：暑期实习网申、笔试、面试 | — | — |
| W78 | 2028-03-20～03-26 | 阶段8 · 8 | 3 月：暑期实习网申、笔试、面试 | — | — |
| W79 | 2028-03-27～04-02 | 阶段8 · 8 | 4 月：复盘面经，补短板 | — | — |
| W80 | 2028-04-03～04-09 | 阶段8 · 8 | 4 月：复盘面经，补短板 | — | — |

### 里程碑

| 截止周 | 截止日期 | 里程碑 |
|---|---|---|
| W2 | 2026-10-11 | 仿真中第一次 offboard 起飞 |
| W10 | 2026-12-06 | ROS 2 画圆节点 + 3 个 C++ 小项目 |
| W16 | 2027-01-17 | MPC 从零实现（线性 MPC → NMPC → acados）+ 博客 |
| W22 | 2027-02-28 | px4-mpc 复现与改造完成（C++ 节点） |
| W25 | 2027-03-21 | 抗扰仿真实验完成（论文 1 仿真章节） |
| W32 | 2027-05-09 | 真机 NMPC 轨迹跟踪 |
| W40 | 2027-07-04 | 论文 1 定稿 |
| W47 | 2027-08-22 | RL 与 MPC 对比报告 |
| W52 | 2027-09-26 | 论文 1 投出 |
| W62 | 2027-12-05 | 论文 2 / 毕业论文核心章节初稿 |
| W66 | 2028-01-02 | 项目 3（嵌入式或全链路）+ 演示视频 |
| W78 | 2028-03-26 | 暑期实习网申与面试 |
| — | 2028-06 ～ 08 | 暑期实习（争取转正） |
| — | 2028-07 ～ 10 | 秋招（提前批 + 正式批） |

---

> 最后一句：**计划是用来调整的，产出是用来证明的。** 每个月对照里程碑检查一次，落后了就砍掉可选项（Step 5.4、Track 的进阶部分），但主线（阶段 2 → 3 → 4 → 6 的论文与实机）不能断。
