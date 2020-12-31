---
layout: post
title: 兼容Vue2和Vue3的长按指令插件
date: 2020-12-31
tags: MarkDown    
---

>  vue本身并未提供长按操作，此插件用于支持vue框架下的长按
>  此插件做了vue2和vue3版本的兼容，便于用户使用

### vue代码中应用[Home.vue]

```js
<template>
  <div v-longpress="longpressFunc">长按我试试</div>
</template>

export default {
  methods: {
    longpressFunc () { 
      alert('长按被触发!') 
    }
  }
}
```

### Vue2中引用[main.js]

```js
import Vue from 'vue'
import LongPress from '@/common/plugins/long-press';
Vue.use(LongPress)
```

### Vue3中引用[main.js]

```js
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App)

import LongPress from '@/common/plugins/long-press';
app.use(LongPress)
```

### 插件代码[long-press.js]

```js
//全局注册长按指令插件
export default {
  install: (app) => {
    let optionData = getOptionData(app)
    if (optionData.option) {
      app.directive('longpress', optionData.option)
    }else{
      console.warn("您的vue版本["+optionData.version+"]尚不能支持[longpress]指令插件！")
    }
  }
}

//获取指令注册属性参数
function getOptionData(app) {
  //获取vue版本号
  var version = app.version
  if (Number(version.split('.')[0]) === 3) {
    //输出vue3的指令注册option
    return {version, option:{
      beforeMount(el, binding) {
        bindFunction(el, binding)
      },
      unmounted(el) {
        unBindFunction(el)
      }
    }}
  } else if (Number(version.split('.')[0]) === 2) {
    //输出vue2的指令注册option
    return {version, option:{
      bind: bindFunction,
      unbind: unBindFunction
    }}
  }
  return {version, option: null}
}

//注册指令执行函数
function bindFunction(el, binding) {
  // 确保指令入参是个函数
  if (typeof binding.value !== 'function') {
    // 如果指令入参不是函数则输出警告
    console.warn(`[longpress:] provided expression is not a function, but has to be`)
  }

  // 定时器指针
  let pressTimer = null

  // 操作计时函数
  let start = (e) => {
    if (e.type === 'click' && e.button !== 0) {
      return;
    }

    if (pressTimer === null) {
      pressTimer = setTimeout(() => {
        //长按1秒之后，执行函数
        binding.value()
      }, 1000)
    }
  }

  // 销毁计时器
  let cancel = () => {
    if (pressTimer !== null) {
      clearTimeout(pressTimer)
      pressTimer = null
    }
  }

  //定义监听销毁的函数
  el.unbindEventListener = () => {
    //销毁所有的监听
    el.removeEventListener("mousedown", start);
    el.removeEventListener("touchstart", start);
    el.removeEventListener("click", cancel);
    el.removeEventListener("mouseout", cancel);
    el.removeEventListener("touchend", cancel);
    el.removeEventListener("touchcancel", cancel);
  }

  // 监听到这些事件时候，产生定时器
  el.addEventListener("mousedown", start);
  el.addEventListener("touchstart", start);
  // 监听到这些事件时候，移除定时器
  el.addEventListener("click", cancel);
  el.addEventListener("mouseout", cancel);
  el.addEventListener("touchend", cancel);
  el.addEventListener("touchcancel", cancel);
}

//注销指令执行函数
function unBindFunction(el) {
  if (el.unbindEventListener) {
    el.unbindEventListener()
  }
}
```
