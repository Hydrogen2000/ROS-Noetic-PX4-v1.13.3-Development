# ROS-Noetic-PX4-v1.13.3-Development

ROS Noetic + PX4 v1.13.3 Development Environment Setup

基于Ubuntu 20.04.6LTS操作系统，ROS版本Noetic，PX4版本v1.13.3。

## 1.鱼香ROS安装ROS

采用鱼香ROS的一键安装脚本，避免繁琐的官网安装步骤和网络连接问题。

```bash
wget http://fishros.com/install -O fishros && . fishros
```

### (1)安装ROS

依次选择“一键安装:ROS” -> “更换系统源再继续安装” -> “更换系统源并清理第三方源” -> “noetic(ROS1)” -> “noetic(ROS1)桌面版”，等待下载完成。

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

### (2)配置rosdep

选择“一键安装:rosdep”，完成后按照指示运行：

```bash
rosdepc update
```

### (3)配置ROS环境

选择“一键配置:ROS环境”。

### 参考

[1]ROS Wiki http://wiki.ros.org/noetic/Installation/Ubuntu

[2]鱼香ROS社区 https://fishros.org.cn/forum/topic/20/%E5%B0%8F%E9%B1%BC%E7%9A%84%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E7%B3%BB%E5%88%97
