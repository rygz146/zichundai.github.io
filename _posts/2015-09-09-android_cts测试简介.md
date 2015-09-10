---
layout: post
title:  "Android CTS测试简介"
date:   2015-09-09 08:43:59
author: DaiJian
categories: Android
tags:	android cts 
cover:  "assets/instacode.png"
---
###一、  Android CTS测试简介
CTS  全称Compatibility  Test  Suite 兼容性测试工具。当电子产品开发出来，并定制了自己
的Android 系统后，必须要通过最新的 CTS 检测，以保证标准的 android  application 能运行在该平台下。通过了 CTS 验证，需要将测试报告提交给 Google，已取得 android market的认证。 CTS 是一款通过命令行操作的工具。目前 cts 没有提供 windows 版本，只能在 Linux 下测试。本文将详细介绍在ubantu系统上如何搭建CTS环境，以及运行测试计划和分析测试结果。

###二、  Android CTS测试环境搭建
1. 下载android-SDK linux版本，下载地址：
*http://developer.android.com/sdk/index.html*
2. 下载android-cts包,下载地址：
*http://source.android.com/compatibility/downloads.html* 
  根据手机系统版本下载对应版本的cts包，否则将无法运行测试
3. jdk的安装：
*http://source.android.com/source/initializing.html*
在ubuntu系统中打开终端，输入命令：
`$ sudo add-apt-repository "deb http://archive.canonical.com/ lucid partner"
$ sudo apt-get update
$ sudo apt-get install sun-java6-jdk`

``$ sudo ln -s /usr/lib/i386-linux-gnu/libX11.so.6 /usr/lib/i386-linux-gnu/libX11.so``
 
4. 将android-sdk和android-cts解压放置在同一个文件夹

5. 在CTS/tools中修改脚本设置环境变量
进入android-cts/tools目录下，修改startcts文件，将脚本中的SDK_ROOT改成自己的SDK路径，如：
SDK_ROOT=/home/jackmiao/workspace/CTS/android-cts/tools
  
 
6. 设置ubantu系统环境变量
进入ubantu系统用户目录下按Ctrl+H，打开.bashrc在最后面加
PATH=$PATH:$HOME/bin: /home/jackmiao/CTS/android-sdk-linux/tools: /home/jackmiao/CTS /android‐sdk‐linux/platform‐tools: /home/ jackmiao/CTS /android‐cts/tools:
其中红色部分为sdk和cts工具的路径请根据自己的实际路径创建

###三、  Android-CTS测试的几个概念
在进行 CTS 测试之前，先阐述 CTS 测试中几个概念： 
Ø  Test Plan（Plan）：测试计划，Test package 的集合，每个 Plan 中都包含若干个测试包 
以android –cts 2.2版本为例,总共有8个测试计划
CTS：包含2万多个测试用例，这些测试用例是检验兼容性必须的，性能测试不包含在本计划中，随版本的更新，本测试计划也会更新。
Signature：包含所有针对公有APIs的署名测试
Android：包含针对android APIs的所有测试
Java：包含所有针对Java核心library的测试
VM：包含对虚拟机的所有测试
RefApp：包含针对参与应用程序的所有测试，随版本的更新，本测试计划也会更新
Performance：包含所有针对性能的测试，随版本的更新，本测试计划也会更新
AppSerurity：针对Application安全性的测试
Ø  Test Package（Package）：测试包，Test case的集合 
Ø  Test case：测试用例，Test 的集合 
Ø  Test：测试，每一个测试对应一个或者多个 Instrumentation Test 
Ø  Instrumentation  Test：Android 测试环境的核心是一个Instrumentation 框架，在这个框架下，你的测试应用程序可以精确控制应用程序。使用Instrumentation，你可以在主程序启动之前，创建模拟的系统对象，如 Context；控制应用程序的多个生命周期；发送 UI 事件给应用程序；在执行期间检查程序状态。Instrumentation 框架通过将主程序和测试程序运行在同一个进程来实现这些功能。
Ø  Result_Type：CTS 的测试结果可以通过命令查阅，也可以通过浏览器查看下结果文件
 
命令查阅结果： 
 
 
其中 Test result  有四种类型的值：Pass，  Fail，  Timeout，  NoExecuted 
浏览器查看 testResult.xml. 路径：  …/android‐cts/repository/results/
 
上图为performance plan所包含的Test package的测试结果，可以看到每个测试包的测试情况，详细的测试结果分析我们将在 CTS 测试结果分析中介绍，这里主要让大家明确 result_type 的含义。 

###四、  终端测试前准备
1. 检测手机是否连接上电脑
打开终端输入命令：adb devices ，连接上这会显示设备ID，如果未连接上则会以？？？？？？？代替,此时可以通过输入以下命令进行连接
$ adb kill-server
$ sudo adb start-server
$ adb devices
 
 
2. 手机端安装CtsDelegatingAccessibilityService.apk
 
此包位于android-cts/repository/testcases下面。可以将安装包拷贝到手机后安装也可通过打开终端输入命令。输入命令方法：先定位到plantform-tools文件夹，cd cts/android-sdk-linux/platform-tools然后再输入安装命令：./adb install /安装包的路径/CtsDelegatingAccessibilityService.apk
安装好后设置进入手机设在菜单：
Setting>Accessibility>Accessibility>DelegatingAccessibilityService
 
3. 终端设置：
1)  语言设置为English
2)  去掉锁屏(Settings > Security & location &security>Set up screen lock>none )
3)  设置屏幕超时为最长时间30分钟(Settings>Display>Screen timeout>30minutes)
4)  设置唤醒状态(Settings>Applications>Development>Stay awake)
5)  设置虚拟地点(Settings > Application > Development > Allow mock locations is set)
6)  运行时让屏幕是home主界面上
7)  运行过程中不要触摸屏幕及按钮
8)  手机时间设置正确
9)  有SD卡并是清空

###五、  测试命令的执行
1. 进入CTS测试环境
开启终端输入命令:  startcts     //  注意权限问题
如果权限不够的话输入：sudo ./startcts
输入用户密码
当出现
Android CTS version 2.2_r1 
Device(s5830f1188529) connected 
cts_host > cts_host > 
就代表已经进入CTS测试环境
 
 
2. CTS测试的方法
 
Ø  查看测试计划
Ls  --plan
 
Ø  查看测试包
Ls  -p
 
Ø  运行测试计划
start ‐‐plan  [test_plan_name] 
举例:运行VM测试计划
输入命令:--plan VM
 
Ø  运行测试计划下的测试包
start ‐‐plan  [ test_plan_name ]  ‐p/‐‐package [plan_package_name] 
举例：运行Java计划下的android.core.tests.archive测试包
 
 
Ø  运行测试计划下测试包中的某个测试项
Start –plan [test plan name]–t
[plan_package_name].[class_name]#[plan_package_test_name]
举例:运行Java计划下android.core.tests.archive包下的test_tostring测试项
 
 
 
Ø  指定某个设备上运行摸个测试计划中的测试包
start --plan [ test_plan_name ]‐d/--device [device_ID] 
举例:指定设备ID号为1234567890ABCDEF运行VM测试计划
 
 
 
 
Ø  查看测试结果
 Ls –r
举例:
 
 
 
 
 
Ø  查看某个session的测试记录
ls -r/--result -s/--session session_id
举例: ls –r –s  3
 
3. CTS详细命令
  Host:
 help: 查看帮助信息
    exit: 退出
  Plan:
    ls --plan: 列出所有的测试包
    ls --plan plan_name: 查看测试方案
    add --plan plan_name: 新建一个测试方案
    add --derivedplan plan_name -s/--session session_id -r/--result result_type:
 derive a plan from the given session
    rm --plan plan_name/all: remove a plan or all plans from repository
    start --plan test_plan_name: 运行一个测试方案
    start --plan test_plan_name -d/--device device_ID: run a test plan using the
 specified device
    start --plan test_plan_name -t/--test test_name: run a specific test
    start --plan test_plan_name -p/--package java_package_name: 运行一个特定的java测试包
    start --plan test_plan_name -t/--test test_name -d/--device device_ID: 用指定的device运行一个特定的测试方案
    start --plan test_plan_name -p/--package java_package_name -d/--device devic
e_ID: run a specific java package using the specified device
  Package:
    ls -p/--package: list available packages
    ls -p/--package package_name: list contents of the package with specified na
me
    add -p/--package root: add packages from root to repository
    rm -p/--package package_name/all: remove a package or all packages from repo
sitory
  Result:
    ls -r/--result: list all result of sessions
    ls -r/--result -s/--session session_id: list detail case result of a specifi
ed session
    ls -r/--result [pass/fail/notExecuted/timeout] -s/--session session_id: list
 detail cases of a specified session by the specified result.
  History:
    history/h: list all commands in command history
    history/h count: list the latest count records in command history
    history/h -e num: run the command designated by 'num' in command history
  Device:
ls -d/--device: list available devices
 
4.  Android-CTS用户指南
 
网址: http://source.android.com/compatibility/downloads.html
 
 
 
 
###六、  测试结果分析
Android-CTS运行后会产生一测试报告,测试报告以开始的日期+时间命名,如2011.12.21_12.09.44, 一般只要查看testresult.xml文件就可对测试结果进行分析。由于CTS测试所需要花的时间比较长,当对测试进行调试时可针对的进行测试可节约大量时间。以下将介绍几种常用的方法：
Ø  查看整体测试概览
Ø  查看具体测试包测试例执行情况
 
Ø  测试fail项查看失败细节
 
 
Ø  测试调试
首先确定是哪个测试包出现失败,然后针对该测试包进行重新测试
$ adb install  …/andandroid‐cts/package.apk
$ adb shell pm list instrumentation   pm 用于管理 package，看当前机器安装了什么用例
$ adb shell am instrument ‐w android.tests.sigtest/.InstrumentationRunner              
Ø  定位某个测试失败项进行单独运行
Start –plan [test plan name]–t
[plan_package_name].[class_name]#[plan_package_test_name]
注意寻找class_name,可从测试报告中找到
