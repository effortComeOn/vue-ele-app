<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="decrease icon-remove_circle_outline" v-show="food.count>0" @click="descreaseCart"></div>
    </transition>
    <div class="count" v-show="food.count>0">{{food.count}}</div>
    <div class="add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script>
import Vue from 'vue'

export default {
  props: {
    food: {
      type: Object,
      default: function () {
        return {
          count: 0
        }
      }
    }
  },
  created () {
    // console.log(this.food)
  },
  methods: {
    addCart (e) {
      if (!e._constructed){
        return
      }
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count += 1
      }
      // 传出当前加号位置
      this.$emit('add-cart', e.target)
    },

    descreaseCart (e) {
      if (this.food.count) {
        this.food.count -= 1
      }
    }
  }
}
</script>

<style lang="less" >
.cartcontrol {
  font-size: 0;
  .move-enter-active, .move-leave-active {
    transition: all .4s;
  }
  .move-enter, .move-leave-to{
    opacity: 0;
    transform: translate3d(24px, 0, 0);
  }
  .decrease,.add {
    display: inline-block;
    padding: 6px;
    line-height: 24px;
    font-size: 24px;
    color: rgb(0,160,220);
  }
  .count {
    display: inline-block;
    vertical-align: top;
    width: 12px;
    padding-top: 6px;
    line-height: 24px;
    font-size: 10px;
    font-weight: 700;
    text-align: center;
    color: rgb(147,153,159);
  }

}
</style>
