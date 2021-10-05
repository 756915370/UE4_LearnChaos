# UE4_LearnChaos
这个工程基本上和[Unreal Engine 4 - Chaos Destruction Getting Started Guide](https://www.youtube.com/watch?v=kQLfGrLDHPc&list=PLncmXJdh4q89ZA99ffpzQNgSQyTJpbOOB&index=1)这个系列教程作者提供的工程一样，额外加上了Disable场。

它包含以下功能：
- **使用锚点场固定柱子**
- **使用相同的ClusterGroupIndex（簇组索引）让上方的两根柱子和下方的三根柱子共用场。这样即使上方的柱子没有指定锚点场也会收到锚点场的功能。**
- **当子弹射出去的时候会破坏柱子**
- **当柱子破碎后掉入地面时，用Disable场让它禁止模拟**

![在这里插入图片描述](https://img-blog.csdnimg.cn/9d54536daf9a4b278b3a79327f139186.gif#pic_center)  
使用版本为**4.26Chaso**，需要启动**FieldSystem插件**。  

电脑配置：
- CPU i7-8750H
- 内存 16G
- 显卡 GTX1060

破碎时5根柱子约1000块碎片，**帧率为15到20帧左右**。
***
### 学习资料：
- [【UE4官方文档】Chaos破坏系统概述](https://docs.unrealengine.com/4.27/zh-CN/InteractiveExperiences/Physics/ChaosPhysics/ChaosDestruction/ChaosDestructionOverview/)
- [Unreal Engine 4 - Chaos Destruction Getting Started Guide](https://www.youtube.com/watch?v=kQLfGrLDHPc&list=PLncmXJdh4q89ZA99ffpzQNgSQyTJpbOOB&index=1)
- [Unreal Engine 4 - Chaos Destruction Basics](https://www.youtube.com/watch?v=VWzCMGcC6eA&list=PLncmXJdh4q89ZA99ffpzQNgSQyTJpbOOB&index=2)
- [Unreal Engine 4 - Chaos Destruction Basics Pt. 2](https://www.youtube.com/watch?v=LRD1qoC5Q6A&list=PLncmXJdh4q89ZA99ffpzQNgSQyTJpbOOB&index=3)
- [【UE5官方文档】物理场参考指南](https://docs.unrealengine.com/5.0/zh-CN/PhysicsFeatures/PhysicsFields/PhysicsFieldsReferenceGuide/)
- [【虚幻5】UE5_十分钟学习Chaos破碎](https://www.bilibili.com/video/BV1dQ4y1X7Fq?spm_id_from=333.999.0.0)
- [【虚幻5】UE5_十分钟学会Chaos物理场](https://www.bilibili.com/video/BV1K44y167rG?spm_id_from=333.999.0.0)

关于UE5：
- UE5对Chaos进行了改进，但是有很多功能是相通的。UE5将物理场分为了**瞬态场、构造场、持久场**。在UE4中，
	- **瞬态场对应ApplyPhysicsField**
	- **构造场对应AddFieldCommand**（不需要勾选Enable也能启用）
	- **持久场对应在Tick中调用ApplyPhysicsField**

***
### 遇到的问题及注意事项记录
- 开启Chaos功能的问题，除了直接使用4.26Chaso版以外，还有修改源代码重新编译的方法，不过重新编译实在太慢了。编一次就要好几个小时，过程中报了以下错导致编译失败：
	- “a conflicting instance of unrealbuildtool is already running”
	- “Failed to initialize ShaderCodeLibrary required by the project because part of the Global shader library is missing from ../../Content/”
	最后放弃了修改源代码的方式来启动Chaos，**建议直接下载4.26Chaos版本。** 

- 使用Chaos的破碎功能帧率会掉的非常快，使用下述方法可以适当减少帧率，不过目前来看在手机上使用这个功能依旧很难。
	- **减少碎片数**
	- **使用Sleep\Disable\Kill场减少不必要的演算**
	- **使用Cache功能，先录制好再播放**。缺点是每次破碎出来的方向、位置都是固定的，且不能和玩家交互。另外4.26版本实测找不到Cache的功能，而4.23版本是有的。

- **Sleep、Disable、Kill场**之间的区别：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4ef07c515f4e46b79a2a8d2026a683da.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5rC05puc5pel6bih,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
- 关于子弹破坏柱子的功能，**只有ExternalClusterStrain场是必须的，另外两个LinearVelocity场和AngularTorque场并不是必须的**。推测可能是教程里为了让破坏效果更加真实加上的。
- 关于地面上的Disable场，只要碎片触碰到场的区域就会生效，不需要这个碎片最终落在这个场的范围内。
- 关于RadialVector、UniformVector等ActorComponent类：这些类都属于UFieldNodeBase的继承类，最后都会转化成对应的FFieldNodeBase类进行数据计算。因为只是在提供配置，所以这些类做成了ActorComponent。相关代码在FieldSystemObject、FieldSystem、FieldSystemNodes。个人精力有限就没有继续深入了。
