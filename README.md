<link href="markdown.css" rel="stylesheet"></link>

# 智能鱼缸微信小程序部分    
*注：为方便验收展示，代码中接收的上行数据均以常数代替*  

### 小组分工  
组员|具体分工
----|-----
徐雪莹	|服务器配置、下行数据发送模块、设置页面和详情页面的页面设计、硬件原型的部分搭建
宋蚺	|上行数据接收模块、用户登陆模块和主页的设计、硬件原型的部分搭建

### 顶层模块划分  
#### 1.服务器配置与通信模块：   
通信模块用于小程序端和服务器的连接、小程序端订阅设备上行数据Topic，是订阅上行数据和发送下行数据的基础。  
#### 2.下行数据发送模块：  
通过小程序端切换开关或更改设置将命令发送至服务器，由上行设备读取命令，实现小程序端对设备端的远程控制。  
#### 3.上行数据接收模块：  
连接服务器，订阅设备上行数据topic，并对来自设备端的信息进行处理和显示。  
#### 4.用户登陆模块：  
具有注册、登陆、通过手机号重置用户名与密码的功能，并在相应步骤给予应有反馈与提示。登陆成功后方能进入主页。   
  
## 具体模块介绍
### 1.服务器配置与通信模块  
#### 1.配置介绍  
由微信小程序开发者文档得，每个微信小程序需要事先设置通讯域名，小程序只可以跟指定的域名进行网络通信，不能使用 IP 地址。且小程序必须使用 HTTPS/WSS 发起网络请求。请求时系统会对服务器域名使用的 HTTPS 证书进行校验，如果校验失败，则请求不能成功发起。所以在配置服务器时必须申请域名和配置SSL证书，又因为网络配置服务器域名只支持https和wss协议（加密的网络通信），还需要对服务器的安全组进行配置，开放相应的支持通信的端口。    
#### 2.配置详情  
![安全组配置](https://github.com/xxy11122/Fish/blob/main/%E5%AE%89%E5%85%A8%E7%BB%84%E9%85%8D%E7%BD%AE.png)  
![域名](https://github.com/xxy11122/Fish/blob/main/%E5%9F%9F%E5%90%8D.png)
![证书](https://github.com/xxy11122/Fish/blob/main/%E8%AF%81%E4%B9%A6.png)  
### 2.下行数据发送模块  
#### 1.功能介绍  
根据硬件设备的上下行传输命令分别在按钮、切换开关绑定事件，当触发相应事件函数时，读取开关/输入框的数据状态，利用client.publish方法，向服务器下行Topic：/mysmarthome/sub发送相应的命令，再由上行设备读取命令，实现小程序端对设备端的远程控制。  
由于硬件读取服务器连续发送的命令会产生丢失，所以需要在发送数据时设置相应的延时。利用setTimeout方法，在连续发送的命令之间产生一定的延时，才能使硬件端正确读取。  
#### 2.上下行传输命令  
```c
//灯光控制命令  
{"target":"LIGHT","value":"00"} //关闭所有灯光  
{"target":"LIGHT","value":"01"} //打开彩灯，关闭白灯  
{"target":"LIGHT","value":"10"} //打开白灯，关闭彩灯  
{"target":"LIGHT","value":"11"} //打开白灯与彩灯  
//换水控制命令  
{"target":"WATER","value":1} //进行一次换水  
//只要换水模块运行一次，就会向云端发送一次消息（硬件软件控制都会返还）  
{"target":"WATER_SUCCESS","value":0} //换水成功返还消息  
//温度控制命令  
{"target":"TEMP_L","value":25} //设置的最低温度  
{"target":"TEMP_H","value":40} //设置的最高温度  
//喂食时间控制命令  
{"target":"FEED_D","value":1} //喂食时间，天  
{"target":"FEED_HO","value":10} //喂食时间，时  
{"target":"FEED_M","value":30} //喂食时间，分钟  
{"target":"FEED_A","value":5} //喂食量，单位：克（g）
```
