<!--  -->
<template>
  <div class="goods">
    <goods-list-item v-for="item in goods" :key="item.iid" :goods-item="item"/>
  </div>
</template>

<script>
import GoodsListItem from './GoodsListItem';

export default {
  name: 'GoodsList',
  components: {
    GoodsListItem
  },
  props: {
    goods: {
      type: Array,
      default() {
        return []
      }
    }
  },
  data () {
    return {
    }
  },
}
</script>

<style  scoped>
  .goods{
    width: 100%;
    margin-top: 10px;
    text-align: center;
  }
</style>
