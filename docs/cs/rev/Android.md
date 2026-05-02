# Android 逆向入门

> https://www.52pojie.cn/thread-408645-1-1.html

52pj上的教程（注册之后还没怎么看过这个论坛，心血来潮）

### 一、环境搭建

安装雷电模拟器，Magisk和插件

### 二、初识APK文件结构、双开、汉化、基础修改

#### APK结构

apk 全称 Android Package，它相当于压缩文件，只要在电脑上将apk后缀改为zip即可解压。

| 文件夹              | 作用                                                         |
| ------------------- | ------------------------------------------------------------ |
| assets              | 存放APK的静态资源文件，比如视频，音频，图片等                |
| lib                 | armeabi-v7a基本通用所有android设备，arm64-v8a只适用于64位的android设备，x86常见用于android模拟器，其目录下的.so文件是c或c++编译的动态链接库文件 |
| META-INF            | 保存应用的签名信息，签名信息可以验证APK文件的完整性，相当于APK的身份证(验证文件是否又被修改) |
| res                 | res目录存放资源文件，包括图片，字符串等等，APK的界面由layout文件设计 |
| AndroidManifest.xml | APK的应用清单信息，它描述了应用的名字，版本，权限，引用的库文件等等信息 |
| classes.dex         | classes.dex是java源码编译后生成的java字节码文件，APK运行的主要逻辑 |
| resources.arsc      | resources.arsc是编译后的二进制资源文件，它是一个映射表，映射着资源和id，通过R文件中的id就可以找到对应的资源 |

#### 双开及原理

双开：简单来说，就是手机同时运行两个或多个相同的应用，例如同时运行两个微信

| 原理               | 解释                                                         |
| :----------------- | :----------------------------------------------------------- |
| 修改包名           | 让手机系统认为这是2个APP，这样的话就能生成2个数据存储路径，此时的多开就等于你打开了两个互不干扰的APP |
| 修改Framework      | 对于有系统修改权限的厂商，可以修改Framework来实现双开的目的，例如：小米自带多开 |
| 通过虚拟化技术实现 | 虚拟Framework层、虚拟文件系统、模拟Android对组件的管理、虚拟应用进程管理 等一整套虚拟技术，将APK复制一份到虚拟空间中运行，例如：平行空间 |
| 以插件机制运行     | 利用反射替换，动态代理，hook了系统的大部分与system—server进程通讯的函数，以此作为“欺上瞒下”的目的，欺骗系统“以为”只有一个apk在运行，瞒过插件让其“认为”自己已经安装。例如：VirtualApp |

修改包名：MT管理器→提取安装包→功能→APK共存就可以了

如果开发者进行了签名校验就不能用这种简单的方式

### 三、汉化APK

汉化：使用专门的工具对外文版的软件资源进行读取、翻译、修改、回写等一系列处理，使软件的菜单、对话框、提示等用户界面显示为中文，而程序的内核和功能保持不变，这个过程即为软件汉化

字符串主要存放的地方：Arsc，XML，Dex

### 四、AndroidManifest.xml

AndroidManifest.xml文件是整个应用程序的信息描述文件，定义了应用程序中包含的Activity,Service,Content provider和BroadcastReceiver组件信息。每个应用程序在根目录下必须包含一个AndroidManifest.xml文件，且文件名不能修改。它描述了package中暴露的组件，他们各自的实现类，各种能被处理的数据和启动位置。

| 属性                               | 定义                                                     |
| :--------------------------------- | :------------------------------------------------------- |
| versionCode                        | 版本号，主要用来更新，例如:12                            |
| versionName                        | 版本名，给用户看的，例如:1.2                             |
| package                            | 包名，例如：com.zj.52pj.demo                             |
| uses-permission android:name=""    | 应用权限，例如：android.permission.INTERNET 代表网络权限 |
| android:label="@string/app_name"   | 应用名称                                                 |
| android:icon="@mipmap/ic_launcher" | 应用图标路径                                             |
| android:debuggable="true"          | 应用是否开启debug权限                                    |