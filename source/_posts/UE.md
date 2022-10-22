---
title: UE
date: 2022-10-20 14:09:27
tags:
---

## [UE](https://zhuanlan.zhihu.com/p/22814098)

开始 UE 啦，UE 作为功能最强大的 Game Engine，广泛在 游戏，特效里面，好了废话不多说，开始学习 UE了。

学习：

1. https://zhuanlan.zhihu.com/p/22813908

### 项目结构

VS项目和文件目录：
![](https://pic2.zhimg.com/80/v2-98f28e37af2dc26f5af34d7723323e7d_1440w.webp)

可以看到，Config目录里带着3个最主要的配置，Editor,Engine,Game。代码方面自动生成了用于编译系统的3个.cs文件，C++代码方面生成了一个Hello "Game Module"，和HelloGameMode。

> 文件目录：

- Binaries:存放编译生成的结果二进制文件。该目录可以gitignore,反正每次都会生成。
- Config:配置文件。
- Content:平常最常用到，所有的资源和蓝图等都放在该目录里。
- DerivedDataCache：“DDC”，存储着引擎针对平台特化后的资源版本。比如同一个图片，针对不同的平台有不同的适合格式，这个时候就可以在不动原始的uasset的基础上，比较轻易的再生成不同格式资源版本。gitignore。
- Intermediate：中间文件（gitignore），存放着一些临时生成的文件。有：
    - Build的中间文件，.obj和预编译头等
    - UHT预处理生成的.generated.h/.cpp文件
    - VS.vcxproj项目文件，可通过.uproject文件生成编译生成的Shader文件。
    - AssetRegistryCache：Asset Registry系统的缓存文件，Asset Registry可以简单理解为一个索引了所有uasset资源头信息的注册表。CachedAssetRegistry.bin文件也是如此。
- Saved：存储自动保存文件，其他配置文件，日志文件，引擎崩溃日志，硬件信息，烘培信息数据等。gitignore
- Source：代码文件。


### 编译类型

很多人在使用UE4的时候，往往只是依照默认的DevelopmentEditor，但实际上编译选项是非常重要的。
UE4本身包含网络模式和编辑器，这意味着你的工程在部署的时候将包含Server和Client，而在开发的时候，也将有Editor和Stand-alone之分；同时你也可以单独选择是否为Engine和Game生成调试信息，接着你还可以选择是否在游戏里内嵌控制台等。

VS

![](https://pic3.zhimg.com/80/v2-c7b3cfbb8cbbb6387a42908c08cfe89a_1440w.webp)

依照官方介绍

>每种编译配置包含两种关键字。第一种表明了引擎以及游戏项目的状态。第二个关键字表明正在编译的目标。

![](https://pic3.zhimg.com/80/v2-c9660d193fdc18d204ba0d91ee3150be_1440w.webp)

![](https://pic1.zhimg.com/80/v2-f049c9630ed11b9e7e2e69e502901dc8_1440w.webp)



组合的各种情况：

![](https://pic4.zhimg.com/80/v2-a7d465573bb2a07fb9a1bdfe8ed08393_1440w.webp)

所以为了我们的调试代码方便，我们选择DebugEditor来加载游戏项目，当需要最简化流程的时候用Debug来运行独立版本。

### 命名约定

客观来说，相比其他引擎的源码，UE4的源码还是非常清晰的，模块组织也比较明了。但阅读源码的学习曲线依然陡峭，我想有以下原因：
1. UE4包含的模块众多，拢共有几十个模块，虽然采用了Module架构来解耦，但难免还是要有依赖交叉的地方，在阅读的时候就很难理清各部分的关系。
2. UE4的功能优秀，作为业界顶尖的成熟游戏引擎，在一些具体的模块内部实现上就脱离了简单粗暴，而是采用了各种设计模式和权衡。同时也需要阅读的人有相关的业务知识。比如材质编辑器编译生成Shader的过程就需要读者拥有至少差不多的图形学知识。
3. 被魔改后的C++，UE4为了各平台的编译和其他考量（具体以后说到编译系统的时候再细讨论），对标准的C++和编译，进行了相当程度的改造，在UHT代码生成和各种宏的嵌套之后，读者就很难一下子看清背后的各种的机制了。

但万丈高楼平地起，咱们也可以从最简单的一步步开始学起，直到了解掌握整个引擎的内部结构。
在阅读代码之前，就必须去了解一下UE4的命名约定，具体的自己去查看官网文档，下面是一些基本需要知道的：

- 模版类以T作为前缀，比如TArray,TMap,TSet 
- UObject派生类都以U前缀
- AActor派生类都以A前缀
- SWidget派生类都以S前缀
- 抽象接口以I前缀
- 枚举以E开头
- bool变量以b前缀，如bPendingDestruction
- 其他的大部分以F开头，如FString,FName
- typedef的以原型名前缀为准，如typedef TArray FArrayOfMyTypes;
- 在编辑器里和C#里，类型名是去掉前缀过的
- UHT在工作的时候需要你提供正确的前缀，所以虽然说是约定，但你也得必须遵守。（编译系统怎么用到那些前缀，后续再讨论）


### 基础概念

和其他的3D引擎一样，UE4也有其特有的描述游戏世界的概念。

在UE4中，几乎所有的对象都继承于UObject（跟Java,C#一样），UObject为它们提供了基础的垃圾回收，反射，元数据，序列化等，相应的，就有各种"UClass"的派生们定义了属性和行为的数据。

跟Unity（GameObject-Component）有些像的是，UE4也采用了组件式的架构，但细品起来却又有些不一样。在UE中，3D世界是由Actors构建起来的，而Actor又拥有各种Component，之后又有各种Controller可以控制Actor（Pawn）的行为。Unity中的Prefab，在UE4中变成了BlueprintClass，其实Class的概念确实更加贴近C++的底层一些。

Unity中，你可以为一个GameObject添加一个ScriptComponent，然后继承MonoBehaviour来编写游戏逻辑。在UE4中，你也可以为一个Actor添加一个蓝图或者C++ Component,然后实现它来直接组织逻辑。 UE4也支持各种插件。

