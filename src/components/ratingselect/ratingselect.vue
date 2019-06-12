<template>
  <div class="ratingselect">
    <div class="rating-type">
      <span @click="select(2, $event)" class="block positive" :class="{'active': selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0, $event)" class="block positive" :class="{'active': selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1, $event)" class="block negitive" :class="{'active': selectType===1}">{{desc.negitive}}<span class="count">{{negitives.length}}</span></span>
    </div>
    <div class="switch" :class="{'on': onlyContent}">
      <span @click="toggleContent" class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
  const POSITIVE = 0
  const NEGITIVE = 1
  const ALL = 2

export default {
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          positive: '满意',
          negitive: '不满意'
        }
      }
    }
  },
  computed: {
    positives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === POSITIVE
      })
    },
    negitives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === NEGITIVE
      })
    }
  },
  methods: {
    select (type, event) { // 这里不可以直接改变this.selectType的值，props是无法直接赋值的
      if (!event._constructed) {
        return
      }
      this.$emit('update:selectType', type)
    },
    toggleContent (e) {
      if (!event._constructed) {
        return
      }
      this.$emit('update:onlyContent', !this.onlyContent)
    }
  }
}
</script>

<style lang="less">
@import '../../common/less/minx.less';

.ratingselect{
  .rating-type{
    padding: 18px 0;
    margin: 0 18px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
    font-size: 0;
    .block{
      display: inline-block;
      padding: 8px 12px;
      border-radius: 2px;
      margin-right: 8px;
      line-height: 16px;
      color: rgb(77,85,93);
      font-size: 12px;
      .count{
        font-size: 8px;
      }
      &.positive{
        background-color: rgba(0,160,220,0.2);
        &.active{
          background-color: rgba(0,160,220,1);
        }
      }
      &.negitive{
        background-color: rgba(77,85,93,0.2);
        &.active{
          background-color: rgba(77,85,93,1);
        }
      }
      &.active{
        color: #fff;
      }
    }
  }
  .switch{
    padding: 12px 18px;
    line-height: 24px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
    color: rgb(147,153,159);
    font-size: 0;
    &.on{
      .icon-check_circle{
        color: #00c850
      }
    }
    .icon-check_circle{
      display: inline-block;
      vertical-align: top;
      font-size: 12px;
      margin-right: 4px;
      margin-top: 6px;
    }
    .text{
      display: inline-block;
      vertical-align: top;
      font-size: 12px;
    }
  }
}
</style>
