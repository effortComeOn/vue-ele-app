<template>
  <div class="seller">
    <div class="seller-content">
      <div class="overview">
        <div class="title">{{seller.name}}</div>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <div class="text">（{{seller.ratingCount}}）</div>
          <div class="text">月售{{seller.sellCount}}单</div>
        </div>
        <ul class="remark">
          <li class="block">
            <h2 class="title">起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="title">商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="title">平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>元
            </div>
          </li>
        </ul>
        <div class="favorite">
          <span @click="toggleFavorite" class="icon-favorite" :class="{'active': favorite}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>

      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-1px">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item border-1px" v-for="(item, index) in seller.supports" :key="index">
            <icon :type="item.type" :size="2"></icon>
            <span class="description">{{item.description}}</span>
          </li>
        </ul>
      </div>

      <split></split>
      <div class="pics">
        <div class="title">商家实景</div>
        <div class="pic-wrapper">
          <ul class="pic-list">
            <li v-for="(pic, index) in seller.pics" :key="index" class="pic-item">
              <img :src="pic" width="120" height="90" alt="">
            </li>
          </ul>
        </div>
        
      </div>

      <split></split>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li v-for="(info, index) in seller.infos" :key="index" class="info-item border-1px">
            {{info}}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import star from '@/components/star/star'
import split from '@/components/split/split'
import icon from '@/components/icon/icon'

import BScroll from 'better-scroll'

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      favorite: false
    }
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏'
    }
  },
  components: {
    star,
    split,
    icon
  },
  watch: {
    seller (val, oldval) {
      console.log('watch')
      this._initScroll()
      this._initPicScroll()
    }
  },
  mounted () {
    console.log(this.seller, 'mounted')
    this.$nextTick(() => {
      this._initScroll()
      this._initPicScroll()
    })
  },

  methods: {
    toggleFavorite (event) {
      if (!event._constructed) {
        return
      }
      this.favorite = !this.favorite
    },
    _initScroll () {
      if (!this.scroll) {
        let sellerscro = document.querySelector('.seller')
        this.scroll = new BScroll(sellerscro, {
          click: true
        })
      } else {
        this.scroll.refresh()
      }
    },

    _initPicScroll () {
      if (this.seller.pics) {
        let picWidth = 120;
        let margin = 6;
        let width = (picWidth + margin) * this.seller.pics.length - margin;

        let picList = document.querySelector('.pic-list')
        picList.style.width = width + 'px';
        if (!this.picScroll) {
          console.log('this.picScroll')
          // this.$nextTick(() => {
            
          // })
          let picWrapper = document.querySelector('.pic-wrapper')
            this.picScroll = new BScroll(picWrapper, {
              click: true,
              scrollX: true,
              eventPassthrough: 'vertical'
            })
        } else {
          console.log('this.picScroll.refresh')
          this.picScroll.refresh()
        }
      }
    }
  }
}
</script>

<style lang="less">
@import '../../common/less/minx.less';

.seller{
  position: absolute;
  top: 174px;
  left: 0;
  bottom: 0;
  width: 100%;
  overflow: hidden;
  .overview{
    padding: 18px;
    .title{
      margin-bottom: 8px;
      line-height: 14px;
      color: rgb(7,17,27);
      font-size: 14px;
    }
    .desc{
      padding-bottom: 18px;
      .border-1px(rgba(7, 17, 27, 0.1));
      font-size: 0;
      .star{
        display: inline-block;
        line-height: 18px;
        margin-right: 8px;
        vertical-align: top;
      }
      .text{
        display: inline-block;
        line-height: 18px;
        margin-right: 12px;
        vertical-align: top;
        font-size: 10px;
        color: rgb(77,85,93);
      }
    }
    .remark{
      display: flex;
      padding-top: 18px;
      .block{
        flex: 1;
        text-align: center;
        border-right: 1px solid rgba(7,17,27,0.1);
        &:last-child{
          border: none
        }
        .title{
          margin-bottom: 10px;
          line-height: 10px;
          font-size: 10px;
          color: rgb(147,153,159);
        }
        .content{
          line-height: 24px;
          font-size: 10px;
          color: rgb(7,17,27);
          .stress{
            font-size: 24px;
          }
        }
      }
    }
    .favorite{
      position: absolute;
      width: 50px;
      top: 18px;
      right: 11px;
      text-align: center;
      .icon-favorite{
        display: block;
        margin-bottom: 4px;
        line-height: 24px;
        font-size: 24px;
        color: #d4d6d9;
        &.active{
          color: rgb(240,20,20);
        }
      }
      .text{
        line-height: 10px;
        font-size: 10px;
        color: rgb(77,85,93);
      }
    }
  }
  .bulletin{
    padding: 18px 18px 0 18px;
    .title{
      margin-bottom: 8px;
      line-height: 14px;
      color: rgb(7,17,27);
      font-size: 14px;
    }
    .content-wrapper{
      padding: 0 12px 16px 12px;
      .border-1px(rgba(7, 17, 27, 0.1));
      .content{
        line-height: 24px;
        font-size: 12px;
        color: rgb(240,20,20);
      }
    }
    .supports{
      .support-item{
        padding: 16px 12px;
        font-size: 0;
        .border-1px(rgba(7, 17, 27, 0.1));
        &:last-child{
          border: none;
        }
        .description{
          line-height: 16px;
          font-size: 12px;
          color: rgb(7,17,27);
        }
      }
    }
  }
  .pics{
    padding: 12px;
    .title{
      margin-bottom: 12px;
      line-height: 14px;
      color: rgb(7,17,27);
      font-size: 14px;
    }
    .pic-wrapper{
      width: 100%;
      overflow: hidden;
      white-space: nowrap;
      .pic-list{
        font-size: 0;
        .pic-item{
          display: inline-block;
          margin-right: 6px;
          width: 120px;
          height: 90px;
          &:last-child{
            margin: 0
          }
        }
      }
    }
  }
  .info{
    padding: 18px 18px 0 18px;
    color: rgb(7,17,27);
    .title{
      padding-bottom: 12px;
      line-height: 14px;
      font-size: 14px;
      .border-1px(rgba(7, 17, 27, 0.1));
    }
    .info-item{
      padding: 16px 12px;
      line-height: 16px;
      .border-1px(rgba(7, 17, 27, 0.1));
      font-size: 12px;
      &:last-child{
        border: none;
      }
    }
  }
}
</style>
