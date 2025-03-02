# Ubuntu20.04 PX4V1.13+Gazebo+Mavros2+ROS2Foxy+QGC+FAST-DDS联合仿真环境

踩了很多坑，文章制作不易，求点赞收藏转发。

## 生成ssh密钥

###  生成ssh密钥

```ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

###  将ssh密钥添加到ssh代理

```eval "$(ssh-agent -s)"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

###  生成的公钥添加到GitHub

```cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa.pub
```

![50796231098ab9d6d511b847a86edba](Ubuntu20.04 px4v1.13.assets/50796231098ab9d6d511b847a86edba.png)

###  测试添加成功

```
ssh -T git@github.com
```

![9564893ed8d95896778b85347f16e20](Ubuntu20.04 px4v1.13.assets/9564893ed8d95896778b85347f16e20.png)

##  安装px4固件v1.13

###  克隆px4v1.13

```sudo apt install git
sudo apt install git

git clone -b v1.13.0-beta1 https://github.com/PX4/PX4-Autopilot.git --recursive
```

- 网不好的话，换成ssh下载会更好一点。如果要确认网如何就用以下指令：

```
ping github.com
ping google.com
```

- 然后Ctrl C，看包的丢失率，如果是0，就证明网还可以。

```git clone -b v1.13.0-beta1 git@github.com:PX4/PX4-Autopilot.git --recursive
git clone -b v1.13.0-beta1 git@github.com:PX4/PX4-Autopilot.git --recursive
```

- 出现如下问题克隆失败：

  ![8e0d4530-5229-4154-9ecd-03b6f9128c5a](Ubuntu20.04 px4v1.13.assets/8e0d4530-5229-4154-9ecd-03b6f9128c5a.png)

### 更新下载子模块

```cpp
cd PX4-Autopilot/
git submodule update --init --recursive
```

![ffa1d491-a9ba-4457-bfd3-d04e9677ac8f](Ubuntu20.04 px4v1.13.assets/ffa1d491-a9ba-4457-bfd3-d04e9677ac8f.png)

###   安装依赖

```./PX4-Autopilot/Tools/setup/ubuntu.sh
sudo ./Tools/setup/ubuntu.sh
```

:smile_cat: 成功安装：![d2039b15-e1f7-45db-b946-d0b3580fb429](Ubuntu20.04 px4v1.13.assets/d2039b15-e1f7-45db-b946-d0b3580fb429.png)

#### 报错与警告

执行依赖的时候，会出现一些问题 :cry:。

- ERROR: pandas 2.0.3 has requirement python-dateutil>=2.8.2, but you'll have python-dateutil 2.7.3 which is incompatible.

```cpp
ERROR: pandas 2.0.3 has requirement python-dateutil>=2.8.2, but you'll have python-dateutil 2.7.3 which is incompatible.
```

![75185c62-cd27-407f-b4fe-82a144d7d6b1](Ubuntu20.04 px4v1.13.assets/75185c62-cd27-407f-b4fe-82a144d7d6b1.png)

- ```
  sudo pip install --upgrade python-dateutil
  ```

  成功安装：

  ![e5473565-4276-4267-8cf3-63d669256526](Ubuntu20.04 px4v1.13.assets/e5473565-4276-4267-8cf3-63d669256526.png)

- 出现如下问题：

![17000461-c412-4b6c-9b8d-97080934539a](Ubuntu20.04 px4v1.13.assets/17000461-c412-4b6c-9b8d-97080934539a.png)

```cpp
//新建终端，打开环境
nano ~/.bashrc 
//最下面添加环境变量
export PATH=:$PATH:/jun/.local/bin
//让环境变量生效
source ~/.bashrc
```

- 添加环境变量：

  ![3a1e705f-55e0-49c1-8be3-e8bef82d3121](Ubuntu20.04 px4v1.13.assets/3a1e705f-55e0-49c1-8be3-e8bef82d3121.png)

- `Ctrl+X`离开保存·
- `source`让环境变量生效
- ![7ad79154-2626-4d44-950f-14394e2e0bd9](Ubuntu20.04 px4v1.13.assets/7ad79154-2626-4d44-950f-14394e2e0bd9.png)

#### 问题解决

- 解决后，重新如下操作：

```cpp 
cd PX4-Autopilot/
//重新操作
./Tools/setup/ubuntu.sh
//等待时间
```

- 成功安装依赖

![53db8863-a2d5-483a-82be-4dfe428e5d79](Ubuntu20.04 px4v1.13.assets/53db8863-a2d5-483a-82be-4dfe428e5d79.png)

- 重启虚拟机

### 编译固件

~~~cpp
//进入px4
cd PX4-Autopilot/
make px4_sitl
~~~

- 编译报错

  ![e21f7be3-0033-4084-b3e8-9d5826b99ced](Ubuntu20.04 px4v1.13.assets/e21f7be3-0033-4084-b3e8-9d5826b99ced.png)

```c++
sudo apt update
sudo apt upgrade
cd ~/PX4-Autopilot
 //清理
make clean
 //重新编译
make px4_sitl
```

- 出现报错`em.RAW_OPT: True, em.BUFFERED_OPT: True})AttributeError: module 'em' has no attribute 'RAW_OPT'`

![807d2513-6162-4f67-a373-14af962a6726](Ubuntu20.04 px4v1.13.assets/807d2513-6162-4f67-a373-14af962a6726.png)

- 解决如下：

```cpp
pip uninstall empy
pip install empy==3.3.4
make clean 
make px4_sitl
```

- 编译成功：

  ![e9f7ac1c-d5b0-4888-94fc-51c7a45fa715](Ubuntu20.04 px4v1.13.assets/e9f7ac1c-d5b0-4888-94fc-51c7a45fa715.png)

## 安装Fast-DDS

### 安装Foonathan memory

- 克隆源码

```cpp
git clone https://github.com/eProsima/foonathan_memory_vendor.git
//网不好就ssh
git clone git@github.com:eProsima/foonathan_memory_vendor.git
```

- 克隆完成：

  ![3c21c024-584d-42af-8391-48206ddb3bef](Ubuntu20.04 px4v1.13.assets/3c21c024-584d-42af-8391-48206ddb3bef.png)

~~~cpp
cd foonathan_memory_vendor
//创建工作空间
mkdir build && cd build
//编译
 cmake ..
//安装
sudo cmake --build . --target install

~~~

- 无法访问等网络问题，就多试几次

- 安装成功：

  ![a6200835-403a-4429-988a-b954536e6042](Ubuntu20.04 px4v1.13.assets/a6200835-403a-4429-988a-b954536e6042.png)

### 安装Fast-DDS

- 克隆源码

```c++
git clone --recursive https://github.com/eProsima/Fast-DDS.git -b v2.0.2 ~/FastDDS-2.0.2
//老规矩，网不好就换成ssh
cd ~/FastDDS-2.0.2
```

![2be12ff3-f2e9-43b4-a3ec-785cc993e4d8](Ubuntu20.04 px4v1.13.assets/2be12ff3-f2e9-43b4-a3ec-785cc993e4d8.png)

- 创建工作空间，编译，安装:smile_cat:::smile_cat::smile_cat:

  ~~~cpp
  mkdir build && cd build
  cmake -DTHIRDPARTY=ON -DSECURITY=ON ..
  //需要编译一段时间，加油哦！超棒哦！
  make -j$(nproc --all)
  sudo make install
      
  ~~~

  编译成功：

  ![ca30858d-bf12-4e86-9554-8f7c24bcd191](Ubuntu20.04 px4v1.13.assets/ca30858d-bf12-4e86-9554-8f7c24bcd191.png)

![def9d2bb-6c2b-49ab-9896-454510a8b07c](Ubuntu20.04 px4v1.13.assets/def9d2bb-6c2b-49ab-9896-454510a8b07c.png)

- 安装成功：

![80b4e9bc-5f84-4309-8c30-a29406f2c4a6](Ubuntu20.04 px4v1.13.assets/80b4e9bc-5f84-4309-8c30-a29406f2c4a6.png)

### 安装Fast-RTPS-Gen

- 克隆源码

  ~~~cpp
  git clone --recursive https://github.com/eProsima/Fast-DDS-Gen.git -b v1.0.4 ~/Fast-RTPS-Gen
  ~~~

![db903ebc-9f22-4674-922b-ec230d0cf00a](Ubuntu20.04 px4v1.13.assets/db903ebc-9f22-4674-922b-ec230d0cf00a.png)

- 修改文件

  ~~~cpp
  cd ~/Fast-RTPS-Gen/gradle/wrapper
  //修改gradle-wrapper.properties文件里的distributionUrl=https\://services.gradle.org/distributions/gradle-6.8.3-bin.zip
  //打开文件
  nano gradle-wrapper.properties 
  
  ~~~

- 修改文件前后：

  ![e900a443-7e87-48cc-8019-1a5287ce8bcc](Ubuntu20.04 px4v1.13.assets/e900a443-7e87-48cc-8019-1a5287ce8bcc.png)

![2bf3f39f-c3ab-429d-9383-71b2b079a310](Ubuntu20.04 px4v1.13.assets/2bf3f39f-c3ab-429d-9383-71b2b079a310.png)

- 安装编译：:small_airplane::small_airplane::small_airplane:

  ~~~
  cd ~/Fast-RTPS-Gen 
  ./gradlew assemble && sudo env "PATH=$PATH" ./gradlew install
  
  ~~~

- 编译成功：

  ![b92c83b4-0aeb-41f4-8a91-955950a5a0d3](Ubuntu20.04 px4v1.13.assets/b92c83b4-0aeb-41f4-8a91-955950a5a0d3.png)

## 安装ROS2

参考小甲鱼一键安装`wget http://fishros.com/install -O fishros && bash fishros`

### 安装ros2-foxy

~~~
wget http://fishros.com/install -O fishros && bash fishros
~~~

![93bfd9e7-0e26-4d07-8929-670d35826be7](Ubuntu20.04 px4v1.13.assets/93bfd9e7-0e26-4d07-8929-670d35826be7.png)

### 安装依赖

~~~
sudo apt install python3-colcon-common-extensions
sudo apt install ros-foxy-eigen3-cmake-module
pip install --user -U empy pyros-genmsg setuptools
~~~

报错

![213c04b8-5924-43d1-bd55-38a0637dec2c](Ubuntu20.04 px4v1.13.assets/213c04b8-5924-43d1-bd55-38a0637dec2c.png)

- 解决

~~~
unset all_proxy    
unset ALL_PROXY
python3 -m pip install launchpadlib
~~~

- 重新安装

  ~~~
  pip install --user -U empy pyros-genmsg setuptools
  
  ~~~

- 查看ros2版本

  ~~~
  echo ROS_DisTRO
  ~~~

  

## 安装Gazabo

```
//编译px4固件的时候安装了gazebo
//查看gazebo版本
gazebo --version 
//启动gazebo
gazevbo
//都没问题则无需在意
```

![f1e2bce6-7556-4583-af07-7959cd0124c4](Ubuntu20.04 px4v1.13.assets/f1e2bce6-7556-4583-af07-7959cd0124c4.png)

## 安装MAVROS2

### 安装依赖

~~~c++
sudo apt install -y python3-vcstool python3-rosinstall-generator python3-osrf-pycommon python3-colcon-common-extensions

~~~

### 创建mavros2_ws工作空间

~~~
mkdir -p ~/mavros2_ws/src

~~~

### 下载安装清单

~~~
cd ~/mavros2_ws/
rosinstall_generator --format repos mavlink | tee /tmp/mavlink.repos
//网不好就多试一下，要将mavros2.4改成2.7，下面介绍
//2.4本身有bug
//生成mavros清单
rosinstall_generator --format repos --upstream mavros | tee -a /tmp/mavros.repos

~~~

![19d5358e-7c8b-49ca-9ad9-47c38d0a6a0d](Ubuntu20.04 px4v1.13.assets/19d5358e-7c8b-49ca-9ad9-47c38d0a6a0d.png)

- mavros改版本

![7f806aba-8526-4b0a-946d-89e35a57ce42](Ubuntu20.04 px4v1.13.assets/7f806aba-8526-4b0a-946d-89e35a57ce42.png)

![b6e59fa7-b8a3-4ab8-b48e-41e4cba73122](Ubuntu20.04 px4v1.13.assets/b6e59fa7-b8a3-4ab8-b48e-41e4cba73122.png)

![dee808ce-3b5c-4761-bdd2-457765302ce1](Ubuntu20.04 px4v1.13.assets/dee808ce-3b5c-4761-bdd2-457765302ce1.png)

### 下载源码:crying_cat_face:

~~~
//老规矩，网不好就多弄几次
//或者ping github.com 看看丢包率
//运气也占很大一部分，重启一下虚拟机试试
//好吧，我也搞了好久这个地方
//重启后要重新从清单那步开始弄
//搞定，大家加油加油
vcs import src < /tmp/mavlink.repos
vcs import src < /tmp/mavros.repos
~~~

![10c9c6ac-8268-43f8-9d87-a935de047ffc](Ubuntu20.04 px4v1.13.assets/10c9c6ac-8268-43f8-9d87-a935de047ffc.png)

![b675212f-76e8-4e2e-97b2-f0fa69b9b6ce](Ubuntu20.04 px4v1.13.assets/b675212f-76e8-4e2e-97b2-f0fa69b9b6ce.png)

### 安装依赖

~~~
rosdep install --from-paths src --ignore-src -y
~~~

- 安装成功

  ![69e098ed-6ca0-4323-bec5-c144e4d96aff](Ubuntu20.04 px4v1.13.assets/69e098ed-6ca0-4323-bec5-c144e4d96aff.png)

- 报错

  ERROR: your rosdep installation has not been initialized yet.  Please run:

      sudo rosdep init
      rosdep update

  ![7d1705ad-3e5b-4bfe-b818-a79367fdd095](Ubuntu20.04 px4v1.13.assets/7d1705ad-3e5b-4bfe-b818-a79367fdd095.png)

- 解决1

  ~~~
  sudo rosdep init
  rosdep update
  rosdep install --from-paths src --ignore-src -y
  //网不好就多试几次，搞不出来就下一个方法
  
  ~~~

- 解决2

  ~~~
  wget http://fishros.com/install -O fishros && bash fishros
  //选3
  //1]:一键安装(推荐):ROS(支持ROS/ROS2,树莓派Jetson)
  //[3]:一键安装:rosdep(小鱼的rosdepc,又快又好用)
  rosdep install --from-paths src --ignore-src -y
  ~~~

  ![f1c21ccf-295f-405f-935b-b356d2bf0c2e](Ubuntu20.04 px4v1.13.assets/f1c21ccf-295f-405f-935b-b356d2bf0c2e.png)

### 安装GeographicLib 

~~~
 sudo ./src/mavros/mavros/scripts/install_geographiclib_datasets.sh 

~~~

![6d6dcdaf-f785-48b9-8432-90e6ad1b2739](Ubuntu20.04 px4v1.13.assets/6d6dcdaf-f785-48b9-8432-90e6ad1b2739.png)

- 报错

  `  sudo ./src/mavros/mavros/scripts/install_geographiclib_datasets.sh 
  ./src/mavros/mavros/scripts/install_geographiclib_datasets.sh: 第 33 行： hash: geographiclib-get-geoids：未找到
  ./src/mavros/mavros/scripts/install_geographiclib_datasets.sh: 第 37 行： hash: geographiclib-datasets-download：未找到
  OS not supported! Check GeographicLib page for supported OS and lib versions. `

![f5ea7c6e-fe53-420e-bc67-15acb98207c7](Ubuntu20.04 px4v1.13.assets/f5ea7c6e-fe53-420e-bc67-15acb98207c7.png)

- 解决

~~~
sudo apt-get update
sudo apt-get install geographiclib-tools

~~~

![10215c2b-daa4-41a5-8636-b5d9f4b26dcc](Ubuntu20.04 px4v1.13.assets/10215c2b-daa4-41a5-8636-b5d9f4b26dcc.png)

- 重新执行

  ~~~
  sudo ./src/mavros/mavros/scripts/install_geographiclib_datasets.sh 
  
  ~~~

### 编译

~~~cpp
cd ~/mavros2_ws
colcon build
//至少十分钟，加油加油呀
//保佑大家一次过
~~~

- 报错

   `Could not find a package configuration file provided by "geographic_msgs  with any of the following names`

    geographic_msgsConfig.cmake
    geographic_msgs-config.cmake

![91be4e45-5949-4d60-9415-4977b5281bfb](Ubuntu20.04 px4v1.13.assets/91be4e45-5949-4d60-9415-4977b5281bfb.png)

- 解决：

~~~
sudo apt-get install ros-foxy-geographic-msgs
~~~

- 继续：

  ~~~
  //删除刚刚编译
  rm -rf build install log
  //重新编译
  colcon build 
   
  ~~~

- 报错

![f195a740-9863-4831-af83-2c01152e4c70](Ubuntu20.04 px4v1.13.assets/f195a740-9863-4831-af83-2c01152e4c70.png)

- 解决将`.hpp`文件变成`.h`文件

  ~~~
  find ./src/mavros/ -type f -exec sed -i 's/tf2_eigen\.hpp/tf2_eigen\.h/g' {} +
  
  ~~~

- 继续`colcon`

~~~cpp
rm -rf build install log
colcon build 
~~~

- 编译成功

  ![a7f8fc26-46f2-4c3a-b82f-2bc5be01e58d](Ubuntu20.04 px4v1.13.assets/a7f8fc26-46f2-4c3a-b82f-2bc5be01e58d.png)

### 设置环境

~~~
nano ~/.bashrc
//在最下面设置环境变量
source ~/mavros2_ws/install/setup.bash
//打开一个终端
ros2 launch mavros px4.launch
~~~

## ROS2工作空间

### 创建工作空间

~~~
mkdir -p ~/ws_sensor_combined/src/
cd ~/ws_sensor_combined/src/
~~~

### 克隆1.13版本的px4_ros_com和px4_msgs

~~~
 git clone -b release/1.13 https://github.com/PX4/px4_ros_com.git ~/px4_ros_com_ros2/src/px4_ros_com
 git clone -b release/1.13 https://github.com/PX4/px4_msgs.git ~/px4_ros_com_ros2/src/px4_msgs
 //老规矩，网不好就ssh
~~~

![df9b33c0-9dc7-43d0-b8ca-cd60da777607](Ubuntu20.04 px4v1.13.assets/df9b33c0-9dc7-43d0-b8ca-cd60da777607.png)

### 编译工作空间

~~~
cd ~/px4_ros_com_ros2/src/px4_ros_com/scripts
source build_ros2_workspace.bash
//编译得十几分钟，可以喝个水休息一下
~~~

![dac461c8-23e6-409c-98b9-a5f1c2f006ec](Ubuntu20.04 px4v1.13.assets/dac461c8-23e6-409c-98b9-a5f1c2f006ec.png)

### 添加环境变量

~~~
//老规矩，先打开,然后找到最下面
nano ~/.bashrc
//保存文档
source ~/px4_ros_com_ros2/install/setup.bash
//让文档生效
source ~/.bashrc
~~~

### 检验安装成功

- 打开一个终端

  ~~~
  cd PX4-Autopilot/
  make px4_sitl_rtps gazebo
  
  ~~~

![4937d47b-6bd8-4e49-8d1f-8768f1bfc1e3](Ubuntu20.04 px4v1.13.assets/4937d47b-6bd8-4e49-8d1f-8768f1bfc1e3.png)

- 打开一个终端

~~~
//通信
micrortps_agent -t UDP
~~~

![34da25c5-98b1-4624-b4de-2d68c8b92120](Ubuntu20.04 px4v1.13.assets/34da25c5-98b1-4624-b4de-2d68c8b92120.png)

- 打开一个终端

~~~
//启动示例
ros2 launch px4_ros_com sensor_combined_listener.launch.py
~~~

![e281c0d2-f1f9-4ea4-a7d3-e391d1a00fba](Ubuntu20.04 px4v1.13.assets/e281c0d2-f1f9-4ea4-a7d3-e391d1a00fba.png)

## 安装QT 5.15.2

### 安装依赖

~~~
sudo apt install gcc g++ make cmake build-essential libgl1-mesa-dev
sudo apt install libxcb-cursor0 libxcb-cursor-dev
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y

~~~

### 下载qt

~~~
//虚拟机的浏览器下载
https://mirrors.tuna.tsinghua.edu.cn/qt/archive/online_installers/4.8
~~~

![7ab766bf-16d0-411a-a486-0777d7aa1063](Ubuntu20.04 px4v1.13.assets/7ab766bf-16d0-411a-a486-0777d7aa1063.png)

~~~
chmod +x qt-online-installer-linux-x64-4.8.1.run
sudo ./qt-online-installer-linux-x64-4.8.1.run 

~~~

### 安装qt

![04e4e5b4-c6b1-4af2-9895-4d1cfefa7c86](Ubuntu20.04 px4v1.13.assets/04e4e5b4-c6b1-4af2-9895-4d1cfefa7c86.png)

![f82690da-cc46-4f5f-a17b-e659694c3299](Ubuntu20.04 px4v1.13.assets/f82690da-cc46-4f5f-a17b-e659694c3299.png)

![174fb52e-63db-49fa-9da7-c8ee1d03424d](Ubuntu20.04 px4v1.13.assets/174fb52e-63db-49fa-9da7-c8ee1d03424d.png)

![1d5c26a0-56ce-415c-836c-75e1df715768](Ubuntu20.04 px4v1.13.assets/1d5c26a0-56ce-415c-836c-75e1df715768.png)

![b61530fe-a54f-4e12-907e-da76cb1af6ba](Ubuntu20.04 px4v1.13.assets/b61530fe-a54f-4e12-907e-da76cb1af6ba.png)

![8fe532ca-25dd-4d41-aeaa-963e1d27a7e1](Ubuntu20.04 px4v1.13.assets/8fe532ca-25dd-4d41-aeaa-963e1d27a7e1.png)

## 基于Qt5.15.2的QGC 4.3安装

### 安装依赖，安装后重启

~~~
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager -y
sudo apt install gcc g++ make cmake git
sudo apt install speech-dispatcher libudev-dev libsdl2-dev patchelf build-essential curl
//安装后重启
~~~

![8eb1cca2-e5ba-434c-a281-fdf345859f54](Ubuntu20.04 px4v1.13.assets/8eb1cca2-e5ba-434c-a281-fdf345859f54.png)

### 克隆源码

~~~
git clone https://github.com/mavlink/qgroundcontrol.git
//不顺畅就ssh，白天网会更好点
git clone git@github.com:mavlink/qgroundcontrol.git
~~~

![5147ef44-4b0d-4e6f-910f-6288bf88bce8](Ubuntu20.04 px4v1.13.assets/5147ef44-4b0d-4e6f-910f-6288bf88bce8.png)

~~~
cd qgroundcontrol/
//切换分支
git checkout Stable_V4.3
~~~

![a5da2e0f-223e-4ca5-901f-c07f324a578e](Ubuntu20.04 px4v1.13.assets/a5da2e0f-223e-4ca5-901f-c07f324a578e.png)

~~~
git submodule update --init --recursive  
//更新下载子模块
//网不好就多试几次
//我就是试好几次
//大家加油加油
~~~

![64451124-687c-4adb-8c09-d0454224bcc9](Ubuntu20.04 px4v1.13.assets/64451124-687c-4adb-8c09-d0454224bcc9.png)

### 编译qgc 方法1

打开qt，找到qgc编译文件

![0cba41b1-ce78-4f0a-8f5c-0b4a0df8d30c](Ubuntu20.04 px4v1.13.assets/0cba41b1-ce78-4f0a-8f5c-0b4a0df8d30c.png)

编译：![cfc714db-ce19-471a-970d-5a14f4955a64](Ubuntu20.04 px4v1.13.assets/cfc714db-ce19-471a-970d-5a14f4955a64.png)

很慢的:

![08218ad2-39ed-43a9-943e-8c134d8181a9](Ubuntu20.04 px4v1.13.assets/08218ad2-39ed-43a9-943e-8c134d8181a9.png)

![aee70a6c-7f0c-4d5c-acb9-4069a06126a3](Ubuntu20.04 px4v1.13.assets/aee70a6c-7f0c-4d5c-acb9-4069a06126a3.png)

### 编译qgc 方法2

~~~
cd qgroundcontrol
mkdir build
cd build
qmake ..
make -j$(nproc)  # 使用多核加速编译
cd ~/qgroundcontrol/build/staging
./QGroundControl
~~~

![1364273f-5310-41ca-b22f-c33128273a1c](Ubuntu20.04 px4v1.13.assets/1364273f-5310-41ca-b22f-c33128273a1c.png)

#### 报错与警告

![32018acc-178b-47a1-8b72-0eba23e07537](Ubuntu20.04 px4v1.13.assets/32018acc-178b-47a1-8b72-0eba23e07537.png)

~~~
nano ~/.bashrc
//找到最后一行
export PATH="/opt/Qt/5.15.2/gcc_64/bin:$PATH"
source ~/.bashrc 
~~~

## 测试mavros节点

### 启动gazebo仿真

打开一个终端

~~~
cd ~/PX4-Autopilot
make px4_sitl gazebo
~~~

![051da50c-1578-4f65-8289-d98084ff0a3d](Ubuntu20.04 px4v1.13.assets/051da50c-1578-4f65-8289-d98084ff0a3d.png)

### 启动mavros 2 通讯

打开另一个终端

~~~
//进入工作区间
cd ~/mavros2_ws
ros2 launch mavros px4.launch fcu_url:="udp://:14550@192.168.1.1:14550"
~~~

![869c29f6-2ee2-41ce-b0ea-16ac8ea4f2b5](Ubuntu20.04 px4v1.13.assets/869c29f6-2ee2-41ce-b0ea-16ac8ea4f2b5.png)

再打开一个终端

~~~
ros2 topic echo /mavros/state

~~~

![04a4cf53-82d1-438b-8d21-e1dbe91f301f](Ubuntu20.04 px4v1.13.assets/04a4cf53-82d1-438b-8d21-e1dbe91f301f.png)

## PX4+Gazebo+fast通信

### 启动Gazebo

打开一个终端

~~~
cd PX4-Autopilot/
make px4_sitl_rtps gazebo
~~~

### 启动fast通讯

~~~
cd px4_ros_com_ros2/
micrortps_agent -t UDP
~~~

![81b227b1-efed-42f7-8869-93bc9548c6f2](Ubuntu20.04 px4v1.13.assets/81b227b1-efed-42f7-8869-93bc9548c6f2.png)

## 未完待续，后面会更新和matlab联合仿真教程
