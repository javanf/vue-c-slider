## 前言
很多现有的vue slider组件都是单个滑块，一次业务需要，只能自己动手来一个了。双向两滑块限定区域，实现过滤功能了。

![VUE开发一个组件——Vue Slider 双向两滑块限定区域](http://cdn.javanx.cn/wp-content/themes/lensnews2.2/images/post/20181207173308.gif)

看起来，是不是还挺有趣的，限定时间区域，温度，数量等等，都是不错的组件。实现起来，也不难的。

## 页面部分
`ruler`是整个滑块区域，下面的`date`只是展示有的。并写了一个简单的`filter`过滤器，不明白过滤器的同学，请看[《vue 内置过滤器总结（附加自定义过滤器）》](http://www.javanx.cn/20181116/vue-filter/)
`startbar`、`endbar`分别就是两个滑块了。上面添加了`touchstart`和`touchmove`事件，用于监听滑动的位置，计算值。

```html
<div class="slider">
  <div class="ruler" id="ruler" ref="ruler">
    <div ref="bar" class="bar startbar" @touchstart="startTouchstart" @touchmove="startTouchmove"></div>
    <div ref="endbar" class="bar endbar" @touchstart="endTouchstart" @touchmove="endTouchmove"></div>
  </div>
  <div class="date clearfix">
    <div class="fl">{{startStep | hoursFilter}}</div>
    <div class="fr">{{endStep | hoursFilter}}</div>
  </div>
</div>
```

```scss
#c-slider{
  .clearfix{
    &:after{
      content: '';
      display: block;
      clear: both;
    }
  }
  .slider{
    margin: auto;
    width: 80%;
    .date{
      color: #333;
      font-size: .7rem;
      margin-top: 1rem;
      .fl{
        float: left;
      }
      .fr{
        float: right;
      }
    }
    .ruler{
      background: #879BAE ;
      height: 1px;
      position:  relative;
      margin-top: 75px;
      .bar{
        position: absolute;
        top: -.5rem;
        height: 1rem;
        width: 1rem;
        border-radius: 100%;
        background: #D8D8D8;
        font-size:  0.3rem;
        line-height:  0.65rem;
        text-align:  center;
      }
      .startbar{
        left: 0;
      }
      .endbar{
        right: 0;
        background: #879BAE;
      }
    }
  }
}
```

## JS事件
下方注释很详细，就简单的介绍一下吧

### 默认值
```javascript
data () {
  return {
    $ruler: '', // 滑竿
    $bar: '', // 左侧滑块
    $endbar: '', // 右侧滑块
    startX: '', // 左侧滑块位置
    endX: '', // 右侧滑块位置
    step: '', // 滑竿在限定范围内可以分多少步
    intervalStart: 0, 
    intervalEnd: 24,
    startStep: 0,
    endStep: 24,
    amountW: '' //  滑竿多长距离
  }
}
```  

### 初始化
`Vue.nextTick`用于延迟执行一段代码，否则初始化`initSlider`很容易找不到元素。同时我们用`vm.$refs.xxx`来获取元素。
元素上面记得添加`ref`属性。

```javascript
created() {
  const vm = this;
  vm.$nextTick(() => {
    vm.initSlider();
  })
},
methods: {
  initSlider(){
    const vm = this;
    vm.$ruler = vm.$refs.ruler;
    vm.$bar = vm.$refs.bar;
    vm.$endbar = vm.$refs.endbar;
    // 滑竿多长距离
    vm.amountW = vm.$ruler.clientWidth - vm.$bar.clientWidth; 
    // 总共多少步
    vm.step = vm.amountW / (vm.intervalEnd - vm.intervalStart);
  }
}
```


### 事件
```javascript
methods: {
  startTouchstart(e) {
    const vm = this;
    // 开始滑动时滑块的位置
    vm.startX = e.touches[0].pageX;
  },
  startTouchmove(e) {
    const vm = this;

    // 滑动距离=当前滑块x距离-最开始滑块距离
    let slidedis = e.touches[0].pageX - vm.$ruler.offsetLeft; 

    // 滑动距离小于0 或者大于滑竿的宽度，return掉
    if (slidedis < 0 || slidedis > vm.amountW) {
      return;
    }
    let ste = Math.round(slidedis / vm.step);
    if ((ste + vm.intervalStart) >= vm.endStep) {
      return;
    }
    vm.startStep = ste + vm.intervalStart;
    vm.$bar.style.left = (ste * vm.step) + 'px'
  },
  endTouchstart(e) {
    const vm = this;
    // 开始滑动时滑块的位置
    vm.endX = e.touches[0].pageX; 
  },
  endTouchmove(e) {
    const vm = this;
    // 滑动距离=当前滑块x距离-最开始滑块距离
    let slidedis = e.touches[0].pageX - vm.$ruler.offsetLeft; 

    if (slidedis < 0 || slidedis > vm.amountW) {
      return;
    }
    let ste = Math.round(slidedis / vm.step);

    if (vm.startStep >= (ste + vm.intervalStart)) {
      return;
    }
    vm.endStep = ste + vm.intervalStart;

    if(vm.endStep==24){
      vm.$endbar.style.left = ''
      vm.$endbar.style.right = '0px'
    }else{
      vm.$endbar.style.left = (ste * vm.step) + 'px'
    }
  }
}
```

> 有兴趣的同学，可以继续开发，优化等等。

源码地址：[vue-c-slider](https://github.com/javanf/vue-c-slider)

## 推荐文章
[《VUE开发一个组件——日历选择控件》](http://www.javanx.cn/20181105/vue-c-calendar/)
[《VUE开发一个组件——移动端弹出层（IOS版）》](http://www.javanx.cn/20181106/vue-h5-popup/)
[《VUE开发一个组件——Vue PC城市选择控件》](http://www.javanx.cn/20181127/vue-c-city/)
[《VUE开发一个组件——Vue list列表滑动删除》](http://www.javanx.cn/20181130/vue-list-del/)