<template>
  <div class="header">
    <!-- 内容 -->
    <div class="header-content">
      <div class="avatar">
        <img :src="seller.avatar" width="64" height="64">
      </div>

      <div class="desc">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="desc-content">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="supports">
          <v-icon :type="seller.supports[0].type" :size="1"></v-icon>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <!-- 公告 -->
    <div class="header-bulletin" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <!-- 背景图 -->
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <transition name="fade">
      <v-dialog v-show="isShow" :seller="seller" :isShow.sync="isShow"></v-dialog>
    </transition>
  </div>
</template>

<script>
import dialog from './model/index'
import icon from '@/components/icon/icon'

export default {
  props: ['seller'],
  data () {
    return {
      isShow: false
    }
  },
  components: {
    'v-dialog': dialog,
    'v-icon': icon
  },
  methods: {
    showDetail () {
      this.isShow = true
    },
    hideModel (child) {
      this.isShow = child
    }
  }
}
</script>

<style lang="less" scoped>
@import '../../common/less/minx.less';

.header {
  position: relative;
  overflow: hidden;
  color: #fff;
  background-color: rgba(7, 17, 27, 0.5);
  .header-content {
    position: relative;
    padding: 24px 12px 18px 24px;
    font-size: 0;
    .avatar {
      display: inline-block;
      vertical-align: top;
      img {
        border-radius: 2px;
      }
    }
    .desc {
      display: inline-block;
      margin-left: 16px;
      .title {
        margin: 2px 0 8px 0;
        .brand {
          display: inline-block;
          width: 30px;
          height: 18px;
          .bg-img('brand');
          background-size: 30px 18px;
          background-repeat: no-repeat;
          vertical-align: top;
        }
        .name{
          margin-left: 6px;
          font-size: 16px;
          line-height: 18px;
          font-weight: bold;
        }
      }
      .desc-content{
        margin-bottom: 10px;
        line-height: 12px;
        font-size: 12px;
      }
      .supports{
        .text{
          line-height: 12px;
          font-size: 10px;
        }
      }

    }
    .support-count {
      position: absolute;
      right: 12px;
      bottom: 14px;
      padding: 0 8px;
      height: 24px;
      line-height: 24px;
      border-radius: 14px;
      background-color: rgba(0,0,0,0.2);
      text-align: center;
      .count{
        vertical-align: top;
        font-size: 10px;
      }
      .icon-keyboard_arrow_right{
        margin-left: 2px;
        line-height: 24px;
        font-size: 10px;
      }
    }
  }
  .header-bulletin {
    position: relative;
    height: 28px;
    line-height: 28px;
    padding: 0 22px 0 12px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    background: rgba(7, 17, 27, 0.2);
    .bulletin-title{
      vertical-align: top;
      margin-top: 8px;
      display: inline-block;
      width: 22px;
      height: 12px;
      .bg-img('bulletin');
      background-size: 22px 12px;
      background-repeat: no-repeat;
    }
    .bulletin-text{
      vertical-align: top;
      margin:0 4px;
      font-size: 10px;
    }
    .icon-keyboard_arrow_right{
      position: absolute;
      right: 12px;
      top: 8px;
      font-size: 10px;
    }
  }
  .background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    filter: blur(10px);
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to{
    opacity: 0;
  }
}
</style>
