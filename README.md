# ROS-Noetic-PX4-v1.13.3-Development

ROS Noetic + PX4 v1.13.3 Development Environment Setup

基于Intel NUC11TNKi7运算平台，Ubuntu 20.04.6LTS操作系统，ROS版本Noetic，PX4版本v1.13.3。本文步骤在全新的操作系统上验证无误，最后验证时间2025/4/3。

## 1. 鱼香ROS安装ROS

采用鱼香ROS的一键安装脚本，避免繁琐的官网安装步骤和网络连接问题：

```bash
wget http://fishros.com/install -O fishros && . fishros
```

### (1) 安装ROS

依次选择“一键安装:ROS”->“更换系统源再继续安装”->“更换系统源并清理第三方源”->“noetic(ROS1)”->“noetic(ROS1)桌面版”，等待下载完成。

可以通过运行`roscore`验证安装完成，应该看到类似以下的信息：

```bash
started roslaunch server http://hydrogen-NUC11TNKi7:42121/
ros_comm version 1.17.0

SUMMARY
========
PARAMETERS
 * /rosdistro: noetic
 * /rosversion: 1.17.0

NODES

auto-starting new master
process[master]: started with pid [107531]
ROS_MASTER_URI=http://hydrogen-NUC11TNKi7:11311/

setting /run_id to 8ad8347c-1062-11f0-9527-09c83d221b3a
process[rosout-1]: started with pid [107541]
started core service [/rosout]
```

### (2) 配置rosdep

选择“一键安装:rosdep”，完成后按照指示运行：

```bash
rosdepc update
```

### (3) 配置ROS环境

选择“一键配置:ROS环境”。

### 参考

[1] ROS Wiki &nbsp; http://wiki.ros.org/noetic/Installation/Ubuntu

[2] 鱼香ROS社区 &nbsp; https://fishros.org.cn/forum/topic/20/%E5%B0%8F%E9%B1%BC%E7%9A%84%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E7%B3%BB%E5%88%97

## 2. 安装MAVROS

MAVROS是一个ROS功能包，其作用是通过MAVLink协议实现运算平台与飞控的ROS通信。

### (1) 安装mavros和mavros-extras支持

```bash
sudo apt-get install ros-noetic-mavros ros-noetic-mavros-extras
```

### (2) 通过官方脚本安装GeographicLib库

```bash
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
sudo bash ./install_geographiclib_datasets.sh
```

### 参考

[1] PX4用户文档(v1.13) &nbsp; https://docs.px4.io/v1.13/en/ros/mavros_installation.html

## 3. 安装QGroundControl地面站

QGC是PX4固件支持的地面站调试软件。

### (1) 安装依赖

```bash
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager -y
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
sudo apt install libfuse2 -y
sudo apt install libxcb-xinerama0 libxkbcommon-x11-0 libxcb-cursor-dev -y
```

### (2) 下载软件并赋予运行权限

官网下载链接：https://d176tv9ibo4jno.cloudfront.net/latest/QGroundControl.AppImage

下载完成后，赋予软件运行权限：

```bash
chmod +x ./QGroundControl.AppImage
```

之后可以通过双击运行软件，或者运行`./QGroundControl.AppImage`。

### 参考

[1] QGC用户文档 &nbsp; https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html

## 4. 下载并编译PX4固件

### (1) 配置git和代理

由于PX4源码工程很大，网络连接不稳定很难成功，一定要**科学上网**！

下载git软件包：

```bash
sudo apt-get install git
```

配置git代理，本文的代理端口是`127.0.0.1:7890`：

```bash
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890
```

验证代理设置成功，运行以下指令，应该输出代理端口：

```bash
git config --get http.proxy
git config --get https.proxy
```

如果要取消代理，运行：

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### (2) clone PX4 v1.13.3源码

网络顺畅的话，可以直接运行：
```bash
git clone https://github.com/PX4/PX4-Autopilot.git --branch v1.13.3
```

如果仍然因为源码工程太大，clone过程会中断，可以按照以下步骤，先只clone最新的一次提交：

```bash
git clone https://github.com/PX4/PX4-Autopilot.git --branch v1.13.3 --depth 1
```

进入仓库不断补全之前的提交，`--depth`后面的参数按照网络情况选择，逐渐增大，例如100 200 300...1000 2000 3000...，而`--unshallow`参数表示补全所有提交：
```bash
cd PX4-Autopilot/
git fetch --depth 1000
...
git fetch --unshallow 
```

仓库补全后，再进行`fetch`则会显示`对于一个完整的仓库，参数 --unshallow 没有意义`。

### 3. 下载PX4子模块

```bash
git submodule update --init --recursive
```

一定要保证这一步运行完成，如果有因为网络连接问题的`fatal`报错，多尝试运行几次。

### 4. 编译PX4固件

针对自己的飞控硬件选择编译命令，本文为`Holybro Pixhawk 6c`，运行命令如下：

```bash
make px4_fmu-v6c_default
```

编译时注意确认第一行的提示版本为`-- PX4 version: v1.13.3`。

在编译过程中，会遇到缺少一些软件包而编译失败的问题，按照编译提示逐渐安装齐软件包即可。例如本文遇到了：

```bash
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ModuleNotFoundError: No module named 'menuconfig'
CMake Error at cmake/kconfig.cmake:6 (message):
  kconfiglib is not installed or not in PATH
  please install using "pip3 install kconfiglib"
```

```bash
CMake Error at CMakeLists.txt:235 (project):
  The CMAKE_C_COMPILER:
    arm-none-eabi-gcc
  is not a full path and was not found in the PATH.
  Tell CMake where to find the compiler by setting either the environment
  variable "CC" or the CMake cache entry CMAKE_C_COMPILER to the full path to
  the compiler, or to the compiler name if it is in the PATH.
-- Enabling double FP precision hardware instructions
```

```bash
Failed to import packaging: No module named 'packaging'
You may need to install it using:
    pip3 install --user packaging
```

```bash
Failed to import jinja2: No module named 'jinja2'
You may need to install it using:
    pip3 install --user jinja2
```

```bash
Failed to import toml: No module named 'toml'
You may need to install it using:
    pip3 install --user toml
```

```bash
Failed to import jsonschema: No module named 'jsonschema'
You may need to install it using:
    pip3 install --user jsonschema
```

```bash
Traceback (most recent call last):
  File "/home/hydrogen/PX4-Autopilot/src/drivers/uavcan/libuavcan/libuavcan/dsdl_compiler/libuavcan_dsdlc", line 59, in <module>
    from libuavcan_dsdl_compiler import run as dsdlc_run
  File "/home/hydrogen/PX4-Autopilot/src/drivers/uavcan/libuavcan/libuavcan/dsdl_compiler/libuavcan_dsdl_compiler/__init__.py", line 18, in <module>
    from dronecan import dsdl
ModuleNotFoundError: No module named 'dronecan'
```

```bash
Traceback (most recent call last):
  File "/home/hydrogen/PX4-Autopilot/src/modules/mavlink/mavlink/pymavlink/tools/mavgen.py", line 16, in <module>
    from pymavlink.generator import mavgen
  File "/home/hydrogen/PX4-Autopilot/src/modules/mavlink/mavlink/pymavlink/generator/mavgen.py", line 26, in <module>
    from future import standard_library
ModuleNotFoundError: No module named 'future'
```

依次运行了以下命令进行补全：

```bash
pip3 install kconfiglib
apt install gcc-arm-none-eabi
pip3 install --user packaging
pip3 install --user jinja2
pip3 install --user toml
pip3 install --user jsonschema
pip3 install dronecan
pip3 install future
```

## 四、常用调试工具安装

### 1. plotjuggler安装

安装plotjuggler软件：

```bash
sudo apt-get install ros-noetic-plotjuggler
```

安装相关ROS支持，否则无法打开bag文件：

```bash
sudo apt-get install ros-noetic-plotjuggler-msgs ros-noetic-plotjuggler-ros
```

运行plotjuggler：

```bash
rosrun plotjuggler plotjuggler
```




















