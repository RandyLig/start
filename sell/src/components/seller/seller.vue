<template>
  <div class="seller" ref="seller">
    <div class="seller-wrapper">
      <div class="content">
        <div class="name">
          <div class="title">{{seller.name}}</div>
          <div class="star-wrapper">
            <star class="star" :size="36" :score="seller.score"></star>
            <span class="count">({{seller.ratingCount}})</span>
            <span class="count">月售{{seller.sellCount}}单</span>
          </div>
        </div>
        <div class="delivery-info">
          <div class="three">
            <div class="title">起送价</div>
            <div class="min-price">{{seller.minPrice}}<a>元</a></div>
          </div>
          <div class="three">
            <div class="title">商家配送</div>
            <div class="delivery-price">{{seller.deliveryPrice}}<a>元</a></div>
          </div>
          <div class="three">
            <div class="title">平均配送时间</div>
            <div class="delivery-time">{{seller.deliveryTime}}<a>分钟</a></div>
          </div>
        </div>
        <div class="favorite">
          <div class="icon-favorite" :class="{active:favorite}" @click="toggleFavorite($event)"></div>
          <div class="text">{{favoriteText}}</div>
        </div>
      </div>
      <div class="seller-activity">
        <div class="bulletin">
          <div class="title">公告与活动</div>
          <div class="text">{{seller.bulletin}}</div>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item" v-for="(item,index) in seller.supports">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">{{seller.supports[index].description}}</span>
          </li>
        </ul>
      </div>
      <div class="seller-scene">
        <div class="title">商家实景</div>
        <div class="scene-wrapper" ref="sceneW">
          <ul class="scene" ref="scene">
            <li class="scene-item"  v-for="scene in seller.pics">
              <img :src="scene" width="120" height="90">
            </li>
          </ul>
        </div>
      </div>
      <div class="seller-info">
        <div class="title">商家信息</div>
        <div class="info-wrapper">
          <ul class="info-list">
            <li v-for="info in seller.infos" class="info-item">{{info}}</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../../components/star/star.vue';
  import BScroll from 'better-scroll';
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        favorite: true
      };
    },
    watch: {
      'seller' () {
        this._initScroll();
        this._initScene();
      }
    },
    methods: {
//      实现页面滚动
      _initScroll () {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.seller, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
//      实现商家实景左右滚动
      _initScene () {
        if (this.seller.pics) {
          let picWidth = 120;
          let marginRight = 6;
          let width = (picWidth + marginRight) * this.seller.pics.length;
          this.$refs.scene.style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$refs.sceneW, {
                scrollX: true,
                eventPassThrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
      },
      toggleFavorite (event) {
        if (!event._constructed) {
          return;
        }
        this.favorite = !this.favorite;
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    components: {
      star,
      BScroll
    },
    mounted: function () {
      this.$nextTick(function () {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.seller, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      });
      this._initScene();
    },
    computed: {
      favoriteText () {
        return this.favorite ? '已收藏' : '收藏';
      }
    }
  };
</script>

<style lang="stylus" ref="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .seller
    position absolute
    top 174px
    left 0
    bottom 0
    width 100%
    overflow hidden
    background rgb(243, 245, 247)
    .seller-wrapper
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      .content
        background #fff
        padding 0 18px
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .name
          padding 18px 0 0 0
          border-bottom 1px solid rgba(7, 17, 27, 0.1)
          .title
            color rgb(7, 17, 27)
            margin-bottom 8px
            line-height 14px
            font-size 14px
          .star-wrapper
            padding-bottom 18px
            .star
              display inline-block
              vertical-align top
            .count
              display inline-block
              vertical-align top
              color rgb(77, 85, 93)
              font-size 10px
              line-height 18px
              margin-left 12px
        .delivery-info
          display flex
          flex-flow row
          background #fff
          width 100%
          .three
            flex 1.0
            text-align center
            padding 18px 0
            .title
              font-size 10px
              line-height 10px
              color rgb(147, 153, 159)
              margin-bottom 4px
            .min-price, .delivery-price, .delivery-time
              font-size 24px
              line-height 24px
              color rgb(7, 17, 27)
              font-weight 200
              a
                font-size 10px
        .favorite
          position absolute
          right 11px
          top 12px
          text-align center
          width 40px
          .icon-favorite
            color #d4d6d9
            font-size 24px
            line-height 24px
            &.active
              color rgb(240, 20, 20)
          .text
            margin-top 4px
            font-size 10px
            line-height 10px
            color rgb(77, 85, 93)
      .seller-activity
        margin-top 16px
        background #fff
        border-top 1px solid rgba(7, 17, 27, 0.1)
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .bulletin
          padding 0 18px
          .title
            padding 18px 0 0 0
          .text
            padding 8px 12px 16px 12px
            font-size 12px
            line-height 24px
            color rgb(240, 20, 20)
            font-weight 200
            border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .supports
          padding 0 16px
          .support-item
            padding 12px 16px
            font-size: 0
            border-bottom 1px solid rgba(7, 17, 27, 0.1)
            &:last-child
              border none
              margin-bottom: 0
            .icon
              display: inline-block
              vertical-align: top
              width: 16px
              height: 16px
              margin-right: 6px
              background-size: 16px 16px
              background-repeat: no-repeat
              &.decrease
                bg-image('decrease_4')
              &.discount
                bg-image('discount_4')
              &.guarantee
                bg-image('guarantee_4')
              &.invoice
                bg-image('invoice_4')
              &.special
                bg-image('special_4')
            .text
              line-height: 16px
              font-size: 12px
              font-weight 200
              color rgb(7, 17, 27)
      .seller-scene
        background #fff
        margin-top 16px
        padding 0 18px
        border-top 1px solid rgba(7, 17, 27, 0.1)
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .title
          padding 18px 0 0 0
        .scene-wrapper
          white-space nowrap
          overflow hidden
          width 100%
          .scene
            font-size 0
            padding 12px 18px 18px 0
            .scene-item
              margin-right 6px
              display inline-block
      .seller-info
        background #fff
        margin-top 16px
        padding 0 18px
        border-top 1px solid rgba(7, 17, 27, 0.1)
        .title
          padding 18px 0
        .info-wrapper
          .info-list
            .info-item
              font-size 12px
              line-height 16px
              font-weight 200
              color rgb(7, 17, 27)
              padding 16px 12px
              border-bottom 1px solid rgba(7, 17, 27, 0.1)
</style>
