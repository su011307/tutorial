# 安装驱动

所有的硬件都需要驱动程序来驱动它们，激光雷达也不例外。不过好消息是，官方已经维护了一套 [SDK](https://github.com/Livox-SDK/Livox-SDK2)（软件开发工具包，顾名思义）和 [ROS2 驱动](https://github.com/Livox-SDK/livox_ros_driver2)，我们不必自动重复造轮子，只需要将它们安装并编译即可。如果想要理解下面的代码在做什么，最好有 [Git](../swe/proj-mng/git.md) 和 [CMake](../swe/proj-mng/cmake.md) 的基础

## `Livox-SDK2`

这个库是驱动程序，负责告诉 Linux 如何驱动激光雷达，不然我们的 Linux 没有办法正确识别并让激光雷达正常工作

```bash
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd Livox-SDK2/
mkdir build
cd build
cmake .. && make -j
sudo make install
```

## `livox_ros_driver2`

`Livox-SDK2` 只是告诉 Linux 系统如何驱动我们的雷达，但是却没有告诉运行在 Linux 之上的 ROS2 如何使用它。`livox_ros_driver2` 就是做这个活的

```bash
git clone https://github.com/Livox-SDK/livox_ros_driver2.git
cd livox_ros_driver2/
source /opt/ros/<ROS 版本代号>/setup.bash  # 版本代号可以 ls /opt/ros 查看, 这里默认是 humble (注意大小写)
./build.sh humble
```

# 依赖项

### 注意

如果已经按照 [教程](../swe/ros/index.md) 安装好了 ROS 2 的话，是不需要安装 [PCL]() 和 [Eigen]()（线性代数计算） 的。不过保险起见，还是执行以下下面的命令确保我们接下来依赖的库已经被正确安装了

```bash
sudo apt update && sudo apt upgrade
sudo apt install libpcl-dev libeigen-dev
```

# 安装数据处理库

激光雷达的驱动本身只是返回一堆点云数据，但是关于 [畸变](concept.md) 等的处理则需要我们负责。不过好消息是开源社区已经为我们完成了这部分内容的编写，我们不必自己动手 😄

我们会用到 [`FAST_LIO_ROS2`](https://github.com/Ericsii/FAST_LIO_ROS2) 这个库，它是 `FAST_LIO` 在 ROS 2 上的移植，并且已经帮我们完成了很多脏活累活的处理。安装它：

```bash
cd <ros2_ws>/src # cd into a ros2 workspace folder
git clone https://github.com/Ericsii/FAST_LIO_ROS2.git --recursive
cd ..
rosdep install --from-paths src --ignore-src -y
colcon build --symlink-install
. ./install/setup.bash
```