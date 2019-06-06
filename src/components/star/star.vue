<template>
  <div class="star" :class="starType">
    <span v-for="(itemClass, index) in itemClasses" :class="itemClass" class="star-item" :key="index"></span>
  </div>
</template>

<script>
const LEN = 5
const CLS_ON = 'on'
const CLS_OFF = 'off'
const CLS_HALF = 'half'

export default {
  props: ['size', 'score'],
  computed: {
    starType () {
      return 'star-' + this.size
    },
    itemClasses () {
      let result = []
      let score = Math.floor((this.score * 2) / 2)
      let hasDecimal = score % 1 !== 0
      let intger = Math.floor(score)

      for (let i = 0; i < intger; i++) {
        result.push(CLS_ON)
      }
      if (hasDecimal) {
        result.push(CLS_HALF)
      }
      while (result.length < LEN) {
        result.push(CLS_OFF)
      }

      return result
    }
  }
}
</script>

<style lang="less">
@import '../../common/less/minx.less';

.star{
  font-size: 0;
  .star-item{
    display: inline-block;
    background-repeat: no-repeat;
  }
  &.star-48{
    .star-item {
      width: 20px;
      height: 20px;
      margin-right: 22px;
      background-size: 20px 20px;
      &:last-child{
        margin-right: 0;
      }
      &.on {
        .bg-img('star48_on');
      }
      &.off {
        .bg-img('star48_off');
      }
      &.half {
        .bg-img('star48_half');
      }
    }
  }
  &.star-36{
    .star-item {
      width: 15px;
      height: 15px;
      margin-right: 6px;
      background-size: 15px 15px;
      &:last-child{
        margin-right: 0;
      }
      &.on {
        .bg-img('star36_on');
      }
      &.off {
        .bg-img('star36_off');
      }
      &.half {
        .bg-img('star36_half');
      }
    }
  }
  &.star-24{
    .star-item {
      width: 10px;
      height: 10px;
      margin-right: 3px;
      background-size: 10px 10px;
      &:last-child{
        margin-right: 0;
      }
      &.on {
        .bg-img('star24_on');
      }
      &.off {
        .bg-img('star24_off');
      }
      &.half {
        .bg-img('star24_half');
      }
    }
  }
}
</style>
