<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item"><router-link to="/goods">商品</router-link></div>
      <div class="tab-item"><router-link to="/ratings">评论</router-link></div>
      <div class="tab-item"><router-link to="/seller">商家</router-link></div>
    </div>
    <router-view :seller="seller"/>
  </div>
</template>

<script>
import header from '@/components/header/header'

const ERR_OK = 0

export default {
  name: 'App',
  data () {
    return {
      seller: {}
    }
  },
  components: {
    'v-header': header
  },
  created () {

  },
  mounted () {
    this.queryGoods()
  },
  methods: {
    queryGoods () {
      this.$http.get('/api/seller').then(response => {
        let seller = response.body
        if (seller.errno === ERR_OK) {
          this.seller = seller.data
        }
      }, response => {
        console.log(response)
      })
    }
  }
}
</script>

<style lang="less">
@import './common/less/minx.less';

body{
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;

  .tab {
    display: flex;
    .border-1px(rgba(7, 17, 27, 0.1));
    .tab-item {
      flex: 1;
      text-align: center;
      &>a{
        color: #2c3e50;
        text-decoration: none;
        display: inline-block;
        width: 114px;
        height: 40px;
        line-height: 40px;
        &.active{
          color: rgb(240, 20, 20)
        }
      }
    }
  }
}
</style>
