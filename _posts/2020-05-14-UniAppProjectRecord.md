---
layout: post
title: 小程序项目实施关键技术总结
date: 2020-05-14 
tags: UniApp   
---

> 最近接到公司一个小程序的紧急项目，包含有管理端、服务端和小程序客户端。
>
> 没有基版，没有服务器，没有公众号和小程序账号，一切从0开始，要求20天上线，让人很头疼！
>
> 本着做优质产品的原则，这次我将会细细打磨小程序这块的产品。
>
> 虽然加班很晚才回来，但还是想学习罗志祥大师的时间管理，能够充分的利用时间，高效的工作。
> 现就对小程序端的一些重点做细致总结。
>
> ***如果您有更好的方案，欢迎联系探讨，微信/QQ：1215458034***
>
> 小程体验二维码:
>
> <img src="https://mashaojie.cn/blog/sxnx-mp/gh_a77f938e4585_1280.jpg" alt="gh_a77f938e4585_1280 (1)" style="zoom:30%;" />
>
>
> H5程序体验鉴赏，请来这里 https://mashaojie.cn/sxxh 
>
> 【手机上打开食用才有味道】
>
> 网站更新时间：2020-06-12 00:27

***小程序端的技术选型是uni-app，因为其在端平台编译运行方面有极大优势，且框架比较成熟和稳定***

### 1、解决uni-app中swiper高度自适应问题

> 【更新：2020-06-06】对这块代码进行重构，解决小程序兼容问题

**问题：** 在uni-app中swiper的高度默认为150px【其实在很多Vue的UI框架中默认都是150px】，很多网上的资料都是让设置高度100%，然而这并不能解决问题。

**解决方案**：更改swiper-item的 ***height:auto;*** 【原本是100%，适应父元素高度】，然后让swiper-item的内部子元素撑开swiper-item的高度，在元素渲染结束后获取swiper-item的高度，将高度赋值给swiper元素！

***特别注意：此时的swiper-item并没有heigh这个样式属性，而是取其srcollHeight这个样式属性***

***核心代码：***

html代码：

```html
<swiper :style="{height: swiperHeight + 'px'}" ref="newSwiper" id="newSwiper" @change="ontabchange">
	<swiper-item style="height:auto;" v-for="tab in newsList" :key="tab.id">
    <!-- 内部子元素 -->
	</swiper-item>
</swiper>
```

js代码：

```js
ontabchange(e) {
  let index = e.target.current || e.detail.current;
  //当前显示的swiper-item
  this.newsTabIndex = index;
},
resetSwiperHeight(){
  if(this.newsList.length > 0){
    this.$nextTick(() => {
      // #ifdef MP-WEIXIN
      let queryNode = wx.createSelectorQuery().in(this).selectAll(".swiper-item.news-swiper-item"); 
      queryNode.boundingClientRect((rect)=> {
        this.swiperHeight = rect[this.newsTabIndex].height;
      }).exec();
      // #endif
      // #ifdef  H5
      this.swiperHeight = this.$refs.newSwiper.$children[this.newsTabIndex].$el.scrollHeight;
      // #endif
    });
  }
}
```



### 2、不可描述的主屏吸顶/定位到页面指定位置的滑动功能

> 因为对支付宝的首页交互体验特别有好感，所以想极力做到人家大厂的交互美感
>
> ***有品质的产品都是很注重交互的***
>
> 【更新：2020-06-06】对这块代码进行重构，主要解决以下问题：
>
> 	1、滑动动画卡顿性能问题；
>
> 2、小程序上代码的兼容问题；
>
> 	3、小程序上代码性能问题和调优；
> 	
> 	----小程序代码性能问题主要是因为微信小程序本身存在的一点设计上的不合理，小程序为了有更好的性能采用的是双线程架构模式，双线程分别是逻辑线程和虚拟DOM渲染线程，两个线程间通过JSBridge进行交互通讯，而JSBridge性能并不高，如果逻辑线程和渲染线程存在高频率的交互，JSBridge将会成为性能瓶颈，页面将会出现严重的卡顿甚至卡死。而这块代码中onPageScroll会监听页面滚动频繁的回调，从而触发渲染线程重新渲染页面，在H5下没有问题，但在微信小程序中操作体验极差。解决办法不光要加防抖，还要加渲染锁定，使渲染过程中忽略收到的onPageScroll监听。	

***解决方案：*** 其实有两种实现方案，在uni-app中有onPageScroll生命周期函数和uni.createIntersectionObserver这个Api方法，最终还是选择了onPageScroll，原因是createIntersectionObserver这个接口讲述的并不是很清晰，不太好理解，虽然尝试了一下能用，但是细节的可控性方面，还是onPageScroll更为实用。

***特别注意：*** 1、onPageScroll这个生命周期函数触发很平凡，所以要做防抖处理，否则页面交互不可控；2、为了更好的性能，加上了渲染锁定。

***核心代码：***

**main.vue**

html代码：

```html
<!-- 自定义导航头 -->
<nav-bar  ref="navBar" id="navBar" :backState="2000" :bgColor="['#0CA054','#29AD66']" fontColor="#FFF" :title="headerTitle"></nav-bar>
<!-- 首页 -->
<view id="homePage">
  <!-- 标题头组件 -->
  <home-title></home-title>
  <!-- 快捷菜单 -->
  <quick-module></quick-module>
  <!-- 新闻菜单 -->
  <news-tab ref="newsTab" id="newsTab" :newsTabBars="newsTabBars" :newsTabIndex="newsTabIndex" @onChange="onChange"></news-tab>
  <!-- 新闻列表 -->
  <news-swiper :newsList="newsList" :newsTabIndex="newsTabIndex" :scrollHeight="scrollHeight" @onChange="onChange" @goDetail="goDetail" @loadMore="loadMore"></news-swiper>
  <!-- 悬浮新闻菜单 -->
  <news-tab :newsTabBars="newsTabBars" :style="newsTabStyle" :newsTabIndex="newsTabIndex" @onChange="onChange"></news-tab>
</view>
```

js代码：

```js
data() {
  return {
    headerTitle: "",
    newsTabBarTop: 0,//吸顶状态下的悬浮新闻菜单栏应该距离页面顶部的高度
    newsTabStyle: "display:none;",//悬浮菜单栏的样式字符串
    newsList: [],//新闻内容
    newsTabIndex: 0,//当前选中的新闻菜单
    scrollHeight: 0,//新闻列表的高度，通过计算得到
    transformTime: 150,//吸顶和定位到新闻的转场动画时长
    isTop: true,//定位吸顶标志
    isLock: false,//scorll锁定
    scrollDiff: 40,//过拉参数【单位px】
    newsPosition: 0//新闻菜单定位参数【单位px】
  };
},
onReady(){
  //获取手机参数
  this.modile = uni.getSystemInfoSync();
  // #ifdef MP-WEIXIN
  //获取自定义导航栏的定位信息
  let queryNode = wx.createSelectorQuery().in(this);
  let queryNode1 = queryNode.select("#navBar"); 
  queryNode1.boundingClientRect((rect)=> {
    //获取导航栏的高度数据
    this.newsTabBarTop = rect.height;
  }).exec();

  //获取页面中新闻菜单的定位信息
  let queryNode2 = queryNode.select("#newsTab"); 
  queryNode2.boundingClientRect((rect)=> {
    //定位到新闻菜单悬浮时新闻列表的位置 = 页面内新闻菜单到页面顶部的距离 - 自定义导航栏的高度数据
    this.newsPosition = rect.top - this.newsTabBarTop;
    //计算new-swiper新闻列表所要占据的高度
    this.scrollHeight = this.modile.windowHeight - 20 - this.newsTabBarTop - rect.height - (this.is_bang?70:60);
  }).exec();
  // #endif

  //#ifdef H5
  //获取导航栏的高度数据
  this.newsTabBarTop = this.$refs.navBar.$el.clientHeight;
  //定位到新闻菜单悬浮时新闻列表的位置 = 页面内新闻菜单到页面顶部的距离 - 自定义导航栏的高度数据
  this.newsPosition = this.$refs.newsTab.$el.offsetTop - this.newsTabBarTop;

  //计算new-swiper所要占据的高度
  this.scrollHeight = this.modile.windowHeight - 20 - this.newsTabBarTop - this.$refs.newsTab.$el.clientHeight - (this.is_bang?70:60);
  //#endif 
},
onPageScroll(el){
  if(el.scrollTop < this.scrollDiff){//如果当前位置小于过拉参数，则置顶返回
    this.isTop = true;
  }else if(el.scrollTop < this.newsPosition - 15){//如果当前位置小于新闻元素位置一定范围，则直接隐藏悬浮的新闻菜单
    this.tabNo();
  }else{//显示悬浮新闻菜单
    this.tabFixed();
    this.isTop = false;
  }
  if(this.isLock || this.tabIndex != 0) return;//如果当前处于渲染锁定状态，则直接返回
  if(timer){//防抖
    clearTimeout(timer);//清除计时器
  }
  //重新计时
  timer = setTimeout(() => {
    if(el.scrollTop < this.newsPosition && el.scrollTop > this.newsPosition-this.scrollDiff){
      //如果拉动距离小于scrollDiff这个差值，且在新闻菜单附近，则定位回到新闻菜单
      console.log("小范围-定位新闻菜单");
      this.goNews();
    }else if(el.scrollTop > 0 && el.scrollTop < this.scrollDiff){ //如果拉动距离小于scrollDiff这个差值，则定位吸顶
      console.log(el.scrollTop);
      console.log("小范围-吸顶");
      this.goTop();
    }else if(el.scrollTop >= this.scrollDiff && el.scrollTop <= this.newsPosition-this.scrollDiff) {
      //中间情况的处理--【如果是吸顶状态，则定位到新闻菜单】，【如果在新闻菜单，则定位到吸顶】
      if(this.isTop){
        console.log("公共范围-定位新闻菜单");
        this.goNews();
      }else{
        console.log("公共范围-吸顶");
        this.goTop();
      }
    }
  }, 50);
},
methods: {
  //新闻菜单不悬浮
  tabNo(){
    this.newsTabStyle = "display:none;";
  },
  //新闻菜单悬浮
  tabFixed(){
    this.newsTabStyle = "width:100%;position:fixed;background-color:#fff;z-index:20;box-shadow: 0rpx 0rpx 10rpx #cdcdcd;top:"+this.newsTabBarTop+"px;";
  },
  //页面置顶
  goTop(){
    this.isTop = true;
    uni.pageScrollTo({
      scrollTop: 0,
      duration: this.transformTime,
      selector: "#homePage"
    });
    this.lockScroll();
  },
  //页面定位到新闻列表
  goNews(){
    this.isTop = false;
    uni.pageScrollTo({
      scrollTop: this.newsPosition,
      duration: this.transformTime,
      selector: "#homePage"
    });
    this.lockScroll();
  },
  //锁定渲染锁，在页面渲染完成后20毫秒开锁---小程序的页面每秒渲染60次，每次渲染耗时17ms---16.6666...ms
  lockScroll(){
    this.isLock = true;
    setTimeout(() => {
      this.isLock = false;
    }, this.transformTime + 20);
  }
}
```

### 3、html解析插件

> 推荐链接：https://github.com/jin-yufeng/Parser

这是一款很强大的html解析插件，兼容各个平台的各个版本都有，值得阅览学习。

### 4、服务端限流【希望有经验的大神给一些更好的建议...】

因为最初项目实施的时候，客户对于公告这块的需求只是文本，到后来测试阶段又要求实现图片，再后来发现又有视频的需求【没有明确需求范围的项目就是个无底洞...】，其实图片也还好，但是视频就...不太好了。为了节约成本，根据需求我买的云服务器只有1M带宽，下载速度差不多100k/s【毕竟是不可能超越125k/s，而且还有TPC层网络丢包重传和链路损耗】。

如果视频清晰度不高也还好，但是当我上传了一个30M的mv做测试的时候，卡顿就已然出现了，这还只是单用户访问，多用户同时看视频，服务端会过载，报503，这时候管理端就上不去了。

***正常的请求，搞得像DoS攻击一样。。。***

在短期内无法更换服务器的情况下，只能是损失性能，确保服务的稳定，于是我在文件服务器配置上加了限流，限制最高下载速度50k/s【这是一个测试经验值，因为我发现设置为70k/s时，网络环境稍差一点，管理端登录偶尔还是会有503错误】

***最终，系统整体是稳定了，但是视频的浏览这块客户体验极差。。。***


