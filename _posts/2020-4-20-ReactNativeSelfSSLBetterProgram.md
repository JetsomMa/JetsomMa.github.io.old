---
layout: post
title: ReactNative框架下自签SSL证书请求被拦截的最佳解决方案
date: 2020-4-20
tags: ReactNative   
---

### 问题场景

​使用自签证书的服务端，在RN框架下客户端发送https请求会被拦截，从而导致服务端收不到请求。网上查找了很多解决办法，但在我看来都并非最佳方案，为此做以纪录！

>其实解决方法的代码，互联网上都是有的，本猿之所以写这篇文章，只是因为本猿恬不知耻的觉得本猿的解决方法更好一下

### 解决这个问题我认为分两部分

#### 1、解决问题的方法

#### 2、解决问题的更好的方法

### 解决问题的方法

我亲测可用的方案，代码如下所示：https://www.jianshu.com/p/ec4abc6a649a【特别鸣谢作者eyow的乐于分享】

***为了防止原作者文献的丢失，我将会在另一片文章中将关键代码摘要出来，这里就不做赘述了。***

首先，这样的代码和操作是可以解决问题的，IOS的修改可以做的一劳永逸，但是安卓的解决方式却并不友好。

在我们成功的在安卓打包工程中引入了ReactAndroid模块之后，因为RN模块是整个工程的根本，其他每个模块都有引用，如下图所示：

![image-20200505220454398](https://mashaojie.club/download/source/typora-user-images/image-20200505220454398.png)

***也就是说，我们要将每个RN插件的build.gradle配置中react-native的引用都要修改为：compile project(':ReactAndroid')；***

***甚至于我们重新npm install之后要改打包壳代码，我们新增一个RN插件之后也要对应的修改打包壳中的build.gradle的引用代码。***

这样并不友好！

### 解决问题的更好的方法

> 有时候，对事物的完美要求和对现状的不满足是促使我们成长和发展的一大要素

我们回到npm引用的资源的node_modules路径下react-native中看一眼，如下图所示：

![image-20200505221601343](https://mashaojie.club/download/source/typora-user-images/image-20200505221601343.png)

不难发现，原本react-native的引用实际上引用的是如图的这堆jar包和aar包，我们只要修改的jar包和aar包中对应的地方的代码，问题就解决了！

所以第一个想到的是反编译，修改后再打回对应的jar包和aar包。
***【这个想法太天真，当看到.md5和.sha1这些文件的时候，有足够开发经验的人都知道，这是防篡改的】***

***再理一理思路：***1、既然安卓的源码我们都有了，那么我们导出上面的jar包和aar包也就变得有可能了；2、这些包的文件结构很眼熟啊，在哪里见过呀-------这不就是maven么！【有时候在做SpringBoot项目的时候，会导致Maven构建项目失败，其中最常见的应该是引用ojdbc6的时候，因为这个jar是Maven仓库自己下载不下来的，需要我们手动下载，然后mvn install到我们的maven仓库中，细心的人会去翻看本地Maven仓库！】

> 此时的作者灵光一闪，凭借自己多年的临床经验，用自己的脚趾头想了一想，然后一拍大腿惊喊到——这事能弄！

我们玩过安卓Cordova壳开发的都知道，安卓项目是用gradle构建的，也可以通过写gradle脚本将一个安卓模块导出成为一个aar引用资源包或者一个jar，那么这个maven库导出的代码应该也在gradle配置文件里面！

> 当中有四个构建资源下载特别慢，及其耗时，而且容易出错，于是我在别人的百度云盘里找到了对应资源上传到了我的资源服务器，并改了资源下载的脚本，才确保了ReactAndroid模块能够正常高效的构建：【有需要解决这一问题的小伙伴可以微信联系我，我愿意共享代码和我文件服务器上的资源文件。微信/QQ：1215458034】如下图所示：

![image-20200505224445021](https://mashaojie.club/download/source/typora-user-images/image-20200505224445021.png)

#### 按照上面的思路，完成这件事儿基本上分为两步：1、修改gradle脚本；2、运行gradle脚本

##### 1、修改gradle脚本；

顺着上面的思路耐心找一找，果真还给找到了相关配置，如下图所示：

![image-20200505225352386](https://mashaojie.club/download/source/typora-user-images/image-20200505225352386.png)

再上图中我们看到了两个很重要的配置路径：1、aar本地资源输出的路径；2、Maven仓库的路径。

***所以做这件事儿的前提是你得有一个Maven仓库！***

资源输出的路径，根据自己的需要去修改，Maven仓库的路径改成自己本地maven仓库的地址！

以下为我的配置修改结果，如图所示：

![image-20200505230625987](https://mashaojie.club/download/source/typora-user-images/image-20200505230625987.png)

##### 2、运行gradle脚本

运行之前，我们要先找到对应关键的gradle的task！

经过我耐心的研读寻找，也确实找到了，如下图所示：

![image-20200505232229601](https://mashaojie.club/download/source/typora-user-images/image-20200505232229601.png)

***我们需要先运行assemble，让基础模块进行组装编译，然后再运行uploadArchives生成Maven库资源，然后再运行installArchives【在other目录里面】导出Maven资源***

运行结束后，我们会在对应的位置生成对应的aar包和jar包，如下图所示，此时我们只需要将aar和jar包对应的覆盖到我们的node_modules/react-native目录下即可！

![image-20200505231800723](https://mashaojie.club/download/source/typora-user-images/image-20200505231800723.png)

***总结：工作是一种态度，也是一种爱好。虽然现在市面上都在流行前后端分离，讲求技术的专业性，然而跨界并不是什么坏事，有时能更好的解决问题，产生意想不到的效果。所以我们要足够努力，且有必要的自我肯定！***