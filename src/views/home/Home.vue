<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    
    <scroll class="content" 
            ref="scroll" 
            :probe-type="3" 
            @scroll='contentScroll'
            :pull-up-load="true"
            @pullingUp="loadMore">
      <home-swiper :banners="banners"/>
      <recommend-view :recommends="recommends"/>
      <feature-view/>
      <tab-control class="tab-control" 
                  :titles="['流行','新款','精选']"
                  @tabClick="tabClick"/>
      <goods-list :goods="showGoods"/>
    </scroll>

    <!-- 组件监听事件需要用 .native -->
    <back-top @click.native="backClick" v-show="isShowBackTop"/>    
  </div>
</template>

<script>
  //
  import HomeSwiper from './childComps/HomeSwiper'
  import RecommendView from './childComps/RecommendView'
  import FeatureView from './childComps/FeatureView'

  //
  import NavBar from 'components/common/navbar/NavBar'
  import TabControl from 'components/content/tabControl/TabControl'
  import GoodsList from 'components/content/goods/GoodsList'
  import Scroll from 'components/common/scroll/Scroll'
  import BackTop from 'components/content/backTop/BackTop'

  //网络请求
  import {getHomeMultidata, getHomeGoods} from 'network/home'


  export default {
    name: "Home",
    components: {
      HomeSwiper,
      RecommendView,
      FeatureView,
      
      NavBar,
      TabControl,
      GoodsList,
      Scroll,
      BackTop
    },
    data(){
      return{
        banners: [],
        recommends: [],
        goods: {
          'pop': {page: 0, list: []},
          'new': {page: 0, list: []},
          'sell': {page: 0, list: []},
        },
        //当前默认类型是-流行
        currentType: 'pop',
        //返回顶部按钮的默认显示状态
        isShowBackTop: false
      }
    },
    computed: {
      showGoods(){
        return this.goods[this.currentType].list
      }
    },
    //生命周期钩子，模板初始化后就会调用created钩子
    //钩子里面只管调用了什么方法
    //而怎样实现的在methods里面
    created() {
      //1、请求多个数据
      this.getHomeMultidata()

      //2、请求商品数据
      //流行
      this.getHomeGoods('pop')
      //型款
      this.getHomeGoods('new')
      //精品
      this.getHomeGoods('sell')

      //3、监听itme中图片加载玩完成
      this.$bus.$on('itemImageLoad', () => {
        this.$refs.scroll.refresh()
      })
    },
    methods: {
      /**
       * 事件监听相关方法
       */
      tabClick(index){
        switch(index){
          case 0:
            this.currentType = 'pop'
            console.log(Scroll)
            break
          case 1:
            this.currentType = 'new'
            break
          case 2:
            this.currentType = 'sell'
            break
        }
      },
      backClick(){
        //回到顶部
        //拿到scroll 用 .scrollTo()方法   (x, y, 毫秒)
        // this.$refs.scroll.scroll.scrollTo(0, 0, 1000)
        //或者
        //拿到scroll组件 调用scrollTo方法
        this.$refs.scroll.scrollTo(0, 0)
      },
      contentScroll(position){
        this.isShowBackTop = position.y < -1000
      },
      loadMore(){
        this.getHomeGoods(this.currentType)
      },

      /**
       * 网路请求相关方法
       */
      getHomeMultidata(){
        getHomeMultidata().then(res => {
          this.banners = res.data.banner.list;
          this.recommends = res.data.recommend.list
        })
      },
      getHomeGoods(type) {
        const page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          //push(...数组) es6 将数组依次加到另一个数组中
          this.goods[type].list.push(...res.data.list)
          //页码加1
          this.goods[type].page += 1

          this.$refs.scroll.finishPullUp()
        })
      }
    },
  }
</script>

<style scoped>
  #home{
    padding-top: 44px;
    height: 100vh;  /* 100个视口 */ 
  }

  .home-nav{
    background-color: var(--color-tint);
    color: #fff;

    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9;
  }

  .tab-control{
    position: sticky;
    top: 44px;
    background-color: #fff;
  }

  .content{
    /* margin-top: 44px; */
    height: calc(100% - 49px);
    overflow: hidden;
  }
</style>
