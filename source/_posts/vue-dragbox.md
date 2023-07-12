---
title: vue-dragbox 叠层拖动框
date: 2023-07-05 16:27:23
tags: 
- Vue.js
- 前端
---





# vue dragbox - 拖动框



## 前言

在工作中，遇到这样一个需求： 甲方老板需要系统内的所有弹窗做成类似windows桌面那样， 可以拖动且按层级展示。

那本文就用vue来实现一个通用的拖动框组件。



## 思想

要实现这个功能，可以分为以下几步：

1. 拖动

   改变元素的x , y定位， 可以联想到使用css中的 `transform : translate()` ； 

   拖动可以拆解为3个动作：

   1. 鼠标按下 `mousedown`
   2. 鼠标拖动 `mousemove`
   3. 鼠标弹起 `mouseup`

2. 层级

   点击某个弹窗，将该弹窗的zIndex设置为比当前zIndex更大即可； 如果是用的element-ui， 可以使用 `PopupManager.js` 实现层级管理

3. 组件封装

   使用插槽的方式，将弹窗内容包含在该组件里面



## 代码

### 实现

```vue
<template>
  <div
    class="draggable-box"
    :class="draggable?'cursorM':''"
    :style="{
      width: `${width}px`,
      height: `${height}px`,
      backgroundColor: background,
      transform: `translate(${x}px, ${y}px)`,
      zIndex: zIndex,
    }"
    @mousedown.prevent="dragStart"
    @mousemove.prevent="drag"
    @mouseup.prevent="dragEnd"
  >
    <slot></slot>
  </div>
</template>

<script>
  import { PopupManager } from 'element-ui/lib/utils/popup';

  export default {
    name: 'DraggableBox',
    props: {
      x: {
        type: Number,
        default: 0,
      },
      y: {
        type: Number,
        default: 0,
      },
      width: {
        type: Number,
        default: 5120,
      },
      height: {
        type: Number,
        default: 0,
      },
      background: {
        type: String,
        default: 'transparent',
      },
      zIndex: {
        type: Number,
        // 默认为当前层级+100
        default: PopupManager.nextZIndex() + 100,
      },
      draggable: {
        type: Boolean,
        default: true
      }
    },
    data() {
      return {
        isDragging: false,
        startPosition: { x: 0, y: 0 },
      }
    },
    methods: {
      // 开始拖动
      dragStart(event) {
        if(this.draggable){
          this.isDragging = true
          this.startPosition.x = event.clientX - this.x
          this.startPosition.y = event.clientY - this.y
          // 开始拖动时，将当前zIndex设置为最大，实现弹窗层级
          this.zIndex = PopupManager.nextZIndex();
        }
      },
      // 拖动中改变 x,y
      drag(event) {
        if(this.draggable) {
          if (!this.isDragging) return
          this.x = event.clientX - this.startPosition.x
          this.y = event.clientY - this.startPosition.y
        }
      },
      // 拖动结束
      dragEnd() {
        this.isDragging = false
      },
    },
  }
</script>

<style scoped>
  .cursorM{
    cursor: move;

  }
  .draggable-box {
    position: absolute;
  }
</style>

```



### 示例

```vue
<template>
	<DragBox>
        <!-- 在此处定义你的对话框内容 -->
    	<div class="dialog">
    		Life Oriented Programming
    	</div>
    </DragBox>
</template>

<script>
// 换成自己的项目路径
import DragBox from '@/components/Dragbox';
    
export default {
    name: 'mytest',
    components: {DragBox},
    data() {
        return {
            
        }
    }
}

</script>

<style scoped>
    .dialog {
        position: absolute;
        width: 1000px;
        height: 500px;
        border: 1px solid #FFF;
    }
</style>
```



---



以上就是使用vue实现拖动框组件的内容。



***Life Oriented Programming***

