<!--  -->
<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'

export default {
  name: 'Scroll',
  data () {
    return {
      scroll: null
    }
  },
  props: {
    probeType: {
      type: Number,
      default: 0
    },
    pullUpLoad: {
      type: Boolean,
      default: false
    }
  },
  mounted() {
    //1、创建BScroll对象
    this.scroll = new BScroll(this.$refs.wrapper, {
      click: true,
      //监听滚动的位置 
      // 0 | 1 都不监听，2 手滑动时监听，惯性滑动不监听， 3 滚动或惯性都监听
      probeType: this.probeType,
      pullUpLoad: this.pullUpLoad
    })

    //2、监听滚动的位置
    this.scroll.on('scroll', (position) => {
      this.$emit('scroll', position)
    })

    //3、监听上拉刷新事件
    this.scroll.on('pullingUp', () => {
      this.$emit('pullingUp')
    })
  },
  methods: {
    scrollTo(x, y, time=1000){
      //scroll是scroll的回到顶部方法
      this.scroll.scrollTo(x, y, time)
    },
    finishPullUp(){
      this.scroll.finishPullUp()
    },
    refresh(){
      this.scroll.refresh()
    }
  },
}
</script>

<style  scoped>

</style>
