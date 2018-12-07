<template>
  <div id="c-slider">
    <div id="test1">
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
    </div>
  </div>
</template>

<script>
export default {
  name: 'vue-c-slicer',
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
  },
  filters: {
    hoursFilter(date) {
      if (date < 10) {
        return '0' + date + ':00';
      } else {
        return date + ':00';
      }
    },
  },
  created() {
    const vm = this;
    vm.$nextTick(() => {
      vm.initSlider();
    })
  },
  methods: {
    initSlider(){
      const vm = this;
      vm.$ruler = this.$refs.ruler;
      vm.$bar = this.$refs.bar;
      vm.$endbar = this.$refs.endbar;

      vm.amountW = vm.$ruler.clientWidth - vm.$bar.clientWidth; // 滑竿多长距离
      vm.step = vm.amountW / (vm.intervalEnd - vm.intervalStart); // 总共多少步
    },
    startTouchstart(e) {
      const vm = this;
      vm.startX = e.touches[0].pageX; // 开始滑动时滑块的位置
    },
    startTouchmove(e) {
      const vm = this;

      let slidedis = e.touches[0].pageX - vm.$ruler.offsetLeft; // 滑动距离=当前滑块x距离-最开始滑块距离

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
      vm.endX = e.touches[0].pageX; //开始滑动时滑块的位置
    },
    endTouchmove(e) {
      const vm = this;

      let slidedis = e.touches[0].pageX - vm.$ruler.offsetLeft; //滑动距离=当前滑块x距离-最开始滑块距离

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
}
</script>

<style lang="scss">
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
</style>