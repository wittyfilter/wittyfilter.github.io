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

来以root权限运行HA，目的是为了安装一些必要的插件。随时去http://树莓派IP地址:8123 看看是不是安装好了。等到安装好以后就可以以普通用户身份运行HA了！即`hass --open-ui`

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
  # 雅虎天气
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

ffmpeg:
  ffmpeg_bin: '/usr/local/bin/ffmpeg'

camera:
  - platform: ffmpeg
    name: ezviz
    input: -rtsp_transport tcp -i rtsp://admin:???@???/h264/ch1/main/av_stream

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

## 3. 设备与组件

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

虽然对小米其他产品无感，但是一些智能家居的产品还是很不错的！我认为一个原因是收购了一些像yeelight这样的成熟物联网平台，又有一些例如aqara的衍生产业链；另一个是产品的可控性很高，没有为了生态圈而封闭；还有就是价格低廉（一贯特征）！当初是为了门窗传感器入的坑，刚好双十一有个米家智能家居套装的优惠，里面包括了智能网关、门窗传感器、人体传感器、智能开关与智能插座，很实惠就入了。智能插座可以进行功率统计，来完成一些类似于充满电自动断电的环保功能，这里不展开叙述，聊一聊其他的产品。

#### 网关

大部分米家产品都要先接入智能网关才能在米家APP中使用。同样，智能网关是接入HA的桥梁。打开米家APP，先选择智能网关，然后点选右上角的`···` ，进入后点`关于`。这时候，对着空白地方不断点击，就会出现`局域网通信协议`和`网关信息`选项，是不是和Android开启开发者选项很像！打开局域网通信协议，记住局域网通信协议密码以及网关的 MAC 地址，填入配置文件中：

```yaml
xiaomi_aqara:
  gateways:
    - mac: ???
      key: ???
```

网关除了桥接作用，还有很方便的地方便是自带的光照传感器，Alarm以及亮灯功能。光照传感器作为是否需要开灯的先决条件特别重要，Alarm可以作为开门的门铃声，而灯泡可以和人体传感器结合做成自动夜灯。

#### 门窗传感器

其实就是两块磁铁+传感+无线传输，因此买一个门磁套件+ESP8266自己DIY成本不过20人民币！但米家的这个还是很不错的～

#### 人体传感器

也是可以DIY的低成本产品，放在大概1米～2米的位置，倒立着放90度放都是可以的！但是需要注意一下几个雷区：
1. 不能隔着玻璃！即使能被看见。 
2. 感应到有人以后一分钟以内都是进入冻结状态的，无法进行感应。
3. 不要用于关灯的逻辑！非常尴尬。做饭做一半、洗澡洗一半、上厕所上一半就进入黑夜的感觉很不好！因为总是无法规避有bug，或者一动不动的时候会不会误伤自己。

#### 贴墙式开关

非嵌入式开关，因此灵活性很高，可以有单击、双击、长按三种events。结合Automation可以衍生出无数种events！

#### 86嵌入式墙壁开关

有两种版本，一种是单火线版，一种是零火线版。据论坛上所说后者更加便宜且稳定性更高，但实际家中装修没有留出零线，因此买这类产品前一定要先拆开原来的开关看看线的情况！

另一种问题就是双控开关无法直接替换，因此如果这个开关和另一个开关共同控制负载，那么最好保留原状！一定要改装的话，可以采用一边嵌入式86开关，一边用上述的贴墙式开关，就可以实现联动了！但是原来的那些线就没有用了，且要直接短接掉（操作特别注意，小心用电安全！）。一种常见的双控开关设置如下所示，改装时候可以参考。

#### 小米空净

空净选择小米的原因，一是性价比（打折时候入），二是可以很好地被HA支持。先将小米空净接入米家APP得到ip地址，随后参考[Retrieving the access token](https://www.home-assistant.io/components/vacuum.xiaomi_miio/#retrieving-the-access-token)获得access token，最后在配置中加入：

```yaml
fan:
  - platform: xiaomi_miio
    friendly_name: "???"
    model: zhimi.airpurifier.v6
    host: ???
    token: ???
```

就可以像控制风扇一样控制开关和风速啦！

此外，小米空净内置的三个传感器：温度，湿度与AQI也可以被利用起来！利用HA的Template sensor模块将xiaomi_miio的attributes抽离出来：

```yaml
- platform: template
    sensors:
      xiaomi_ap_aqi:
        friendly_name: AQI
        value_template: "{{ states.fan.xiaomi_miio_device.attributes.aqi }}"
        unit_of_measurement: AQI
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
```

### ESP8266

ESP8266是个好东西，简单理解就是一个带有Wi-Fi模块的廉价微控制器。淘宝一个Nodemcu lua v3只需要13块钱，就可以DIY出各种智能家居设备了！例如和继电器相接控制家里各自电器，或者利用GPIO接入各种类型传感器。根据需要的逻辑，对芯片进行烧写c代码，其风格让我想起了当年电子设计竞赛！

有没有更简单的方法呢？有，就是直接利用串口烧写现成的一些成熟系统，例如ESPEasy和ESPHome，然后就可以在网页上任意配置了！结合MQTT服务，可以让Nodemcu成为数据转发中心，但是我对MQTT不是非常了解，只得作罢。当前，仅用于控制简单的LED灯带而已，操作非常简单：先按照教程刷入ESPEasy，初始化后得到Nodemcu的ip地址，然后就可以在浏览器中输入简单的http请求来控制MCU的GPIO啦！例如输入`http://{ip_address}/control?cmd=GPIO,12,1`就可以让GPIO12拉出高电平了！接下来，在HA中，添加基于command_line的开关模块：

```yaml
switch http:
  - platform: command_line
    switches:
      lightwall:
        friendly_name: "???"
        command_on: 'curl -k "http://???/control?cmd=GPIO,12,1"'
        command_off: 'curl -k "http://???/control?cmd=GPIO,12,0"'
```

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

把相机作为摄像头输入，可以使用ffmpeg或者mjpeg。以ffmpeg为例，首先从源码编译带有h264和omx的选项，即

```zsh
sudo ./configure --arch=armel --target-os=linux --enable-gpl --enable-libx264 --enable-nonfree  --enable-omx --enable-omx-rpi
```

然后在配置中加入：

```yaml
 ffmpeg:
  ffmpeg_bin: '/usr/local/bin/ffmpeg'
```

注意替换成你的ffmpeg安装位置。对于usb摄像头，可以在`/dev/`下看看是video多少的编号，然后在配置中输入：

```yaml
 - platform: ffmpeg
    name: webcam
    input: -f v4l2 -r 30 -i /dev/video0
```

就大功告成啦！

笔者家里没有usb摄像头，倒是有一个海康萤石的安防摄像头，如何接入呢？有两种方法。第一种是利用萤石摄像头自带的rtsp协议接入HA，也是用的ffmpeg模块。
```yaml
camera:
  - platform: ffmpeg
    name: ezviz
    input: -rtsp_transport tcp -i rtsp://admin:???@???/h264/ch1/main/av_stream
```

第二种方法是参考[](https://bbs.hassbian.com/forum.php?mod=viewthread&tid=3953&highlight=%E8%90%A4%E7%9F%B3)，首先在HA配置文件夹中新增`custom_components/camera/`文件夹，放入[ezviz.py](https://github.com/c1pher-cn/homeassistan-ezviz/blob/master/custom_components/camera/ezviz.py)文件，随后在配置文件中加入：

```yaml
camera:
  - platform: ezviz
     name: "ezviz"
     id: "???"
     key: "???"
     sec: "???"
```

其中，name是设备名，deviceid（设备序列号）见 https://open.ys7.com/console/device.html ，appkey和appsecret见 https://open.ys7.com/console/application.html 。原理就是每隔30秒调用萤石的api取一次设备图像，这个时间间隔可以在ezviz.py中修改。

虽然在HA中添加了摄像头，但是其图像并不能在Homekit中显示，因为这一部分目前还没有得到实现，怎么办呢？只能退而求其次，用Homebridge来为摄像头桥接了。如何安装Homebridge呢？首先需要安装Nodejs，然后命令行输入：

```zsh
sudo apt-get install libavahi-compat-libdnssd-dev
sudo npm install -g --unsafe-perm homebridge
```

早期HA不支持直接接入Homekit时，还需要利用Homebridge来接入，即安装Homebridge-homeassistant：

```zsh
sudo npm install -g homebridge-homeassistant
```

然后在Homebridge的配置文件`/home/pi/.homebridge/config.json`中写入：

```json
{
    "bridge": {
        "name":"Homebridge",
        "username":"11:22:33:44:55:66",
        "port":51826,
        "pin":"123-45-678"
    },

"platforms": [
  {
    "platform": "HomeAssistant",
    "name": "HomeAssistant",
    "host": "http://127.0.0.1:8123",
    "password": "???",
    "supported_types": ["binary_sensor", "climate", "cover", "device_tracker", "fan", "group", "input_boolean", "light", "lock", "media_player", "remote", "scene", "sensor", "switch"]
  }]
}
```

其中，username是树莓派的MAC地址，port是Homekit的端口默认就好，pin是在 iPhone 上认证 HomeBridge 网关的密码，随喜好输入，而password是在HA中设置的访问密码。

现在HA可以直接接入Homekit了，只需要在`configuration.yaml`中添加一句：

```yaml
homekit:
```

即可，因此Homebridge只需要完成摄像头的接入就可以了。安装好Homebridge后还需要安装Camera-ffmpeg：

```zsh
sudo npm install -g homebridge-camera-ffmpeg
```

安装后，修改Homebridge的配置`config.json`如下：

```json
{
    "bridge": {
        "name":"Homebridge",
        "username":"11:22:33:44:55:66",
        "port":51826,
        "pin":"123-45-678"
 },
"platforms": [
      {
      "platform": "Camera-ffmpeg",
      "cameras": [
        {
          "name": "EZVIZ",
          "videoConfig": {
              "source": "-rtsp_transport tcp -i rtsp://admin:???@???:554/h264/ch1/main/av_stream",
              "maxStreams": 2,
              "maxWidth": 640,
              "maxHeight": 480,
              "maxFPS": 30           
          }
        }]}
]
}
```

最后，运行

```zsh
homebridge -D
```

就可以在Homekit中根据上面的pin添加摄像头啦～

### Media player

之前捣鼓过利用树莓派DIY智能音箱，体验非常差，于是乎听歌还是老老实实手机连接蓝牙音箱。在HA中，也可以指定外接音箱播放特定语音来达到反馈的目的。

media_player组件可以在HA中虚拟出一个播放器，这个播放器和tts（Text to speech）结合就可以让HA说出任何想说的话！首先需要添加一个media_player的运行引擎，我个人喜欢用vlc，最好用的跨平台播放器！首先在树莓派中安装vlc，然后在HA配置中添加：

```yaml
media_player:
  - platform: vlc
```

针对tts的HA组件默认使用的是google，即：

```yaml
tts:
   - platform: google
```

调用tts.google_say服务即可！google的tts非常棒，但是不能说中文，因此如果想要中文的话可以去[百度语音平台](http://yuyin.baidu.com)申请一个免费的应用，用于这里的tts！配置如下：

```yaml
tts:
  - platform: baidu
    app_id: ???
    api_key: ???
    secret_key: ???
```

这样，调用tts.baidu_say服务就可以说话啦！虽然依然存在不足，比如不能有标点符号，比如不能说英文了......

## 4. 自动化（Automation）

自动化是HA的重头戏！智能家居是否“智能”就体现在这里了。基本的自动化有三个要素：

1. Trigger
      自动化触发的条件！是必须要有的环节。
2. Condition
       环境条件，与trigger不同。
3. Action

>Triggers look at the actions, while conditions look at the results: turning a light on versus a light being on.


附上现有的`automation.yaml`，并加以说明：

```yaml
- id: '1542637821107'
  alias: Back home
  trigger:
    - platform: state
      entity_id: binary_sensor.door_window_sensor_158d00023137b7
      from: 'off'
      to: 'on'
  condition:
    - condition: numeric_state
      below: '300'
      entity_id: sensor.illumination_7c49eb17e992
    - condition: template
      value_template:  {% raw %}'{{states.binary_sensor.motion_sensor_kitchen.attributes["No motion since"] | int >= 1200}}' {% endraw %}
  action:
    - service: switch.turn_on
      entity_id: switch.kitchen_left
    - service: light.turn_on
      entity_id: light._5     
    - service: tts.baidu_say
      data:
        entity_id: media_player.vlc
        message: 欢迎回家    

- id: '1543895897994'
  alias: Turn on air purifier
  trigger:
    - platform: numeric_state
      above: '73'
      entity_id: sensor.filtered_pm25
  condition:
    - condition: time
      after: 07:30
      before: '23:00'
      weekday:
        - mon
        - tue
        - wed
        - thu
        - fri
  action:
    - service: fan.turn_on
      entity_id: fan.xiaomi_miio_device    
    - service: fan.set_speed
      data:
        entity_id: fan.xiaomi_miio_device
        speed: Favorite

- id: '1543895897995'
  alias: Good night
  trigger:
    - platform: time
      at: '23:02:00'
  condition:
    - condition: state
      entity_id: fan.xiaomi_miio_device
      state: 'on'
  action:
    service: fan.set_speed
    data:
      entity_id: fan.xiaomi_miio_device
      speed: Silent

- id: '1543895897996'
  alias: Turn air purifier strong
  trigger:
    - platform: numeric_state
      above: '73'
      entity_id: sensor.filtered_pm25
  condition:
    - condition: state
      entity_id: fan.xiaomi_miio_device
      state: 'on'
    - condition: template
      value_template: {% raw %}'{{states.fan.xiaomi_miio_device.attributes["speed"] == "Silent"}}' {% endraw %}
    - condition: time
      after: 07:30
      before: '23:00'
      weekday:
        - mon
        - tue
        - wed
        - thu
        - fri
  action:
    service: fan.set_speed
    data:
      entity_id: fan.xiaomi_miio_device
      speed: Favorite
 
- id: '1543895980971'
  alias: Turn off air purifier
  trigger:
    - platform: numeric_state
      below: '68'
      entity_id: sensor.filtered_pm25      
  condition:
    - condition: time
      after: 07:30
      before: '23:00'
    - condition: state
      entity_id: fan.xiaomi_miio_device
      state: 'on' 
  action:
    service: fan.turn_off
    entity_id: fan.xiaomi_miio_device

- id: '1544346029104'
  alias: Set volume
  trigger:
    - platform: homeassistant
      event: start
  condition: []
  action:
    service: media_player.volume_set
    data:
      entity_id: media_player.vlc
      volume_level: 0.99

- id: '1544883674285'
  alias: Toggle balcony light
  trigger:
    - platform: event
      event_type: xiaomi_aqara.click
      event_data:
        click_type: single
        entity_id: binary_sensor.switch_balcony        
  condition: []
  action:
    service: switch.toggle
    entity_id: switch.rack_light

- id: '1542637821109'
  alias: Turn on balcony light
  trigger:
    - platform: state
      entity_id: binary_sensor.motion_sensor_balcony
      from: 'off'
      to: 'on'
  condition:
    - condition: state
      entity_id: switch.rack_light
      state: 'off'
    - condition: time
      after: '17:00'
      before: 07:00
  action:  
    service: switch.turn_on
    entity_id: switch.rack_light

- id: '1542637821108'
  alias: Turn on toilet light
  trigger:
    - platform: state
      entity_id: binary_sensor.motion_sensor_toilet
      from: 'off'    
      to: 'on'
  condition: []
  action:
    service: switch.turn_on
    entity_id: switch.toilet_left     

- id: '1545558784756'
  alias: Turn off table up
  trigger:
    - platform: state
      entity_id: switch.table_up
      for: 00:03:00
      from: 'off'      
      to: 'on'
  condition: []
  action:
    service: switch.turn_off
    entity_id: switch.table_up

- id: '1545558784757'
  alias: Turn off table down
  trigger:
    - platform: state
      entity_id: switch.table_down
      for: 00:03:00
      from: 'off'      
      to: 'on'
  condition: []
  action:
    service: switch.turn_off
    entity_id: switch.table_down
```

## 5. 优化

有时候，传感器的数值在某个时间会突然跳动很厉害，造成某些自动化的误触发（比如上述的空气质量差时自动打开空净），这时候就要使用一些方法来去掉这些outliers，或者通过滤波来让数据更加平滑。

![]({{ site.baseurl }}/public/images/HA2.png)