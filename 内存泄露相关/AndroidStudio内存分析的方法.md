# Android Studion 快速定位内存泄露的方法
---

内存泄露是指一些生命周期结束的对象，由于一些原因还存在内存中，并且不能被GC回收，导致内存不断的增长，最终导致程序卡顿甚至内存溢出（俗称的OOM）。

本文根据Google的官方文档，结合笔者的实际使用情况，介绍了如何使用 Android Studio(简称AS)自带的工具对内存进行监控、分析，其中使用到的分析案例来自GitHub:

> [基于Android中内存泄露的相关调研及解决方案:MemoryLeakSample ](https://github.com/liaomingwei/MemoryLeakSample)

## 工具介绍
Memory Monitor 是AS中自带的一种内存监视器，提供了内存监控和内存分析功能，能够让开发者非常方便的检查App的RAM的使用情况。

上图中，区域D是内存的监控界面，其中各个按钮从左至右的功能如下：

- Enable:打开/暂停内存监控
- Initiate GC：点击后执行一次GC操作。
- Dump Java Heap：Java内存堆镜像。点击生成一个hprof文件显示当前的Java内存堆情况
- Start Application Tracking：跟踪内存使用情况。点击第一次开始跟踪，点击第二次结束跟踪并生成alloc文件。（在本文中不做介绍）
- Memory monitor help：跳转到官方帮助文档

## 参数说明
### Memory Monitor参数

参数名|含义
-|-
Free|当前进程，剩余的可用内存
Allocated|当前进程，已经使用的内存

两者相加，就是此时App分配给当前进程的可用内存总量，这一总量会随着内存使用的情况发生变化，当总量超过App允许的最大内存使用量时，就会出现OOM。

### hprof文件参数

hprof 文件是某个时间点的内存分析文件，记录了当前时间点的内存分配、使用情况，从这个文件中，我们可以检查出大多数的内存泄露场景，并快速的定位到造成内存泄露的原因。下面是这个文件的一些参数的含义的介绍：

参数名|含义
-|-
Class Name|存储在内存中的对象名称
Total Count|在内存中存在的对象的个数
Heap Count|在堆内存中存在的对象的个数
Sizeof|每个对象占用的内存大小
Shallow Size|当前对象自身占用的内存大小
Retained Size|当前对象保留的内存大小
Instance|当前对象在内存中的实例
Reference Tree|当前实例的引用列表
Depth|GC Root 到当前实例的最小权重
Dominating Size|当前实例可支配的大小

关于这些参数解释，还有下面几点需要注意：

1. Total Count 和 Heap Count 不同的原因是因为Java的内存分为堆内存和栈内存，其中 Heap Count 是指在堆内存中的存储个数。堆内存是由Java的内存回收机制管理的，应用对于内存的分析和优化也是基于堆内存的，所以我们需要关注的也是 Heap Count 的值。

2. Retained Size 意思是保留的内存大小，也是指的当前对象如果被GC后堆上能释放的大小，和 Shallow Size 相比，它的大小包括了当前对象引用的一些对象的大小，这两种大小的具体解释可以看看这篇文章：[Shallow heap & Retained heap](http://bjyzxxds.iteye.com/blog/1532937)

3. 深度这个参数表示的是从 GC Root 到选定实例的最短的跳转（这里的跳转可理解成从一个类找到另一个类），在分析 Reference Tree 时可以根据这个值展开对应的项（比如说这个值是5，你就在 Reference Tree 中找4，然后找3，一直到0，既可以找到被 GC Root 持有的实例）

## 使用详解

### 使用 hprof 文件进行分析

在介绍参数时我提到过，可以通过分析 hprof 文件来快速的定位代码中简单的内存泄露，所谓的简单的内存泄露，就是由于 Activity 造成的内存泄露。

Activity 造成的内存泄露是 Android 开发中最常见，也是最容易产生的内存泄露，文章开头提到的案例中，有很多都是会造成 Activity 泄露例子，而 AS 为我们提供了一个工具，能够自动的帮我们找到 hprof 文件中 Activity 的泄露。

![]()

如上图所示，我们点击“Analyzer Tasks”，选中“Detect Leaked Activities”，然后点击“Perform Analysis”,便可以在 Analysis Results 下生成一个“Leaked Activities”的可展开项，展开后我们就可以看到是哪个 Activity 造成的内存泄露。点击 Leaked Activities 下的子项后，我们可以在 Reference Tree 下面可能会看到如下内容：

![]()

这种情况我们可以看到，所泄露的 Activity 被用不同的颜色展示出来了，我们依次展开其子项就可以找到造成内存泄露的地方，而依据就是 Depth 这个值，当 Depth 为0时，就是造成内存泄露的罪魁祸首了。

Depth 这个值我们上面有过介绍，是 GC Root 到当前实例的最小跳转次数（可以抽象的理解成 GC Root 到达实例的最小权重），当 Depth 为0时表示对应的实例被 GC Root 直接持有，反映在内存泄露的前提下，就是造成内存泄露的根源。

### 转换文件到MAT分析

上面我们提到过，AS 提供的工具能帮我们快速的定位到与 Activity 相关的内存泄露，有时我们需要更加详细的分析应用的内存情况，还是需要使用到 MAT 的工具。

MAT 需要的也是 hprof 文件，但是不能直接 AS 生成的 hprof 文件，所以我们需要将hprof文件转换为MAT可识别的文件，AS 也为我们提供了这个方法。

![]()

我们可以在 AS 的侧边栏上看到有一个“Captures”的页卡，打开后，可以看到所有我们生成的 hprof 文件，选择一个文件后，右键选择“Export to standard .hprof”,然后选择输出位置和文件名就可以了。

>**注意：**有些人的 AS 侧边栏上没有这个页卡，可以在"View - Tool Windows - Captures"中打开

## 优缺点分析

- 优点：
 - Google的亲儿子，不用担心维护问题。
 - 嵌入在 AS 中，使用方便。
 - 可以自动分析 Activity 相关的内存泄露
 - 定位迅速，使用简单
<br>
- 缺点：
 - 功能单一，通过列表展示数据不直观
 - 无法很好地定位非内存泄露造成的大量内存占用的问题（比如ListView中不使用ViewHolder）
 - 目前还有很多Bug（比如点击生成文件卡死等）

所以，笔者认为，AS 提供的工具，适合我们在开发过程中监视、分析我们的内存使用情况，当检测到内存使用出现异常时，可以先尝试解决 AS 定位到的问题，如果内存的使用情况得不到改善，再考虑使用 MAT 工具来进行内存分析。


## 参考文献

[Google官方文档](https://developer.android.com/studio/profile/investigate-ram.html)
