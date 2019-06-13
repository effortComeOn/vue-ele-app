<template>
  <div class="detail">
    <div class="wrapper clearfix">
      <div class="main">
        <span class="name">{{seller.name}}</span>
        <div class="star">
          <star :size="48" :score="seller.score"></star>
        </div>

        <div class="title">
          <div class="line border-1px"></div>
          <div class="text">优惠信息</div>
          <div class="line border-1px"></div>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item" v-for="(item, index) in seller.supports" :key="index">
            <icon :type="item.type" :size="2"></icon>
            <span class="description">{{item.description}}</span>
          </li>
        </ul>

        <div class="title">
          <div class="line"></div>
          <div class="text">商家公告</div>
          <div class="line"></div>
        </div>
        <div class="bulletin">
          <p class="content">{{seller.bulletin}}</p>
        </div>

      </div>
    </div>

    <div class="close" @click="hideModel">
      <i class="icon-close"></i>
    </div>
  </div>
</template>

<script>
import star from '@/components/star/star'
import icon from '@/components/icon/icon'

export default {
  props: ['seller', 'classMap', 'isShow'],
  components: {
    star,
    icon
  },
  methods: {
    hideModel () {
      this.$emit('update:isShow', false)
    }
  }
}
</script>

<style lang="less" scoped>
@import '../../../common/less/minx.less';

.detail{
  position: fixed;
  top: 0;
  left: 0;
  z-index: 100;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(7, 17,27, 0.8);
  .wrapper{
    min-height: 100%;
    width: 100%;
    .main{
      margin-top: 64px;
      text-align: center;
      padding-bottom: 64px;
      .name{
        line-height: 16px;
        font-size: 16px;
        font-weight: 700;
      }
      .star{
        margin-top: 18px;
        padding: 2px 0;
      }
      .title{
        display: flex;
        width: 80%;
        margin: 28px auto 24px auto;
        .line{
          flex: 1;
          position: relative;
          top: -6px;
          .border-1px(rgba(255, 255, 255, 0.2));
        }
        .text{
          padding: 0 12px;
          font-size: 14px;
          font-weight: 700;
        }
      }
      .supports {
        width: 80%;
        margin: 0 auto;
        .support-item{
          padding: 0 12px;
          margin-bottom: 12px;
          font-size: 0;
          text-align: left;
          &:last-child {
            margin-bottom: 0;
          }
          .description{
            line-height: 16px;
            margin-left: 4px;
            font-size: 12px;
          }
        }
      }

      .bulletin{
        width: 80%;
        margin: 0 auto;
        .content{
          padding: 0 12px;
          line-height: 24px;
          font-size: 12px;
          text-align: left;
        }
      }
    }
  }
  .close{
      position: relative;
      width: 32px;
      height: 32px;
      margin: -64px auto 0 auto;
      clear: both;
      font-size: 32px;
    }
}
</style>
