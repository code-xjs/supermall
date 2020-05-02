<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <home-swiper :banners="banners"/>
    <recommend-view :recommends="recommends"/>
    <feature-view/>
    <tab-control class="tab-control" 
                :titles="['流行','新款','精选']"
                @tabClick="tabClick"/>
    <goods-list :goods="showGoods"/>
  </div>
</template>

<script>
  import HomeSwiper from './childComps/HomeSwiper'
  import RecommendView from './childComps/RecommendView'
  import FeatureView from './childComps/FeatureView'

  import NavBar from 'components/common/navbar/NavBar'
  import TabControl from 'components/content/tabControl/TabControl'
  import GoodsList from 'components/content/goods/GoodsList'

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
        currentType: 'pop'
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
    },
    methods: {
      /**
       * 事件监听相关方法
       */
      tabClick(index){
        switch(index){
          case 0:
            this.currentType = 'pop'
            break
          case 1:
            this.currentType = 'new'
            break
          case 2:
            this.currentType = 'sell'
            break
        }
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
          // console.log(this.goods)
        })
      }
    },
  }
</script>

<style scoped>
  #home{
    padding-top: 44px;
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
</style>
