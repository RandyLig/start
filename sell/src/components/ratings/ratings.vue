<template>
  <div class="rating" ref="rating">
    <div class="rating-wrapper">
      <div class="overView">
        <div class="overView-left">
          <div class="score">{{seller.score}}</div>
          <div class="title">综合评价</div>
          <div class="text">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overView-right">
          <div class="grade">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore" class="star"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="grade">
            <span class="title">食物评价</span>
            <star :size="36" :score="seller.foodScore" class="star"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <selectRatings class="ratings" :select-type="selectType" :Content-only="ContentOnly" :ratings="ratings"
                     v-on:ratingSelect="select" v-on:toggleContent="content"></selectRatings>
      <div class="rating-content">
        <ul>
          <li v-for="rating in ratings" class="rating-item" v-show="showRating(rating.rateType,rating.text)">
            <div class="avatar">
              <img :src="rating.avatar" width="28" height="28">
            </div>
            <div class="content">
              <div class="name">{{rating.username}}</div>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score" class="star"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟</span>
              </div>
              <div class="text">{{rating.text}}</div>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span class="recommend-item" v-for="item in rating.recommend">{{item}}</span>
              </div>
              <div class="time">{{rating.rateTime}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../../components/star/star.vue';
  import selectRatings from '../../components/selectRatings/selectRatings.vue';
  import BScroll from 'better-scroll';
  const ALL = 2;
  const ERR_OK = 0;
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      star,
      selectRatings,
      BScroll
    },
    data () {
      return {
        ratings: [],
        selectType: ALL,
        ContentOnly: true
      };
    },
//    errno===0时表示请求成功
    created () {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.ratings = response.data;
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.rating, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
      });
    },
    methods: {
      showRating (type, text) {
        if (this.ContentOnly && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },
      //      v-on的自定义方法，处理子组件传过来的消息
      select: function (type) {
        this.selectType = type;
//        点击后刷新DOM
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      //      v-on的自定义方法，处理子组件传过来的消息
      content: function (ContentOnly) {
        this.ContentOnly = ContentOnly;
        //        点击后刷新DOM
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    }
  };
</script>

<style lang="stylus" ref="stylesheet/stylus">
  .rating
    position absolute
    top 174px
    left 0
    bottom 0
    width 100%
    overflow hidden
    background rgb(243, 245, 247)
    .overView
      display flex
      padding 18px 0
      background #fff
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      .overView-left
        flex 0 0 137px
        width 137px
        text-align center
        border-right 1px solid  rgba(147, 153, 159, .2)
        /* 自适应iphone5 时的宽度*/
        @media only screen and (max-width: 320px)
          flex 0 0 100px
          width 100px
        .score
          font-size 24px
          color rgb(255, 153, 0)
          line-height 28px
        .title
          font-size 12px
          line-height 12px
          color rgb(7, 17, 27)
          padding-top 6px
        .text
          padding-top 8px
          color rgb(147, 153, 159)
          font-size 10px
          line-height 10px
      .overView-right
        flex 1
        padding-left 24px
        padding-top 6px
        justify-content center
        align-items center
        @media only screen and (max-width: 320px)
          padding-left 6px
        .grade
          display block
          font-size 0
          .title
            display inline-block
            vertical-align top
            font-size 12px
            line-height 18px
            color rgb(7, 17, 27)
          .score
            display inline-block
            vertical-align top
            font-size 12px
            line-height 18px
            color rgb(255, 153, 0)
          .star
            display inline-block
            vertical-align top
            margin 0 12px
        .delivery
          display block
          .title
            font-size 12px
            line-height 18px
            color rgb(7, 17, 27)
          .time
            font-size 12px
            line-height 18px
            padding-left 12px
            color rgb(147, 153, 159)
    .ratings
      background #fff
      border-top 1px solid rgba(7, 17, 27, 0.1)
      margin-top 16px
    .rating-content
      padding 0 18px
      background #fff
      position relative
      .rating-item
        padding 18px 0
        display flex
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .avatar
          flex 0 0 28px
          margin-right 12px
          left 0
          top 0
          img
            border-radius 50%
        .content
          flex 1
          position relative
          .name
            font-size 10px
            line-height 12px
            color rgb(7, 17, 27)
            margin-bottom 4px
          .star-wrapper
            margin-bottom 6px
            font-size 0
            .star
              display inline-block
              vertical-align top
            .delivery
              display inline-block
              vertical-align top
              font-size 10px
              line-height 12px
              margin-left 6px
              font-weight: 200
              color rgb(147, 153, 159)
          .text
            font-size 12px
            line-height 18px
            color rgb(7, 17, 27)
            margin-bottom 8px
          .recommend
            .icon-thumb_up
              font-size 12px
              line-height 18px
              margin-right 8px
              color rgb(0, 160, 220)
            .recommend-item
              padding 0 12px
              border 1px solid rgba(7, 17, 27, 0.1)
              border-radius 2px
              margin-right 8px
              line-height 18px
              color rgb(147, 153, 159)
              font-size 9px
          .time
            position absolute
            right 0
            top 0
            font-size 10px
            line-height 12px
            font-weight: 200
            color rgb(147, 153, 159)
</style>
