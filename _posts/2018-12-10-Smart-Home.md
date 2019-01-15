---
title: 初探智能家居Home Assistant
layout: post
categories:
- Technique
tags:
- Raspberry Pi
- Home Assistant
- HomeBridge
- HomeKit
---

## 前言

![]({{ site.baseurl }}/public/images/HA1.png)

上面的画面是否有一种浓浓的科技感？没有错，这就是苹果在iOS10中带来的智能家居应用HomeKit。图中的每一个元素都是家中电器设备或传感单元的控制接口。为什么笔者要弄智能家居呢？来源于一个非常小的需求：家里的门锁总感觉不可靠，即使有了淘宝神器也不能完全安心，每天还是会纠结门是否关好。聊天的时候淡淡说有那种门窗传感器可以了解一下，于是乎上什么值得买搜了一下，找到了一个讲小米智能家居全家桶排雷的文章，里面介绍到了Home Assistant开源智能家居平台，可以和苹果的HomeKit进行对接，这样就可以在手机上监控家中的情况。这篇文章让我想起了国庆去托马斯家住的时候，早上七点钟灯会自动开起来，当时托马斯很自豪地说这是他设置的智能家居程式，让我羡慕不已，感觉有种赛博朋克的味道！正好，在实验室里面找到了闲置许久的树莓派，拿来做家庭控制中心正好不过了！

<!-- more -->

什么是智能家居？请看Home Assistant官网的概括：

![](https://developers.home-assistant.io/img/en/architecture/home_automation_landscape.svg)

可以看到，智能家居主要分三个层次：
1. Home Control，收集组件的信息并对组件进行控制，同样也接收来自用户的控制并返回信息。
2. Home Automation，根据用户的配置，自动发送控制指令（通过用户指导来替代用户层的操作）。
3. Smart Home，根据各种之前的控制行为与结果，自学习到下一次发送的控制指令（无需用户指导而替代用户层的操作）。

根据上述的架构，显然当前智能家居还停留在自定义的控制规则上，和人工智能的“智能”二字相去甚远，但说到底其本意是为了方便生活中的一些小细节，因此也算是一个很有意义的“大人的玩具”了！再者，当前大的互联网公司在布局自己的智能家居产品时，或多或少会收集一些用户数据，这些数据日后经过训练，都有可能作为一种智能服务提供给消费者，真正做到Smart Home这一级别的智能家居。

那么，这么多智能家居平台，要选择哪一家呢？虽然每个公司都有自己的战略布局，但当前对于开发者的友好程度并不一致。例如，有小米博联这样的“半开放”平台（虽然加塞了自己的东西但是可以很好地接入第三方平台）和阿里京东这样的“封闭”平台（截止目前，天猫精灵AI联盟里面买的东西还是只能用天猫精灵控制，也就是从自家的app里面添加以外别无他法）。其实我们最经常能接触到的智能家居控制中心便是苹果的HomeKit，界面非常符合苹果一贯的简洁风格，但是HomeKit的配件都很贵，预算有限的人（包括我）常常会望而却步，才不得不考虑买其他家的智能家居产品。如果不介意都买一家公司的“全家桶”，那么这家公司自己的智能家庭APP已经可以满足你的需求。如果考虑到价格和性能，每种产品都选择了不同家的公司，那手机里装的APP就要塞不下了。

![]({{ site.baseurl }}/public/images/HA2.png)

因此，为了将很多家智能家居设备整合在一起，需要用到第三方的开放平台。首先要说开源的HomeBridge，它由前HomeKit程序员创建，用Node.js编写，让无法接入HomeKit的第三方产品通过插件兼容桥接入。然后就是本文的重点Home Assistant（HA，下同），它属于新兴力量，基于Python且UI友好，崇尚“Simplicity”，社区活跃而文档丰富，每周都有新版本的小插件发布。此外还有其他一些好平台，比如Domoticz，openHAB等，笔者尚未接触过。根据网路上的描述，OpenHAB基于Java开发，社区非常成熟，但插件审核严格，更新较慢；而Domoticz用C++实现，体积小可扩展性高，但其中文社区不够活跃，文档也相对较少。

![]({{ site.baseurl }}/public/images/HA5.png)

所以，最后综合考虑，我选择了HA作为控制中心！先来上一张效果图吧～对UI没有强迫症的我用的都是默认的UI设置。

![]({{ site.baseurl }}/public/images/HA3.png)

## 1. 安装与配置Home Assistant

任何电脑，任何操作系统，只要能运行python，都可以运行HA：笔记本、台式机、Mac、微型电脑...等等。选择树莓派的原因是自己刚好就有一块，而且放在那里常开功率也不会太大，不会进入睡眠模式。虽然亲测初代B版树莓派也是可以满足需求的，但是在编译安装HA和其他插件的过程中总会出现这样那样的错误，和内存与算力均有关系。考虑到未来会加入摄像头（ffmpeg），或者希望自带WiFi（可以放在任何角落而不需要放在路由器旁边），树莓派三代B+版本是最好的选择。

![]({{ site.baseurl }}/public/images/HA6.png)

因为HA是基于Python的，所以安装只需简单给出命令：

```zsh
$ sudo pip3 install homeassistant
```

运行HA的方法是：
```zsh
$ hass --open-ui
``` 

第一次运行需要来以root权限运行HA，即`sudo hass --open-ui`，其目的是为了安装一些必要的插件（需要权限）。随时去http://树莓派IP地址:8123 看看是不是安装好了。等到安装好以后，以后都只需以普通用户身份运行HA，值得注意的是运行必要的一些配置文件，如`configuration.yaml`，`groups.yaml`和`automations.yaml`等，需要在本地的`.homeassistant/`中再建一个（一般是`/home/{your_username}/.homeassistant/`），原来的配置都放在`/root/.homeassistant`下了。具体需要哪些请在以普通身份运行HA时看终端的输出～下面贴出基本的配置参数：

```yaml
homeassistant:
  # HA的名字
  name: RIVERSIDE
  # 经纬度，用于判断太阳升起落下的时间
  latitude: !secret latitude
  longitude: !secret longitude
  # 海拔
  elevation: 0
  # 公制，英制单位
  unit_system: metric
  # 时区，请在: http://en.wikipedia.org/wiki/List_of_tz_database_time_zones中查找
  time_zone: Asia/Shanghai
  # 个性化设置文件位置，例如改变实体名称，改变在HA中是否可见的状态等等
  customize: !include customize.yaml
  packages: !include_dir_named packages

# 开启前端
frontend:

# 开启配置管理的UI
config:

# 如果使用 SSL/TLS, 或者运行在 Docker 容器内，请注释掉这个
http:
#   旧的验证方式，不安全，但某些插件需要这个密码
  api_password: !secret http_password

# 系统更新器，会上传主机的参数，详见https://home-assistant.io/blog/2016/10/25/explaining-the-updater/
updater:

# 自动嗅探新的设备
discovery:

# 保存设备状态的历史
history:

#保存HA的日志
logbook:

# 查看跟踪设备的地图位置
map:

# 太阳所在的位置
sun:

# 数据库
recorder:

# google文字转语音
tts:
  - platform: google

# 天气预测，使用yr天气组件预测24小时后的温度
sensor:
  - platform: yr
    forecast: 24
    monitored_conditions:
      - temperature
      - dewpointTemperature
      - symbol

# 分组、自动化和脚本文件所在的位置
group: !include groups.yaml
automation: !include automations.yaml
script: !include scripts.yaml
```

如果是在树莓派上，还可以考虑官方的[Hass.io](https://www.home-assistant.io/hassio/)，一个基于Docker的HA系统，将镜像烧录到SD卡上就可以即插即用！笔者不考虑Hass.io的原因是希望在树莓派上还可以运行一些别的程序，比如HomeBridge或者媒体中心之类的，因此就选择了手动安装啦～

## 2. 原理与接入HomeKit

HA的基本原理用其官网的一张图就可以概括：

![](https://developers.home-assistant.io/img/en/architecture/ha_architecture.svg)

可以看到，HA的核心是Events Bus，它不断侦听和产生不同的事件，就好像心脏不停在跳动一般给HA输送血液；各种不同的组件，如灯、开关、传感器等等的状态通过状态机模块记录，一旦有状态变化就会产生一个“状态变化”事件发送给Event Bus；服务中心侦听Events Bus的服务请求，并根据组件们事先注册好的服务来发送控制指令；计时器模块每秒钟发送“时间变化”事件，就好像时钟电路的上升沿触发作用一样。

核心机制在每个HA系统中都是固定的，而让每个HA与众不同的关键便是：组件（Component）。组件分两种，一种是和各种物联网设备交互的组件，另一种是响应HA中发生的各种事件的组件。例如在上述配置文件`configuration.yaml`中，`sensor:`和`tts:`都代表了组件。我们来看看HA中“当太阳下山，而家里有人时打开灯”这一过程中涉及的组件：

![](https://developers.home-assistant.io/img/en/architecture/component_interaction.png)

1. 第一种组件将不同的物联网设备封装起来，通过统一的接口向外提供状态信息，并在服务中心注册服务使得HA能够控制它们。例如不同类型的开关都可以通过Switch组件接入HA，它们在HA中的状态都是“开/关”，并且都可以呼叫服务对它们进行“打开开关”和“关闭开关”的控制操作。
2. 第二种组件其实就是一些系统内置和用户自定义的自动化操作，我的理解是将前一种类型组件的状态和这些组件对应的服务进行打包的一个集合。

把HA和上述智能家居层次图进行对比，可以看到HA实际上是Home Control+Home Automation的集合，Smart Home功能并没有实现（毕竟需要大量数据）。展开来看，完整的HA架构和流程如下图所示（以灯组件和开灯自动化为例）：

![](https://developers.home-assistant.io/img/en/architecture/ha_full_architecture.png)

因此，HA确实是一个相对复杂的系统（相较于其他几个系统已经好很多了），那么如果想更简单地监测和控制家里的电器，要怎么办呢？这里就不得不说苹果的HomeKit了，真·傻瓜式操作，界面清新。可以将HA和HomeKit进行对接吗？答案是完全可以！这里列举一下使用HA对接HomeKit的几个好处：

1. 可以用Siri来控制家电啦！
2. 有一台iPad或者Mac的话，就能作为家庭中心，在外面也能访问HomeKit，进而控制家电。HA的外网接入比较麻烦。

最早将第三方平台接入HomeKit的方法是利用HomeBridge，例如米家网关利用HomeBridge-Mi-Aqara插件接入HomeBridge，从而接入HomeKit。这种方法的限制是只能够一次控制一类家居，比如 HomeBridge-Mi-Aqara 就只能控制米家类家居。而Home Assistant的引入让整个平台更加统一，兼容，因此后来有了HomeBridge-HomeAssistant插件让Home Assistant通过HomeBridge桥接进HomeKit。最后，HomeBridge-HomeAssistant这个东西被抛弃了，Home Assistant选择原生直接接入HomeKit，虽然有一些限制（仅特定的Components能接入，详情看https://www.home-assistant.io/components/HomeKit/#supported-components ，包括开关、传感器、风扇和灯具等等）。下面总结了这三种方法是如何接入HomeKit与用户交互的。

![]({{ site.baseurl }}/public/images/HA7.png)

那么如何安装HomeBridge呢？首先需要安装Nodejs，然后命令行输入：

```zsh
sudo apt-get install libavahi-compat-libdnssd-dev
sudo npm install -g --unsafe-perm HomeBridge
```

安装不再维护的HomeBridge-homeassistant插件：

```zsh
sudo npm install -g HomeBridge-homeassistant
```

然后在HomeBridge的配置文件`/home/pi/.HomeBridge/config.json`中写入：

```json
{
    "bridge": {
        "name":"HomeBridge",
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

其中，username是树莓派的MAC地址，port是HomeKit的端口默认就好，pin是在 iPhone 上认证 HomeBridge 网关的密码，随喜好输入，而password是在HA中设置的访问密码。最后的supported_types就是你希望支持的组件啦！最后，运行

```zsh
HomeBridge -D
```

即可将HA通过HomeBridge接入HomeKit！那么，HA中原生支持的HomeKit组件怎么添加呢？只需要在配置中写上：

```yaml
homekit:
```

需要注意初次启用HomeKit组件后，HA 主页会出现 PIN 码，若没有出现，删除配置文件夹下 `.homekit.state` 重试。如果想要让一些HA中的组件不要在HomeKit中出现，只需要在filter域中添加` exclude_domains`和` exclude_entities`部分即可，例如：

```yaml
homekit:
  filter:
    exclude_domains: 
      - automation
    exclude_entities:
      - light._2
      - binary_sensor.k_no_motion_for_20
      - binary_sensor.prone_to_wake
```

## 3. 设备接入

可以看到，让HA多姿多彩的重要因素是不断扩充的组件大家庭。那么，究竟在配置中要添加哪些组件呢？这还得根据你买了哪些设备来添加。为了缩减配置文件`configuration.yaml`的规模，同时也是便于管理，可以将配置文件根据组件类型分成不同的几个文件，例如：

```yaml
# 传感器:
sensor: !include sensor.yaml

# 二进制传感器:
binary_sensor: !include binary_sensor.yaml

# 灯具
light: !include lights.yaml

# 开关：
switch: !include switch.yaml
```

接下来，开始细说笔者家中有哪些设备，是如何接入HA的～

### 红外/射频遥控器

为了解决平时找遥控器怎么也找不到的难题，把所有遥控器控制集中在手机上是个不错的选择。目前市面上的遥控器不是红外控制就是射频控制，由于这两个频段不一样（包括射频自己的频段选择），因此诸如小米天猫万能遥控器有时候是不管用的。可以自己购买无线收发模块DIY，但如果追求省事那就购买博联的RM Pro吧！配置很简单，在接入网络获取到ip后，在`configuration.yaml`里面加入如下字段（以飞利浦电视开关为例）：

```yaml
switch:
  - platform: broadlink
    host: 'IP_ADDRESS'
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

目前加入的遥控设备有：电视（开关，输入源选择，音量大小），阳台自动晾衣杆（照明，上升，下降），自动升降桌（上升，下降），松下遥控吸顶灯。此外，还可以利用HA的cover组件对升降类设备进行cover的模拟（卷帘车库门，自动卷帘窗等等），例如：

```yaml
# 利用博联开关模拟车库门/卷帘窗操作（实际用处不大，因为状态不可控）
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
```

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

有没有更简单的方法呢？有，就是直接利用串口烧写现成的一些成熟系统，例如ESPEasy和ESPHome，然后就可以在网页上任意配置了！结合MQTT服务，可以让Nodemcu成为数据转发中心，但是我对MQTT不是非常了解，只得作罢。当前，仅用于控制简单的LED灯带而已，操作非常简单：先按照教程刷入ESPEasy，初始化后得到Nodemcu的ip地址，然后就可以在浏览器中输入简单的http请求来控制MCU的GPIO啦！例如输入`http://{ip_address}/control?cmd=GPIO,12,1`就可以让GPIO12拉出高电平了！接下来，在HA中添加command_line的开关组件：

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
    host: ???
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
  host: ???
  username: ???
  ssh_key: path_to_sshkey
```

这里使用ssh而不是密码的原因是为了安全，需要在路由器的控制中心中打开ssh访问（LAN only）。

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

虽然在HA中添加了摄像头，但是其图像并不能在HomeKit中显示，因为这一部分目前还没有得到实现，怎么办呢？只能退而求其次，用HomeBridge来为摄像头桥接了。安装Camera-ffmpeg插件：

```zsh
sudo npm install -g HomeBridge-camera-ffmpeg
```

安装后，修改HomeBridge的配置`config.json`如下：

```json
{
    "bridge": {
        "name":"HomeBridge",
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

运行HomeBridge后就可以在HomeKit中根据上面的pin添加摄像头啦～

FFmpeg还有另外一个功能，那就是运动检测，怎么做到呢？因为现在的视频压缩算法需要判断当前的画面和之前画面的差别，如果差别很大就会产生一个新的场景（scene），这一特性刚好可以用来判断静止的相机画面中是否有运动。正好，HA中就有一个FFmpeg Motion的组件：

```yaml
# ffmpeg motion组件，根据视频压缩中scene change的特性进行画面中的运动检测，详见https://www.home-assistant.io/components/binary_sensor.ffmpeg_motion/
- platform: ffmpeg_motion
  input: rtsp://admin:???@???/h264/ch1/main/av_stream
  changes: 2
  extra_arguments: -filter:v "crop=in_w/2:in_h:in_w/2:0"
```

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
  自动化触发的事件！是必须要有的环节。例如：“灯从**关闭**状态到**打开**状态”，“温度低于**18**度时”，“Home Assistant**初次运行**时”，这些都是可以作为触发的事件，重点在于“状态的改变”。
2. Condition
  环境条件，与trigger不同，重点在“当前状态”。例如，“当前太阳**落山**”，“当前**灯**开着”，都是环境状态条件。
3. Action
  运行一系列操作！例如打开灯，关闭风扇等等。

常常有人会把Trigger和Condition搞混，其实很好理解，看看官网上怎么说的：
>Triggers look at the actions, while conditions look at the results: turning a light on versus a light being on.

接下来，举几个笔者写在`automation.yaml`里的例子，并加以说明：

```yaml
# 回家时打开客厅和厨房的灯，并根据门口监控判断回来的是谁，播放欢迎词。判断回家的条件是：门窗传感器打开，并且此时室内无人，光照度小于250。
- id: id1
  alias: Back home
  trigger:
  - platform: state
    entity_id: binary_sensor.door_window_sensor_158d00023137b7
    from: 'off'   
    to: 'on'
  condition:
  - condition: numeric_state
    entity_id: sensor.illumination_7c49eb17e992
    below: 250
  - condition: state
    entity_id: binary_sensor.motion_sensor_kitchen
    state: 'off'
  action:
  - entity_id: switch.kitchen_left
    service: switch.turn_on
  - entity_id: light._5
    service: light.turn_on
  - service: shell_command.recog_people
# 睡觉时候，让空气净化器进入睡眠模式。当前判断睡觉的条件是贝叶斯传感器的睡觉概率。
- id: id2
  alias: Good night
  trigger:
  - platform: state
    entity_id: binary_sensor.sleeping
    from: 'off'
    to: 'on'
  condition:
  - condition: state
    entity_id: fan.xiaomi_miio_device
    state: 'on'
  action:
    service: fan.set_speed
    data:
      entity_id: fan.xiaomi_miio_device
      speed: Silent
# Home Assistant开机时，音量调到最大。这是vlc的一个bug修复自动化...
- id: id3
  alias: Startup
  trigger:
  - event: start
    platform: homeassistant
  condition: []
  action:
  - service: media_player.volume_set
    data:
      entity_id: media_player.vlc
      volume_level: 0.99
  - service: cover.close_cover
    entity_id: cover.garage_door
  - service: switch.turn_off
    entity_id: switch.table_down
# 如果AQI高于73，打开空气净化器并调整至最强风。
- id: id4
  alias: Turn on air purifier
  trigger:
  - platform: numeric_state
    above: '73'
    entity_id: sensor.filtered_pm25
  condition:
  - condition: state
    entity_id: binary_sensor.sleeping
    state: 'off'
  - condition: state
    entity_id: fan.xiaomi_miio_device
    state: 'off'
  action:
  - service: fan.turn_on
    entity_id: fan.xiaomi_miio_device
  - service: fan.set_speed
    data:
      entity_id: fan.xiaomi_miio_device
      speed: Favorite
# 如果AQI小于68，关掉空气净化器。
- id: id5
  alias: Turn off air purifier
  trigger:
  - platform: numeric_state
    below: '68'
    entity_id: sensor.filtered_pm25
  condition:
  - condition: state
    entity_id: binary_sensor.sleeping
    state: 'off'
  - condition: state
    entity_id: fan.xiaomi_miio_device
    state: 'on'
  action:
    service: fan.turn_off
    entity_id: fan.xiaomi_miio_device
# 如果有人单击无线开关，改变阳台灯的状态（开/关）
- id: id6
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
    entity_id: switch.balcony_light
# 在太阳下山时，如果阳台有人，且灯是关闭状态，则把阳台灯打开。
- id: id7
  alias: Turn on balcony light
  trigger:
  - entity_id: binary_sensor.motion_sensor_balcony
    from: 'off'
    platform: state
    to: 'on'
  condition:
  - condition: state
    entity_id: switch.balcony_light
    state: 'off'
  - condition: state
    entity_id: sun.sun
    state: 'below_horizon'
  action:
  - entity_id: switch.balcony_light
    service: switch.turn_on
# 来自小米智能网关的光照传感器bug：当前光照会和前100次采样结果进行平均，因此数据更新经常延时。通过每3分钟不断开关智能网关的夜灯来强制更新采样结果。
- id: id8
  alias: Update lumen sensor
  trigger:
  - platform: time
    minutes: /3
    seconds: 0
  action:
  - data:
      brightness: 2
      entity_id: light.gateway_light
    service: light.turn_on
  - delay:
      seconds: 1
  - entity_id: light.gateway_light
    service: light.turn_off
# 早上Zoey出门十分钟后，把主卧插座给关了。
- id: id9
  alias: Turn off charging power
 trigger:
  - entity_id: binary_sensor.door_window_sensor_158d00023137b7
    for: 
      minutes: 10
    from: 'on'
    platform: state
    to: 'off'
  condition:
  - condition: state
    entity_id: switch.plug_158d000237cd54
    state: 'on'
  - condition: state
    entity_id: device_tracker.zoey
    state: not_home
  action:
  - entity_id: switch.plug_158d000237cd54
    service: switch.turn_off
```

## 5. 进阶部分

### 5.1 数据优化
有时候，传感器的数值在某个时间会突然跳动很厉害，造成某些自动化的误触发（比如上述的空气质量差时自动打开空净），这时候就要使用一些方法来去掉这些outliers，或者通过滤波来让数据更加平滑。例如，可以使用Statistics sensor模块来控制：

```yaml
sensor:
  - platform: template
    sensors:
      xiaomi_ap_aqi_raw:
        friendly_name: AQI Raw
        value_template: "{{ states.fan.xiaomi_miio_device.attributes.aqi }}"
        unit_of_measurement: AQI
  - platform: statistics
    name: "xiaomi_ap_statistics"
    entity_id: sensor.xiaomi_ap_aqi_raw  
  - platform: template
    sensors:                          
      xiaomi_aqi_mean:
        unit_of_measurement: AQI
        value_template: "{{ states.sensor.xiaomi_ap_statistics.attributes.mean }}"
```

除了mean，还有min，max，median，STD等参数可以得到统计！另一种方法便是使用Filter sensor模块来滤波，例如：

```yaml
sensor:
 - platform: template
    sensors:
      xiaomi_ap_aqi_raw:
        friendly_name: AQI Raw
        value_template: "{{ states.fan.xiaomi_miio_device.attributes.aqi }}"
        unit_of_measurement: AQI
  - platform: filter
    name: "Filtered AQI"
    entity_id: sensor.xiaomi_ap_aqi_raw
    filters:
      - filter: lowpass
        time_constant: 10
      - filter: time_simple_moving_average
        window_size: 00:05
        precision: 2
```

效果如下，能够最大程度减少误触发！

![]({{ site.baseurl }}/public/images/HA4.png)

### 5.2 数据库替换

HA原生的SQLite数据库有时候太慢啦！能不能替换成别的数据库呢？这里介绍一下如何替换成PostgreSQL数据库，参考[墨澜的博客](http://cxlwill.cn/Home-Assistant/HomeAssistant-PostgreSQL/)。首先安装PostgreSQL：

```zsh
sudo apt-get install postgresql postgresql-server-dev-9.6
sudo pip3 install psycopg2
```

其中postgresql-server-dev版本号自查。然后创建PostgreSQL数据库，这里沿用HA的用户名（例如用户名为pi），密码假设设为raspberry，那么：

```zsh
sudo -u postgres createuser pi
sudo -u postgres psql
ALTER USER pi WITH PASSWORD 'raspberry';
\q
```

之后创建名为homeassistant的数据库：

```zsh
sudo -u postgres createdb -O pi homeassistant
```

在HA的配置文件中，加入：

```yaml
recorder:
  db_url: postgres://pi:raspberry@localhost/homeassistant
```

重启即可体验飞一般的感觉～

### 5.3 与开放AI平台结合

AI应用的例子：如何在开门时进行人形识别，判断是谁回来了，并且播放欢迎词？以下以百度AI的人体分析为例。

首先，去ai.baidu.com申请一个开发账号，在人工智能-人体分析中创建一个新的应用，获得Api Key和Secret Key。然后，在本地新建一个python文件，例如`body_recognition.py`。利用api key和secret key获得一个可用的token：

```python
import requests

apikey_baidu = '???'
apisecret_baidu = '???'

host_baidu_token = 'https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials'
token_baidu = requests.post(host_baidu_token, data={'client_id':apikey_baidu, 'client_secret':apisecret_baidu}).json()
```

然后，获取一张当前摄像头的图片，并且转换为Base64编码（百度人体识别api的要求，但类似于萤石开放平台的人体识别api就可选直接输入图片url，然而笔者写下这段话的时候该功能还未开通...）。图片转换为Base64编码的方法很简单，在python的base64库中就可以利用`b64encode`方法来进行编码。那么图片如何获取呢？有两种方法，一种是利用摄像头自带的rtsp协议地址，让ffmpeg来帮你截图。这里利用subprocess来运行外部指令：

```python
from subprocess import call
import base64

call(["ffmpeg", "-i", "rtsp://{username}:{password}@{ip_address}:554/h264/ch1/main/av_stream", "-f", "image2", "-t", "0.001", "-y", "/home/pi/tmpimg/snapshot.jpg"])
with open("/home/pi/tmpimg/snapshot.jpg", "rb") as f:
  base64_data = base64.b64encode(f.read())
```

或者，利用萤石开放平台的api获得当前截图的网络地址，同样需要获得设备的序列号和accessToken，具体方法不展开，请到open.ys7.com查找：

```python
result = requests.post('https://open.ys7.com/api/lapp/device/capture', data={'accessToken':'???','deviceSerial':'???','channelNo':1}).json()
if (result['code']=='200'):
  imgurl = result['data']['picUrl']
else:
  imgurl = ''
base64_data = base64.b64encode(io.BytesIO(requests.get(imgurl).content).read())
```

不管用何种方法得到base64编码后的图片，接下来都可以上传到服务器上进行识别啦！

```python
host_baidu_body = 'https://aip.baidubce.com/rest/2.0/image-classify/v1/body_attr'
result = requests.post(host_baidu_body, data={'access_token':token_baidu['access_token'],'image':base64_data, 'type': 'gender,age,glasses'}).json()
```

这里的type可以选取你想要得到的信息，我想要得到的是性别、年龄和是否戴眼镜。所有可选的属性包括以下几种，虽然大部分的用处都不多：

```
gender-性别，
age-年龄阶段，
lower_wear-下身服饰，
upper_wear-上身服饰，
headwear-是否戴帽子，
glasses-是否戴眼镜，
upper_color-上身服饰颜色，
lower_color-下身服饰颜色，
cellphone-是否使用手机，
upper_wear_fg-上身服饰细分类，
upper_wear_texture-上身服饰纹理，
orientation-身体朝向，
umbrella-是否撑伞；
bag-背包,
smoke-是否吸烟,
vehicle-交通工具,
carrying_item-是否有手提物,
upper_cut-上方截断,
lower_cut-下方截断,
occlusion-遮挡
```

为了防止截图中没有人，设置一个循环直到上述返回值中`person_num`参数不为0，并设置一个`timeout`参数控制循环上限:

```python
timeout = 10
while result['person_num'] == 0:
  call(["ffmpeg", "-i", "rtsp://{username}:{password}@{ip_address}:554/h264/ch1/main/av_stream", "-f", "image2", "-t", "0.001", "-y", "/home/pi/tmpimg/snapshot.jpg"])
  with open("/home/pi/tmpimg/snapshot.jpg", "rb") as f:
    base64_data = base64.b64encode(f.read())
  result = requests.post(host_baidu_body, data={'access_token':token_baidu['access_token'],'image':base64_data, 'type': 'gender,age,glasses'}).json()
  timeout = timeout-1
  if timeout == 0:
  	quit()
```

接下来，调用HA的service！这里需要import一个叫做`homeassistant.remote`的模块，然后传递HA的api密码（即在设置中的`api_password`），接着就可以自由呼叫服务了！以判断性别和人数为例，让HA报出不同的欢迎词：

```python
import homeassistant.remote as remote

api = remote.API('127.0.0.1', '{api_password}')
domain = 'tts'
entity_id = 'media_player.vlc'
message1 = "欢迎你们"
message2 = "欢迎女主人"
message3 = "欢迎男主人"
message4 = "欢迎回家"

if result['person_num'] > 1:
  remote.call_service(api, domain, 'baidu_say', {'entity_id': entity_id, 'message': message1})
elif result['person_info'][0]['attributes']['gender']['score'] < 0.75:
  remote.call_service(api, domain, 'baidu_say', {'entity_id': entity_id, 'message': message4})
elif result['person_info'][0]['attributes']['gender']['name'] == "女性":
  remote.call_service(api, domain, 'baidu_say', {'entity_id': entity_id, 'message': message2})
else:
  remote.call_service(api, domain, 'baidu_say', {'entity_id': entity_id, 'message': message3})
```

那么，怎么运行这个外部的脚本呢？需要使用到Shell Command组件。在`configuration.yaml`里面加入：

```yaml
shell_command:
  body_recognition: python3 /home/pi/body_recognition.py
```

这样，在HA的自动化中就可以用`service: shell_command.body_recognition`来调用这个脚本啦～

### 5.4 Variable的使用与贝叶斯传感器

详见[Useful Sensor: Bayesian Sleep Detection in Home Assistant](https://diyfuturism.com/index.php/2017/12/29/useful-sensor-bayesian-sleep-detection-in-home-assistant/) 和 [Useful Sensor: Motion Last Seen & Meta Motion Sensor](https://diyfuturism.com/index.php/2017/12/15/useful-sensor-motion-last-seen-________/)

在配置中添加variable组件：

```yaml
# 变量控制，详见https://github.com/rogro82/hass-variables
variable:  
  last_motion:
    value: 'Unknown'
    restore: true
    attributes:
      icon: mdi:alarm
      friendly_name: 'Last Motion'
```

然后在自动化文件中添加自动化：

```yaml
# 房间内最后有人移动出现在哪里？
- id: id10
  alias: Update Last Motion
  trigger:
    - platform: state
      entity_id: binary_sensor.motion_sensor_toilet, binary_sensor.motion_sensor_balcony, binary_sensor.motion_sensor_kitchen, binary_sensor.ffmpeg_motion
      to: 'on'
  action:
    - service: variable.set_variable
      data:
        variable: last_motion
        attributes_template: >
            {
              "history_1": "{{ variable.state }}",
              "history_2": "{{ variable.attributes.history_1 }}",
              "history_3": "{{ variable.attributes.history_2 }}"
            }
      data_template:
        value: "{{ trigger.to_state.attributes.friendly_name }}"
```

在配置文件中添加基于贝叶斯传感器组件的睡眠传感器
```yaml
# 贝叶斯传感器检测是否睡觉，详见https://www.home-assistant.io/components/binary_sensor.bayesian/
binary_sensor:
  - platform: bayesian
    prior: 0.33
    name: 'Sleeping'
    probability_threshold: 0.85
    observations:
      - entity_id: 'variable.last_motion'
        prob_given_true: 0.1
        prob_given_false: 0.8
        platform: 'state'
        to_state: 'FFmpeg Motion'
      - entity_id: 'variable.last_motion'
        prob_given_true: 0.6
        prob_given_false: 0.2
        platform: 'state'
        to_state: 'Toilet Motion'
      - entity_id: 'variable.last_motion'
        prob_given_true: 0.45
        prob_given_false: 0.3
        platform: 'state'
        to_state: 'Kitchen Motion'
      - entity_id: 'group.all_light'
        prob_given_true: 1.0
        prob_given_false: 0.8
        platform: 'state'
        to_state: 'off'
      - entity_id: 'device_tracker.zoey'
        prob_given_true: 1
        prob_given_false: 0.625
        platform: 'state'
        to_state: 'not_home'
      - entity_id: 'device_tracker.simon'
        prob_given_true: 0.8
        prob_given_false: 0.625
        platform: 'state'
        to_state: 'not_home'
      - entity_id: 'sensor.illumination_7c49eb17e992'
        prob_given_true: 0.8
        prob_given_false: 0.4
        platform: 'numeric_state'
        below: 100
      - entity_id: 'binary_sensor.prone_to_wake'
        prob_given_true: 0.3
        prob_given_false: 0.7
        platform: 'state'
        to_state: 'on'
      - entity_id: 'switch.plug_158d000237cd54'
        prob_given_true: 0.7
        prob_given_false: 0.5
        platform: 'state'
        to_state: 'on'
      - entity_id: 'binary_sensor.k_no_motion_for_20'
        prob_given_true: 1
        prob_given_false: 0.825
        platform: 'state'
        to_state: 'on'
      - entity_id: 'binary_sensor.b_no_motion_for_20'
        prob_given_true: 1
        prob_given_false: 0.825
        platform: 'state'
        to_state: 'on'
      - entity_id: 'binary_sensor.t_no_motion_for_20'
        prob_given_true: 1
        prob_given_false: 0.825
        platform: 'state'
        to_state: 'on'
```

其中，可能醒着的时间段和每个地方是否无人超过20分钟是通过模版实现的：
```yaml
binary_sensor:
  # 可能醒着的时间段（睡眠时间段的反）
  - platform: template
    sensors:
      prone_to_wake:
        value_template: {% raw %}'{{ states.sensor.time.state > "09:00" and states.sensor.time.state <= "21:59"}}'{% endraw %}
  # 20分钟无人二进制传感例子
  - platform: template
    sensors:
      k_no_motion_for_20:
        value_template: {% raw %}'{{states.binary_sensor.motion_sensor_kitchen.attributes["No motion since"] | int >= 1200}}'{% endraw %}
```

为了Debug，可以随时监测贝叶斯传感器的触发概率：

```yaml
# 贝叶斯睡觉传感器的概率记录，用于debug
sensor:
  - platform: template
    sensors:
      sleeping_probability:
        friendly_name: "睡觉概率"
        value_template: {% raw %}"{{ states.binary_sensor.sleeping.attributes.probability | float * 100 }}"{% endraw %}
        unit_of_measurement: "%"
```