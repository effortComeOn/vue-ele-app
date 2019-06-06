<template>
  <div class="goods">
    <div class="menu-wrapper">
      <ul class="menu-ul">
        <li v-for="(item, index) in goods" :key="index" class="menu-item" :class="{'current': currentIndex == index}" @click="selectMenu(index, $event)">
          <span class="text border-1px">
            <span v-if="item.type > 0">
              <v-icon :type="item.type" :size="3"></v-icon>
            </span>
            <span>{{item.name}}</span>
          </span>
        </li>
      </ul>
    </div>
    <div class="goods-wrapper">
      <ul>
        <li v-for="(item, index) in goods" class="food-list" ref="food" :key="index">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="(food, index) in item.foods" :key="index" class="food-item border-1px">
              <div class="icon">
                <img width="57" height="57" :src="food.icon" alt="">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">¥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
                </div>
                <div class="cartwrapper">
                  <v-cart :food="food" @add-cart="handleCart"></v-cart>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <v-shopcart ref="shopcart" :select-foods="selectFoods" :min-price="seller.minPrice" :delivery-price="seller.deliveryPrice"></v-shopcart>
  </div>
</template>

<script>
import icon from '@/components/icon/icon'
import shopcart from '@/components/shopcard/shopcard'
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import BScroll from 'better-scroll'

const ERR_NO = 0

export default {
  props: ['seller'],
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    }
  },
  components: {
    'v-icon': icon,
    'v-shopcart': shopcart,
    'v-cart': cartcontrol
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
    },

    selectFoods () {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  created () {
    this.$http.get('/api/goods').then(response => {
      let res = response.body
      if (res.errno === ERR_NO) {
        this.goods = res.data
        // console.log(this.goods)
        this.$nextTick(() => { // 操作dom等操作放在这里
          this._initScroll()
          this._calculateHeight()
        })
      }
    })
  },

  methods: {
    _initScroll () {
      let menu = document.querySelector('.menu-wrapper')
      this.menuScroll = new BScroll(menu, {
        click: true
      })

      let foods = document.querySelector('.goods-wrapper')
      this.foodsScroll = new BScroll(foods, {
        click: true,
        probeType: 3
      })
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },

    _calculateHeight () {
      let foodList = this.$refs.food
      let height = 0
      this.listHeight.push(height)
      if (foodList) {
        for (let i = 0; i < this.$refs.food.length; i++) {
          let item = this.$refs.food[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      }
    },

    _dropControl (target) {
      this.$nextTick(() => {
        this.$refs.shopcart.drop(target)
      })
    },
    handleCart (target) {
      this._dropControl(target)
    },

    selectMenu (index, event) {
      if (!event._constructed) {
        return
      }
      let foodList = this.$refs.food
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 300)
    }
  }
}
</script>

<style lang="less">
@import '../../common/less/minx.less';

.goods{
  position: absolute;
  top: 174px;
  bottom: 0;
  width: 100%;
  display: flex;
  overflow: hidden;
  .menu-wrapper{
    flex: 0 0 80px;
    width: 80px;
    background-color: #f3f5f7;
    .menu-ul{
      padding: 0;
      .menu-item{
        padding: 0 12px;
        display: table;
        height: 54px;
        width: 56px;
        line-height: 14px;
        &.current{
          position: relative;
          // z-index: 2;
          margin-top: -1px;
          background-color: #fff;
          font-weight: 700;
          .text{
            border: 0;
          }
        }
        .text{
          display: table-cell;
          width: 56px;
          border-bottom: 1px solid rgba(7,17,27,0.1);
          // .border-1px(rgba(7, 17, 27, 0.1));
          vertical-align: middle;
          font-size: 12px;
          &:last-child{
            border: 0
          }
        }
      }
    }
  }
  .goods-wrapper{
    flex: 1;
    .title{
      padding-left: 14px;
      height: 26px;
      line-height: 26px;
      border-left: 2px solid #d9dde1;
      font-size: 12px;
      color: rgb(147,153,159);
      background-color:#f3f5f7;
    }
    .food-item{
      display: flex;
      margin: 18px;
      padding-bottom: 18px;
      border-bottom: 1px solid #d9dde1;
      position: relative;
      // .border-1px(rgba(7, 17, 27, 0.1));
      &:last-child{
        border: 0;
        margin-bottom: 0;
      }
      .icon{
        flex: 0 0 57px;
        margin-right: 10px;
      }
      .content{
        flex: 1;
        .name{
          margin: 2px 0 8px 0;
          height: 14px;
          line-height: 14px;
          font-size: 14px;
          color: rgb(7,17,27);
        }
        .desc, .extra{
          line-height: 10px;
          font-size: 10px;
          color: rgb(147,153,159);
        }
        .desc{
          margin-bottom: 8px;
          line-height: 12px;
        }
        .extra{
          .count{
            margin-right: 12px;
          }
        }
        .price{
          font-weight: 700;
          line-height: 24px;
          .now{
            margin-right: 24px;
            font-size: 14px;
            color: rgb(240,20,20);
          }
          .old{
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147,153,159);
          }
        }
        .cartwrapper{
          position: absolute;
          right: 0;
          bottom: 12px;
        }
      }
    }
  }
}
</style>
