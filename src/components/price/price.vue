<template>
  <div class="price">
    <span class="now">¥{{now}}</span><span class="old" v-show="old">¥{{old}}</span>
  </div>
</template>

<script>
export default {
  props: ['now','old']
}
</script>

<style lang="less">
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
</style>
