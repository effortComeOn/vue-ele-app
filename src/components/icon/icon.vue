<template>
<span>
    <span v-if="size==1" :class="classMap[type]" class="icon1"></span>
    <span v-if="size==2" :class="classMap[type]" class="icon2"></span>
    <span v-if="size==3" :class="classMap[type]" class="icon3"></span>
    <span v-if="size==4" :class="classMap[type]" class="icon4"></span>
</span>
</template>

<script>
export default {
  props: ['type', 'size'],
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  }
}
</script>

<style lang="less">
@import '../../common/less/minx.less';

.icon1{
  display: inline-block;
  vertical-align: top;
  width: 12px;
  height: 12px;
  margin-right: 4px;
  background-size: 12px 12px;
  background-repeat: no-repeat;
  &.decrease {
    .bg-img('decrease_1')
  }
  &.discount {
    .bg-img('discount_1')
  }
  &.invoice {
    .bg-img('invoice_1')
  }
  &.special {
    .bg-img('special_1')
  }
  &.guarantee {
    .bg-img('guarantee_1')
  }
}

.icon2{
  display: inline-block;
  width: 16px;
  height: 16px;
  vertical-align: top;
  margin-right: 6px;
  background-repeat: no-repeat;
  background-size: 16px 16px;
  &.decrease {
    .bg-img('decrease_2')
  }
  &.discount {
    .bg-img('discount_2')
  }
  &.invoice {
    .bg-img('invoice_2')
  }
  &.special {
    .bg-img('special_2')
  }
  &.guarantee {
    .bg-img('guarantee_2')
  }
}

.icon3{
  display: inline-block;
  width: 12px;
  height: 12px;
  vertical-align: top;
  margin-right: 2px;
  background-repeat: no-repeat;
  background-size: 12px 12px;
  &.decrease {
    .bg-img('decrease_3')
  }
  &.discount {
    .bg-img('discount_3')
  }
  &.invoice {
    .bg-img('invoice_3')
  }
  &.special {
    .bg-img('special_3')
  }
  &.guarantee {
    .bg-img('guarantee_3')
  }
}

.icon4{
  display: inline-block;
  width: 16px;
  height: 16px;
  vertical-align: top;
  margin-right: 6px;
  background-repeat: no-repeat;
  background-size: 16px 16px;
  &.decrease {
    .bg-img('decrease_4')
  }
  &.discount {
    .bg-img('discount_4')
  }
  &.invoice {
    .bg-img('invoice_4')
  }
  &.special {
    .bg-img('special_4')
  }
  &.guarantee {
    .bg-img('guarantee_4')
  }
}
</style>
