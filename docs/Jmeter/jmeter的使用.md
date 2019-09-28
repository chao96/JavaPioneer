  新的一天，自己要更加强大，加油！
  
  <!-- /toc -->
  * [一、jmeter文件目录介绍](#一jmeter文件目录介绍)
      * [1.bin目录文件](#1bin目录文件)
      * [2.docs接口文档目录](#2docs接口文档目录)
      * [3.extras扩展插件目录](#3extras扩展插件目录)
      * [4.lib](#4lib)
      * [5.licenses](#5licenses)
      * [6.printable_docs](#6printable_docs)
   * [二、Jmeter功能概要](#二jmeter功能概要)
      * [1.Jmeter工具组成部分](#1jmeter工具组成部分)
      * [2.线程用户Threads (Users)](#2线程用户threads-users)
         * [1) setup thread group](#1-setup-thread-group)
         * [2）teardown thread group](#2teardown-thread-group)
         * [3）thread group](#3thread-group)
      * [3.测试片段（Test Fragment）](#3测试片段test-fragment)
      * [4.配置元件（Config Element）](#4配置元件config-element)
      * [5.定时器（Timer）](#5定时器timer)
      * [6.前置处理器（Per Processors）](#6前置处理器per-processors)
      * [7.后置处理器（Post Processors）](#7后置处理器post-processors)
      * [8.断言](#8断言)
      * [9.监听器](#9监听器)
      * [10.取样器](#10取样器)
      * [11.逻辑控制器](#11逻辑控制器)
   * [三、Jmeter脚本录制](#三jmeter脚本录制)
      * [1.代理服务器操作步骤](#1代理服务器操作步骤)
         * [1）创建一个线程组](#1创建一个线程组)
         * [2）创建一个http代理服务器](#2创建一个http代理服务器)
            * [①HTTP代理服务器设置--分组详解](#http代理服务器设置--分组详解)
         * [3）浏览器--internet属性--连接-局域网设置](#3浏览器--internet属性--连接-局域网设置)
      * [2.Badboy脚本录制](#2badboy脚本录制)
   * [四、Jmeter元件作用域和执行顺序](#四jmeter元件作用域和执行顺序)
      * [1.元件作用域](#1元件作用域)
      * [2.元件执行顺序](#2元件执行顺序)
<!-- /toc -->
      
# 一、jmeter文件目录介绍

![](F:\JavaPioneer\Jmeter\img\1文件目录介绍.png)

## 1.bin目录文件

+ jmeter.bat：windows的启动文件
+ jmeter.log：日志文件
+ jmeter.sh：linux的启动文件
+ jmeter.properties：系统配置文件
+ jmeter-server.bat：windows分布式测试要用到的服务器配置
+ jmeter-service：linux分布式测试要用到的服务器配置

## 2.docs接口文档目录

+ api/index.html：可查看官方文档

## 3.extras扩展插件目录

## 4.lib

所用到的插件目录，里面全是jar包，Jmeter会自动在JMETER_HOME/lib 和ext目录下寻找需要的类

## 5.licenses

jmeter证书目录

## 6.printable_docs

用户使用手册

+ usermanual/index.html

# 二、Jmeter功能概要

## 1.Jmeter工具组成部分

+ 资源生成器：用于生成测试过程中服务器、负载机的资源代码
+ 用户运行器：通常是一个脚本运行引擎，根据脚本要求模拟指定的用户行为
+ 报表生成器：根据测试中实时地数据生成报表，提供可视化的数据显示方式
+ 负载发生器：用于产生负载，通常以多线程或是多线程的方式模拟用户行为

**Test Plan测试计划**：用来描述一个性能测试，包含与本次性能测试所有相关的功能。

## 2.线程用户Threads (Users)

### 1) setup thread group

可用于执行预测试操作。测试前执行定期的线程组

### 2）teardown thread group

可用于执行测试后动作。测试结束后执行定期的线程组

### 3）thread group

我们通常添加运行的线程。可以看做一个虚拟用户组，线程组中的每个线程都可以理解为一个虚拟用户。

## 3.测试片段（Test Fragment）

与线程组不同，当它是一个模块控制器或者是被控制器所引用时才会被执行。

## 4.配置元件（Config Element）

提供对**静态数据**配置的支持。如CSV Data Set config可以将本地数据文件形成数据池（Data Pool）

## 5.定时器（Timer）

用于操作之间设置等待时间，等待时间是性能测试中常用的控制客户端QPS的手段。

## 6.前置处理器（Per Processors）

对实际请求进行前置处理

## 7.后置处理器（Post Processors）

对发出请求后得到的**服务器响应**进行处理。一般用来提取响应中的特定数据。

## 8.断言

检查测试中得到的数据是否符合预期

## 9.监听器

对**测试结果数据**进行处理和可视化展示的元件。查看结果树、聚合报告

## 10.取样器

性能测试中向服务器发送请求，记录响应信息，记录响应时间的最小单元

## 11.逻辑控制器

包含两类元件，一类是控制test plan中sampler节点发送请求的逻辑顺序的控制器，常用的有 if控制器、switch Controller、循环控制器等。另一类是事务控制器、吞吐控制器。

# 三、Jmeter脚本录制

## 1.代理服务器操作步骤

### 1）创建一个线程组

右键测试计划--添加--线程组）

### 2）创建一个http代理服务器

测试计划--添加--非测试元件--http代理服务器

#### ①HTTP代理服务器设置--分组详解

![](F:\JavaPioneer\Jmeter\img\2代理设置-分组详解.png)

+ 不对样本分组：所有请求全部罗列
+ 在组间添加分隔：加入一个虚拟的以分割线命名的动作
+ 每个组放入一个新的控制器：执行时按控制器输出结果
+ 只存储每个组的第一个样本：保存第一次url请求

设置完后要启动代理服务器，录制完成后记得关闭

### 3）浏览器--internet属性--连接-局域网设置

## 2.Badboy脚本录制

1）提示脚本错误解决

badboy—–Preferences——–Playing—–Javascript Error Dialogs

2）录制完成后导入export to JMeter

3）jmeter中打开jmx文件即可

# 四、Jmeter元件作用域和执行顺序

## 1.元件作用域

+ 取样器：不与其它元件相互作用，不存在作用域问题
+ 逻辑控制器：子节点中取样器和逻辑控制器
+ 其余元件，若是取样器子节点，则对其父子节点起作用。如果父节点不是取样器，则作用域是父节点下其它所有后代节点

## 2.元件执行顺序

1）配置元件

2）前置处理程序

3）定时器

4）取样器

5）后置处理程序

6）断言

7）监听器



### 



