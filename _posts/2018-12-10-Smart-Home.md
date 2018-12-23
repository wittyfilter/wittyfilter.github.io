---
title: 初探智能家居Home Assistant
layout: post
categories:
- Technique
tags:
- Raspberry Pi
- Home-assistant
- Homebridge
- Homekit
---

## 0. 起因

为什么要弄智能家居呢？来源于一个非常小的需求：家里的门锁总感觉不可靠，即使有了淘宝神器也不能完全安心，每天还是会纠结门是否关好。聊天的时候淡淡说有那种门窗传感器可以了解一下，于是乎上张大妈搜了一下，找到了一个讲小米智能家居全家桶排雷的文章，里面介绍到了Home Assistant开源智能家居平台，可以和苹果的Homekit进行对接，这样就可以在手机上监控家中的情况。这篇文章让我想起了国庆去托马斯家住的时候，早上七点钟灯会自动开起来，当时托马斯很自豪地说这是他设置的智能家居程式，让我羡慕不已，感觉有种赛博朋克的味道！正好，在实验室里面找到了闲置许久的树莓派，拿来做家庭控制中心正好不过了！

<!-- more -->

那么，这么多智能家居平台，要选择哪一家呢？说到智能家居，每个公司都有自己的战略布局，有小米博联这样的“半开放”平台（虽然加塞了自己的东西但是可以很好地接入第三方平台）和阿里京东这样的“封闭”平台（截止目前，天猫精灵AI联盟里面买的东西还是只能用天猫精灵控制，也就是从自家的app里面添加以外别无他法）。其实我们最经常能接触到的智能家居控制中心便是苹果的Homekit，界面非常符合苹果一贯的简洁风格，但是Homekit的配件都很贵，预算有限的人（包括我）常常会望而却步，才不得不考虑买其他家的智能家居产品。

如果不介意都买一家公司的“全家桶”，那么这家公司自己的智能家庭APP已经可以满足你的需求。如果考虑到价格和性能，每种产品都选择了不同家的公司，那手机里装的APP就要塞不下了。因此，最好的将他们整合在一起的方法便是第三方的开源平台。首先要说开源的Homebridge，由前Homekit程序员创建，用nodejs编写，让无法接入homekit的第三方产品通过插件兼容桥接入。还有其他一些好平台，比如Domoticz，openHAB等元老，笔者尚未接触过。根据网路上的描述，OpenHAB基于Java开发，社区非常成熟，而Domoticz用c++实现，体积小可扩展性高，但其中文社区不够活跃，文档也相对较少。接下来重点介绍Home Assistant（HA，下同）！HA属于新兴力量，基于Python且UI友好，崇尚“Simplicity”，社区活跃而文档丰富，每周都有新版本的小插件发布，比起审核严格的OpenHAB来说快不少。所以，最后综合考虑，我选择了HA作为控制中心，先来上一张效果图吧～对UI没有强迫症的我用的都是默认的UI设置。

![]({{ site.baseurl }}/public/images/HA1.png)

虽然智能家居其实和人工智能的“智能”二字相去甚远，但说到底本意是为了方便生活中的一些小细节，因此也算是一个很有意义的“大人的玩具”了～

## 1. 前置条件

### 1.1 硬件选择

任何电脑都可以运行HA：笔记本、台式机、Mac、微型电脑...等等。选择树莓派的原因是自己刚好就有一块，而且放在那里常开功率也不会太大，不会进入睡眠模式。虽然亲测初代B版树莓派也是可以满足需求的，但是在编译安装HA和其他插件的过程中总会出现这样那样的错误，和内存与算力均有关系。考虑到未来会加入摄像头（ffmpeg），或者希望自带WiFi（可以放在任何角落而不需要放在路由器旁边），树莓派三代B+版本是最好的选择。

### 1.2 操作系统及安装

任何操作系统都可以！因为HA是基于Python的，所以简单给出命令：

```zsh
$ sudo pip3 install homeassistant
```

即可。随后运行
```zsh
$ sudo 	hass --open-ui
``` 

来以root权限运行HA，目的是为了安装一些必要的插件。等到安装好以后就可以以普通用户身份运行HA了！

如果是在树莓派上，还可以考虑官方的[Hass.io](https://www.home-assistant.io/hassio/)，一个基于Docker的HA系统，将镜像烧录到SD卡上就可以即插即用！笔者不考虑Hass.io的原因是希望在树莓派上还可以运行一些别的程序，比如Homebridge或者媒体中心之类的，因此就选择了手动安装啦～

## 2. 配置

在HA的配置文件夹（一般是`/home/pi/.homeassistant/`下），编辑主配置文件`configuration.yaml`。下面贴出我的参考文件：

```yaml
homeassistant:
  # 当前运行HA的名字
  name: ???
  # 家庭地址经纬度，用于计算日出日落的时间
  latitude: ???
  longitude: ???
  # Impacts weather/sunrise data (altitude above sea level in meters)
  elevation: 0
  # metric for Metric, imperial for Imperial
  unit_system: metric
  # Pick yours from here: http://en.wikipedia.org/wiki/List_of_tz_database_time_zones
  time_zone: Asia/Shanghai
  # Customization file
  customize: !include customize.yaml
  packages: !include_dir_named packages

# Show links to resources in log and frontend
# introduction:

# Enables the frontend
frontend:

# Enables configuration UI
config:

# Uncomment this if you are using SSL/TLS, running in Docker container, etc.
http:
#   base_url: example.duckdns.org:8123
   api_password: ???

# Checks for available updates
# Note: This component will send some information about your system to
# the developers to assist with development of Home Assistant.
# For more information, please see:
# https://home-assistant.io/blog/2016/10/25/explaining-the-updater/
updater:
  # Optional, allows Home Assistant developers to focus on popular components.
  # include_used_components: true

# Discover some devices automatically
discovery:

# Allows you to issue voice commands from the frontend in enabled browsers
conversation:

# Enables support for tracking state changes over time
history:

# View all events in a logbook
logbook:

# Enables a map showing the location of tracked devices
map:

# Track the sun
sun:

recorder:
  db_url: postgres://???:???@localhost/homeassistant
	
# mqtt:
#  broker: ???

# Sensors
sensor:
  # Weather prediction
  - platform: yweather
    forecast: 2
    monitored_conditions:
      - weather
      - temp_min
      - temp_max
  - platform: template
    sensors:
      xiaomi_ap_aqi_raw:
        friendly_name: AQI Raw
        value_template: "{{ states.fan.xiaomi_miio_device.attributes.aqi }}"
        unit_of_measurement: AQI
  - platform: filter
    name: "Filtered pm25"
    friendly_name: "空气质量"
    entity_id: sensor.xiaomi_ap_aqi_raw
    filters:
      - filter: lowpass
        time_constant: 10
      - filter: time_simple_moving_average
        window_size: 00:05
        precision: 2
  - platform: template
    sensors:
      xiaomi_ap_temp:
        friendly_name: "温度"
        value_template: "{{ states.fan.xiaomi_miio_device.attributes.temperature }}"
        unit_of_measurement: °C
        device_class: temperature        
  - platform: template
    sensors:
      xiaomi_ap_humidity:
        friendly_name: "湿度"
        value_template: "{{ states.fan.xiaomi_miio_device.attributes.humidity }}"
        unit_of_measurement: "%"
        device_class: humidity

# Text to speech
tts:
  - platform: baidu
    app_id: ???
    api_key: ???
    secret_key: ???

# Cloud
cloud:

# ffmpeg:
#  ffmpeg_bin: '/usr/local/bin/ffmpeg'

# camera:
#  - platform: ffmpeg
#    name: ezviz
#    input: -rtsp_transport tcp -i rtsp://admin:???@???/h264/ch1/main/av_stream
#  - platform: ezviz
#     name: "ezviz"
#     id: "???"
#     key: "???"
#     sec: "???"
  
# - platform: ffmpeg
#    name: webcam
#    input: -f v4l2 -r 30 -i /dev/video0

# ecovacs:
#  username: ???
#  password: ???
#  country: cn
#  continent: as

xiaomi_aqara:
  gateways:
    - mac: ???
      key: ???

light: !include lights.yaml

fan:
  - platform: xiaomi_miio
    friendly_name: "???"
    model: zhimi.airpurifier.v6
    host: ???
    token: ???

switch http:
  - platform: command_line
    switches:
      lightwall:
        friendly_name: "???"
        command_on: 'curl -k "http://???/control?cmd=GPIO,???,1"'
        command_off: 'curl -k "http://???/control?cmd=GPIO,???,0"'

switch broadlink:
  - platform: broadlink
    host: ???
    mac: '???'
    timeout: 15
    switches:
      tv_samsung:
        friendly_name: "电视开关"
        command_on: '???'
        command_off: '???'           
        ......
      table_up:
        friendly_name: "桌子上升"
        command_on: '???'
        command_off: '???'
      table_down:
        friendly_name: "桌子下降"
        command_on: '???'
        command_off: '???'

cover:
  - platform: template
    covers:
      garage_door:
        friendly_name: "升降桌"
        open_cover:
          service: switch.turn_on
          data:
            entity_id: switch.table_up
        close_cover:
          service: switch.turn_on
          data:
            entity_id: switch.table_down
        stop_cover:
          service: switch.turn_off
          data:
            entity_id: switch.table_up
				
ifttt:
    key: ???

homekit:
    filter:
       exclude_domains: 
         - automation
       exclude_entities:
         - ???

asuswrt:
    host: ???
    username: ???
    ssh_key: /home/pi/.ssh/id_rsa

media_player:
  - platform: vlc

group: !include groups.yaml
automation: !include automations.yaml
script: !include scripts.yaml

molohub:
```

## 3. 设备详情

### 红外/射频遥控器

为了解决平时找遥控器怎么也找不到的难题，把所有遥控器控制集中在手机上是个不错的选择。目前市面上的遥控器不是红外控制就是射频控制，由于这两个频段不一样（包括射频自己的频段选择），因此诸如小米天猫万能遥控器有时候是不管用的。可以自己购买无线收发模块DIY，但如果追求省事那就购买博联的RM Pro吧！配置很简单，在接入网络获取到ip后，在`configuration.yaml`里面加入如下字段（以飞利浦电视开关为例）：

```yaml
switch:
  - platform: broadlink
    host: IP_ADDRESS
    mac: 'MAC_ADDRESS'
    timeout: 15
    switches:
      # Will work on most Phillips TVs:
      tv_phillips:
        friendly_name: "Phillips Tv Power"
        command_on: 'JgAcAB0dHB44HhweGx4cHR06HB0cHhwdHB8bHhwADQUAAAAAAAAAAAAAAAA='
        command_off: 'JgAaABweOR4bHhwdHB4dHRw6HhsdHR0dOTocAA0FAAAAAAAAAAAAAAAAAAA='
```

这里的一大串数字字母组合就是学习到的码，如何获得呢？首先先把模板中字段填好，即`command_on: ' '`和`command_off: ' '`部分，保存重启HA。随后进入HA面板中的Services部分，选择`switch.broadlink_learn_command`这个服务并CALL SERVICE，然后按下你需要学习的遥控器按键，顺利的话在HA面板中的States部分会有一个新的Entity叫做`persistent_notification.notification`，查看它的message就能看到学习到的码啦，填入之前留空的模板即可！

如果是射频码的学习，稍微有一点麻烦。目前最省事的方法就是在iOS系统中下载易控这个App，按照里面的操作提示，添加你的RM pro，然后进行扫频操作，按下遥控器的按键后扫频即完成。此时再进入HA进行上述的码学习流程即可！

目前加入的遥控设备有：电视（开关，输入源选择，音量大小），阳台自动晾衣杆（照明，上升，下降），自动升降桌（上升，下降），松下遥控吸顶灯（当前仅开关，亮度调节懒得实现了哈哈哈）

### 米家产品

#### 网关
#### 门窗传感器
#### 人体传感器
#### 开关
#### 小米空净

### ESP8266

ESP8266是个好东西，简单理解就是一个带有Wi-Fi模块的廉价微控制器。淘宝一个Nodemcu lua v3只需要13块钱，就可以DIY出各种智能家居设备了！例如和继电器相接控制家里各自电器，或者利用GPIO接入各种类型传感器。

### Device Tracker

如何知道家中的成员是否在家？可以考虑使用GPS，蓝牙，摄像头...当然，这其中最省心的方法便是查看谁连接了家里的Wi-Fi！那么怎么知道家里Wi-Fi的连接情况呢？比较通用的方法是利用Nmap模块来不断嗅探，例如：

```yaml
device_tracker:
  - platform: nmap_tracker
    hosts: 192.168.1.0/24
    home_interval: 10
    exclude:
     - 192.168.???.???
     - 192.168.???.???
```

如果家里的路由器恰好使用Netgear或者Asuswrt，那么更为有效且方便的方法是：

```yaml
device_tracker:
  - platform: netgear
    host: 192.168.1.1
    username: ???
    interval_seconds: 10
    consider_home: 180
    new_device_defaults:
      track_new_devices: false
      hide_if_away: false
```
或者：
```yaml
asuswrt:
    host: 192.168.1.1
    username: ???
    ssh_key: /home/pi/.ssh/id_rsa
```

具体每个device是否要track，使用什么图片作为头像，别名等等，都可以在`known_devices.yaml`这个配置文件里面得到编辑。

### 摄像头

### 音响


## 4. 自动化

## 5. 优化

有时候，传感器的数值在某个时间会突然跳动很厉害，造成某些自动化的误触发（比如上述的空气质量差时自动打开空净），这时候就要使用一些方法来去掉这些outliers，或者通过滤波来让数据更加平滑。

![]({{ site.baseurl }}/public/images/HA2.png)