<template>
  <transition name="move">
    <div v-show="showFlag" class="food">
      <div class="food-content">
        <div class="img-header">
          <img :src="food.image" alt="">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div> 
        </div>

        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <v-price :now="food.price" :old="food.oldPrice"></v-price>

          <div class="cartcontrol-wrapper">
            <v-cart :food="food" @add-cart="handleCart"></v-cart>
          </div>
          <transition name="fade">
            <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count===0">加入购物车</div>        
          </transition>
        </div>

        <v-split v-show="food.info"></v-split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>

        <v-split v-show="food.ratings"></v-split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <v-ratingselect 
            :select-type.sync="selectType"
            :only-content.sync="onlyContent"
            :desc="desc"
            :ratings="food.ratings">
          </v-ratingselect>

          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length > 0">
              <li v-show="needShow(rating.rateType, rating.text)" v-for="(rating, index) in food.ratings" :key="index" class="rating-item">
                <div class="user">
                  <div class="name">{{rating.username}}</div>
                  <img :src="rating.avatar" width="12" height="12" alt="" class="avatar">
                </div>
                <div class="time">{{rating.rateTime | formatDate(rating.rateTime)}}</div>
                <p class="text">
                  <span :class="{'icon-thumb_down': rating.rateType === 1, 'icon-thumb_up': rating.rateType === 0}"></span>
                  {{rating.text}}
                </p>
              </li>
            </ul>
            <div v-show="!food.ratings || food.ratings.length === 0" class="no-rating">暂无评价</div>
          </div>
        </div>
        
      </div>
    </div>
  </transition>
</template>

<script>
import price from '@/components/price/price'
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import split from '@/components/split/split'
import ratingselect from '@/components/ratingselect/ratingselect'

import { formatDate } from '@/common/js/format'

import Vue from 'vue'
import BScroll from 'better-scroll'

const POSITIVE = 0
const NEGITIVE = 1
const ALL = 2

export default {
  props: {
    food: {
      type: Object
    }
  },
  data () {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      desc: {
        all: '全部',
        positive: '推荐',
        negitive: '吐槽'
      }
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  components: {
    'v-price': price,
    'v-cart': cartcontrol,
    'v-split': split,
    'v-ratingselect': ratingselect
  },
  watch: {
    onlyContent (val, oldval) {
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    selectType (val, oldval) {
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    }
  },
  methods: {
    _initData () {
      this.selectType = ALL,
      this.onlyContent = true,
      this.desc = {
        all: '全部',
        positive: '推荐',
        negitive: '吐槽'
      }
    },
    show () {
      this.showFlag = true
      this._initData()
      this.$nextTick(() => {
        if (!this.scroll) {
          let food = document.querySelector('.food')
          this.scroll = new BScroll(food, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    hide () {
      this.showFlag = false
    },

    handleCart (target) {
      // 传出当前加号位置
      this.$emit('add-cart', target)
    },
    addFirst (event) {
      if (!event._constructed) {
        return
      }
      this.handleCart(event.target)
      Vue.set(this.food, 'count', 1)    
    },
    needShow (type, text) {
      if (this.onlyContent && !text){
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    }
  }
}
</script>

<style lang="less">
  .food {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 48px;
    z-index: 30px;
    width: 100%;
    background-color: #fff;
    &.move-enter-active, &.move-leave-active {
      transition: all .3s linear;
    }
    &.move-enter,  &.move-leave-to {
      transform: translateX(100%);
    }

    .food-content{
      .img-header{
        position: relative;
        width: 100%;
        height: 0;
        padding-top: 100%;
        img {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
        }
        .back {
          position: absolute;
          top: 10px;
          left: 0;
          width: 40px;
          height: 40px;
          background-color: rgba(244, 244, 244, 0.1);
          border-radius: 50%;
          .icon-arrow_lift{
            display: block;
            padding: 10px;
            font-size: 20px;
            color: #fff;
          }
        }
      }
      .content{
        padding: 18px;
        position: relative;
        .title{
          line-height: 14px;
          margin-bottom: 8px;
          font-size: 14px;
          font-weight: 700;
          color: rgb(7,17,27);
        }
        .detail{
          margin-bottom: 8px;
          line-height: 10px;
          height: 10px;
          font-size: 0;
          .sell-count, .rating{
            font-size: 10px;
            color: rgb(147,153,159);
          }
          .sell-count{
            margin-right: 12px;
          }
        }
        .cartcontrol-wrapper{
          position: absolute;
          right: 12px;
          bottom: 12px;
        }
        .buy{
          position: absolute;
          right: 18px;
          bottom: 18px;
          z-index: 10px;
          height: 24px;
          line-height: 24px;
          padding: 0 12px;
          box-sizing: border-box;
          font-size: 10px;
          border-radius: 12px;
          color: #fff;
          background-color: rgb(0,160,220);
          &.fade-enter-active, &.fade-leave-active {
            transition: all .3s linear;
          }
          &.fade-enter,  &.fade-leave-to {
            opacity: 0
          }
        }
      }
      .info{
        padding: 18px;
        .title{
          line-height: 14px;
          margin-bottom: 6px;
          font-weight: 700;
          font-size: 14px;
          color: rgb(7,17,27);
        }
        .text{
          line-height: 24px;
          padding: 0 8px;
          font-weight: 200;
          font-size: 12px;
          color: rgb(77,85,93);
        }
      }
      .rating{
        padding: 16px 0; 
        .title{
          line-height: 14px;
          margin-left: 16px;
          margin-bottom: 6px;
          font-weight: 700;
          font-size: 14px;
          color: rgb(7,17,27);
        }
        .rating-wrapper{
          padding: 0 18px;
          .rating-item{
            position: relative;
            padding: 16px 0;
            border-bottom: 1px solid rgba(7,17,27,0.1);
            .user{
              position: absolute;
              right: 0;
              top: 16px;
              line-height: 12px;
              font-size: 0;
              .name{
                display: inline-block;
                margin-right: 6px;
                vertical-align: top;
                font-size: 10px;
                color: rgb(147,153,159);
              }
              .avatar{
                display: inline-block;
                vertical-align: top;
              }
            }
            .time{
              margin-bottom: 6px;
              line-height: 12px;
              font-size: 10px;
              color: rgb(147,153,159);
            }
            .text{
              line-height: 16px;
              font-size: 12px;
              color: rgb(7,17,27);
              .icon-thumb_down, .icon-thumb_up{
                margin-right: 4px;
                font-size: 12px;
              }
              .icon-thumb_up{
                color: rgb(0,160,220);
              }
              .icon-thumb_down{
                color: rgb(147,153,159);
              }

            }
          }
          .no-rating{
            padding: 16px;
            font-size: 12px;
            color: rgb(147,153,159);
            text-align: center;
          }
        }
      }
    }
  }
</style>