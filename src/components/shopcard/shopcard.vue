<template>
  <div>
    <div class="shopcard">
      <div class="content" @click="toggleList">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight': totalCount > 0}">
              <i class="icon-shopping_cart" :class="{'highlight': totalCount > 0}"></i>
            </div>
            <div class="num" v-show="totalCount>0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'highlight': totalPrice > 0}">{{totalPrice||0}}元</div>
          <div class="desc">另需配送费¥{{deliveryPrice || 0}}元</div>
        </div>
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
      </div>
      <!-- 加入购物车动画 -->
      <div class="ball-container">
          <transition-group name="drop" tag="div"
            @before-enter="beforeEnter"
            @enter="enter"
            @after="afterEnter">
            <div v-for="ball in balls" class="ball" :key="ball.id" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition-group>
      </div>
      <!-- 购物车详情 -->
      <transition name="fold">
        <div class="shopcard-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content">
            <ul>
              <li class="food border-1px" v-for="(food, index) in selectFoods" :key="index">
                <span class="name">{{food.name}}</span>
                <span class="price">¥{{food.price * food.count}}</span>
                <div class="cartwrapper">
                  <v-cart :food="food" @add-cart="handleCart"></v-cart>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-if="listShow" @click="hideList"></div>
    </transition>
  </div>
</template>

<script>
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import BScroll from 'better-scroll'

export default {
  props: {
    minPrice: {
      type: Number,
      default: 0
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    selectFoods: {
      type: Array,
      default: function () {
        return [{
          price: 10,
          count: 1
        }]
      }
    }
  },
  data () {
    return {
      balls: [
        {show: false,id: 1,el: null},
        {show: false,id: 2,el: null},
        {show: false,id: 3,el: null},
        {show: false,id: 4,el: null},
        {show: false,id: 5,el: null},
      ],
      dropBalls: [],
      fold: true
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      if (this.selectFoods) {
        this.selectFoods.forEach((food) => {
          total += food.price * food.count
        })
      }
      return total
    },

    totalCount () {
      let count = 0
      if (this.selectFoods) {
        this.selectFoods.forEach((food) => {
          count += food.count
        })
      }
      return count
    },

    payDesc () {
      let str = ''
      if (this.totalPrice === 0) {
        str = `¥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        str = `还差¥${diff}元起送`
      } else {
        str = '去结算'
      }
      return str
    },

    payClass () {
      let str = ''
      if (this.totalPrice < this.minPrice) {
        str = 'not-enough'
      } else {
        str = 'enough'
      }
      return str
    },

    listShow () {
      if (!this.totalCount) {
        this.fold = true
        return false
      }
      let show = !this.fold
      if (show) {
        if (!this.scroll) {
          this.$nextTick(() => {
            let scroll = document.querySelector('.list-content')
            this.scroll = new BScroll(scroll, {
              click: true
            })
          })
        }else{
          this.scroll.refresh()
        }
      }
      return show
    }
  },
  methods: {
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    beforeEnter (el, done) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(rect.top -22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px,0)`
          el.style.transform = `translate3d(0,${y}px,0)`

          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
          inner.style.transform = `translate3d(${x}px,0,0)`
        }
      }
    },
    enter (el, done) {
      let rf = el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)'
        el.style.transform = 'translate3d(0,0,0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        inner.style.transform = 'translate3d(0,0,0)'
        setTimeout(()=>{  // afterenter函数未调用
          this.afterEnter(el)
        }, 600)
      })
    },
    afterEnter (el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    },
    toggleList () { // 展示购物车详情
      if (!this.totalCount) {
        return
      }
      setTimeout(() => {
        this.fold = !this.fold
      }, 300)
    },

    // 购物车列表页添加动画
    handleCart (target) {
      this.drop(target)
    },
    empty () { // 清空
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    hideList () { //点击面板蒙层消失
      this.fold = true
    },

    pay () {
      if (this.totalPrice < this.minPrice) {
        return
      }
      window.alert(`支付${this.totalPrice}元`)
    }

  },
  components: {
    'v-cart': cartcontrol
  }
}
</script>

<style lang="less">
@import '../../common/less/minx.less';

.shopcard{
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 50;
  width: 100%;
  height: 48px;
  .content{
    display: flex;
    background-color: #141d27;
    font-size: 0;
    color: rgba(255, 255, 255, 0.4);
    .content-left{
      flex: 1;
      .logo-wrapper{
        display: inline-block;
        vertical-align: top;
        position: relative;
        top: -10px;
        margin: 0 12px;
        padding: 6px;
        width: 56px;
        height: 56px;
        box-sizing: border-box;
        border-radius: 50%;
        background-color: #141d27;
        .logo{
          width: 100%;
          height: 100%;
          border-radius: 50%;
          text-align: center;
          background-color: #2b343c;
          &.highlight{
            background-color: rgb(0,160,220);
          }
          .icon-shopping_cart{
            font-size: 24px;
            line-height: 44px;
            color: #80858a;
            &.highlight{
              color: #fff;
            }
          }
        }
        .num{
          position: absolute;
          top: 0;
          right: 0;
          width: 24px;
          height: 16px;
          line-height: 16px;
          text-align: center;
          border-radius: 16px;
          font-size: 9px;
          font-weight: 700;
          color: #fff;
          background-color: rgb(240,20,20);
          box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4);
        }
      }

      .price{
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin-top: 12px;
        padding-right: 12px;
        box-sizing: border-box;
        border-right: 1px solid rgba(255,255,255,0.1);
        font-size: 24px;
        font-weight: 700;
        &.highlight{
          color: #fff;
        }
      }
      .desc{
        display: inline-block;
        vertical-align: top;
        margin: 12px 0 0 12px;
        line-height: 24px;
        font-size: 10px;
      }
    }
    .content-right{
      flex: 0 0 105px;
      width: 105px;
      .pay{
        line-height: 48px;
        height: 48px;
        text-align: center;
        font-size: 12px;
        font-weight: 700;
        &.not-enough{
          background-color: #2b333b;
        }
        &.enough{
          background-color: #00b43c;
          color: #fff;
        }
      }
    }
  }
  .ball-container{
    .ball{
      position: fixed;
      left: 32px;
      bottom: 22px;
      z-index: 200;
      transition: all 0.6s cubic-bezier(0.49,-0.29,0.75,0.41);
      .inner {
        width: 16px;
        height: 16px;
        border-radius: 50%;
        background-color: rgb(0,160,220);
        transition: all 0.6s linear;
        transform: translate3d(0,0,0);
      }
    }
  }
  .shopcard-list{
    position: absolute;
    left: 0;
    top: 0;
    z-index: -1;
    width: 100%;
    transition: all .5s linear;
    transform: translateY(-100%);
    &.fold-enter-active, &.fold-leave-active {
      transition: all .5s linear;
    }
    &.fold-enter,  &.fold-leave-to {
      transform: translateY(0);
    }
    .list-header{
      height: 40px;
      line-height: 40px;
      padding: 0 18px;
      background-color: #f3f5f7;
      border-bottom: 2px solid rgba(7,17,27,0.1);
      .title{
        float: left;
        font-size: 14px;
        color: rgb(7,17,27);
      }
      .empty{
        float: right;
        font-size: 12px;
        color: rgb(0,160,220)
      }
    }
    .list-content{
      padding: 0 18px;
      max-height: 217px;
      overflow: hidden;
      background-color: #fff;
      .food{
        position: relative;
        padding: 12px 0;
        box-sizing: border-box;
        .border-1px(rgba(7,17,27,0.1));
        .name{
          line-height: 24px;
          font-size: 14px;
          color: rgb(7,17,27)
        }
        .price{
          position: absolute;
          right: 90px;
          bottom: 12px;
          line-height: 24px;
          font-size: 14px;
          font-weight: 700;
          color: rgb(240,20,20);
        }
        .cartwrapper{
          position: absolute;
          bottom: 6px;
          right: 0;
        }
      }
    }
  }
}
.list-mask{
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 40px;
  backdrop-filter: blur(10px);
  opacity: 1;
  background-color: rgba(7,17,27,0.6);
  &.fade-enter-active, &.fade-leave-active {
    transition: all .5s;
  }

  &.fade-enter, &.fade-leave-to {
    opacity: 0;
    background-color: rgba(7,17,27,0);
  }
}
</style>
