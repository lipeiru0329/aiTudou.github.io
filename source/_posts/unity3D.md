---
title: unity3D
date: 2021-01-01 22:12:10
tags:
---

## Unity

1. RigidBody:有重力 https://blog.csdn.net/yangyong0717/article/details/72529250
    - Mass 质量，单位为Kg，建议不要让对象之间的质量差达到100倍以上

    - Drag 空气阻力，为0表示没有阻力，infinity表示立即停止移动

    - Angular Drag 扭力的阻力，数值意义同上

    - use Gravity 是否受重力影响

    - Is Kinematic 是否为Kinematic刚体，如果启用该参数，则对象不会被物理所控制，只能通过直接设置位置、旋转和缩放来操作它，一般用来实现移动平台，或者带有HingeJoint的动画刚体

    - Interpolate 如果你的刚体运动时有抖动，尝试一下修改这个参数，None表示没有插值，Interpolate表示根据上一桢的位置来做平滑插值，Extrapolate表示根据预测的下一桢的位置来做平滑插值

    - Freeze Rotation 如果选中了该选项，那么刚体将不会因为外力或者扭力而发生旋转，你只能通过脚本的旋转函数来进行操作

    - Collision Detection 碰撞检测算法，用于防止刚体因快速移动而穿过其他对象

    - Constraints 刚体运动的约束，包括位置约束和旋转约束，勾选表示在该坐标上不允许进行此类操作

2.	Collidate： 有碰撞

3.	Mesh Filter: 出现还是不出现

4. SerializedField: 只在这个script里面的variable

    public: 所有的script公用的
