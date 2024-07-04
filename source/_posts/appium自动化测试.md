---
title: appium自动化测试
date: 2021-10-12 16:15:16
category:
- Appium
tags:
- Android
- Mac
- 自动化测试
---

#### 使用Appium对Android App 进行自动化测试

##### 一、环境

* node v16.3.0
* python 2.7
* java 13.0.1
* android sdk 31

#### 二、概述

>Appium是一个自动化开源工具，支持iOS、Android和Windows桌面平台上的原生、移动Web和混合应用的自动化。
>Appium是跨平台的：它允许你用同样的API对多平台（iOS、Android、Windows）写测试。
>做到在iOS、Android和Windows测试套件之间复用代码。

#### 三、下载安装环境配置

1、 安装python安装Appium-Python-Client库（写python自动化测试脚本会用到）

```python
pip install Appium-Python-Client
```

2、安装SDK

自行安装即可

3、安装JDK

(1) 在[oracle](https://www.oracle.com/java/technologies/downloads/)官网下载安装JDK，安装JDK8及以上的版本。

(2) 安装完成后，设置JDK的环境变量

(3) 我这里使用的是oh my zsh

```shell
cd ~
vim .zshrc
```

```shell
# 配置ANDROID_HOME环境变量
export ANDROID_HOME="/Users/liwenyang/Library/Android/sdk/"

# 配置JAVA_HOME环境变量
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-13.0.1.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH

# 配置Android sdk tools adb
export PATH="$PATH:/Users/liwenyang/Library/Android/sdk/platform-tools"
```

将jdk版本号、用户名换成自己的 

然后 `source .zshrc`

#### 四、安装appium server

1、在[appium官网](https://github.com/appium/appium-desktop/releases/tag/v1.22.0)下载

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012163841.png)

下载完之后进行安装就行了

2、设置

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012164022.png)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012164101.png)

保存并重启就行了

3、启动

点击上图中的 start server 出现这个说明启动成功 

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012164215.png)

#### 五、还需要一个东西

1、用来定位你app页面中的元素的 写自动化脚本会用到

[点击这个下载](https://github.com/appium/appium-inspector/releases/tag/v2021.9.2)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012164617.png)

正常安装即可

2、配置

真机测试 

USB连接手机，打开手机开发者模式，打开开发者选项中的USB调试、USB安装，小米手机还需要打开USB调试（安全设置），然后在CMD命令行输入adb devices回车，如果出现了手机的设备号，说明连接成功。

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012165710.png)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012165003.png)

```json
{
"platformName": "Android", # 声明是ios还是Android系统
"platformVersion": "8.1.0", # Android内核版本号
"deviceName": "MI_5X", # 连接的设备名称
"appPackage": "com.tencent.qqmusic", # apk的包名
"appActivity": ".activity.AppStarterActivity", # apk的launcherActivity
"resetKeyboard": True,
"noReset": True # 在开始会话之前不要重置应用程序状态
}
```

截图中的 appium前缀是勾选那个就可以加上了
 
下面点击 start session 就可以了

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20211012165937.png)

这个工具就是方便我们进行定位app内的元素用的

#### 六、写自动化测试脚本

随便找一个地方写个 autoTest.py

```python
import time
from appium import webdriver


caps = {
    "platformName": "Android",
    "platformVersion": "9",
    "deviceName": "Note9",
    "appPackage": "com.easylife.house.customer",
    "appActivity": ".ui.pub.GuideActivity",
    "resetKeyboard": True,
    "noReset": True
}
driver = webdriver.Remote("http://localhost:4723/wd/hub", caps)

lunch = driver.find_element_by_id("com.easylife.house.customer:id/activity_launch")
lunch.click()
time.sleep(5)

driver.find_element_by_id("com.easylife.house.customer:id/iv_icon2").click()
time.sleep(5)
driver.swipe(500, 1550, 500, 400)

driver.find_element_by_id("com.easylife.house.customer:id/iv_icon3").click()
time.sleep(3)
driver.swipe(500, 1550, 500, 400)

driver.find_element_by_id("com.easylife.house.customer:id/iv_icon4").click()
time.sleep(3)
driver.swipe(500, 1550, 500, 400)

driver.find_element_by_id("com.easylife.house.customer:id/iv_icon5").click()
time.sleep(3)
driver.swipe(500, 1550, 500, 400)
time.sleep(3)

#点击首页
driver.find_element_by_id("com.easylife.house.customer:id/iv_icon1").click()
time.sleep(3)
driver.find_element_by_id("com.easylife.house.customer:id/newHouse").click()
time.sleep(3)
driver.swipe(500, 1550, 500, 400)

driver.quit()

```

运行这个脚本 `python3 autoTest.py`
脚本就可以自动的进行点击 滑动 等操作了
