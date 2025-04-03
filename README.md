# ROS-Noetic-PX4-v1.13.3-Development

ROS Noetic + PX4 v1.13.3 Development Environment Setup

基于Ubuntu 20.04.6LTS操作系统，ROS版本Noetic，PX4版本v1.13.3。

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

