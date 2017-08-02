<template>
  <transition name="move">
  <div v-show="flag"  class="food" ref="food">
    <div class="food-wrapper">
      <div class="head-img">
        <img :src="food.image">
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="text">
          <span class="sell">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="now">¥{{food.price}}</span>
          <span v-show="food.oldPrice" class="old">{{food.oldPrice}}</span>
        </div>
        <div class="cartControl-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <transition name="buy">
        <div class="buy" v-show="!food.count||food.count===0" @click="add">加入购物车</div>
        </transition>
      </div>
      <div class="info" v-show="food.info">
        <div class="title">商家介绍</div>
        <p class="text">{{food.info}}</p>
      </div>
      <!-- vue 2.0 移除了 events 和$dispatch-->
      <div class="foodRatings">商品评价</div>
      <selectRatings :select-type="selectType" :desc="desc" :Content-only="ContentOnly" :ratings="food.ratings"
      v-on:ratingSelect="select" v-on:toggleContent="content"></selectRatings>
      <div class="rating-wrapper">
        <ul v-show="food.ratings &&food.ratings.length">
          <!-- 设置showRating的返回值来判断来显示相应内容 -->
          <li class="rating-item" v-for="rating in food.ratings" v-show="showRating(rating.rateType, rating.text)">
            <div class="user">
              <span class="name">{{rating.username}}</span>
              <img :src="rating.avatar" class="avatar" width="12" height="12" />
            </div>
            <div class="time">{{rating.rateTime}}</div>
            <p class="text">
              <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
              {{rating.text}}
            </p>
          </li>
        </ul>
        <div class="no-rating" v-show="!food.ratings || !food.ratings.length">此处没有评论！</div>
      </div>
      </div>
  </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue';
  import selectRatings from '../../components/selectRatings/selectRatings.vue';

  //  const POSITIVE = 0;
  //  const NEGATIVE = 1;
  const ALL = 2;
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        flag: false,
        selectType: ALL,
        ContentOnly: true,
        desc: {
              all: '全部',
              positive: '推荐',
              negative: '吐槽'
        }
      };
    },
    methods: {
      show () {
        this.flag = true;
        this.selectType = ALL;
        this.ContentOnly = true;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide () {
        this.flag = false;
      },
      add (event) {
        /* 防止PC端多次点击 */
        if (!event._constructed) {
          return;
        }
        Vue.set(this.food, 'count', 1);
      },
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
    },
    components: {
      cartcontrol,
      selectRatings
    },
//    以下是2.0之前的方法，不适用于2.0改版之后
//    获取子组件selectRatings传来的消息
    events: {
      'ratingSelect' (type) {
        this.selectType = type;
//        点击后刷新DOM
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      'toggleContent' (ContentOnly) {
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
  .food
    position fixed
    left 0
    top 0
    bottom 48px
    width 100%
    z-index 30
    background #f3f5f7
    &.move-enter-active ,&.move-leave-active {
      transition: all  0.5s;
      transform translate3d(0, 0, 0)
    }
    &.move-enter, &.move-leave-to {
      transform translate3d(100%, 0, 0)
    }
    .head-img
      position: relative
      width 100%
      height 0
      padding-top 100%
      img
        position absolute
        top 0
        left 0
        width 100%
        height 100%
      .back
        position absolute
        left 5px
        top 10px
        padding 10px
        .icon-arrow_lift
          width 100%
          height 100%
          color #fff
    .content
      height 72px
      background #fff
      padding 18px
      position: relative
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      .title
        font-size 14px
        line-height 14px
        font-weight 700
        color rgb(7, 17, 27)
        margin-bottom 8px
      .text
        padding 8px 0
        color rgb(147, 153, 159)
        font-size 10px
        line-height 10px
        .sell
          margin-right 12px
      .price
        font-weight: 700
        line-height: 24px
        .now
          margin-right: 8px
          font-size: 14px
          color: rgb(240, 20, 20)
        .old
          text-decoration: line-through
          font-size: 10px
          color: rgb(147, 153, 159)
      .cartControl-wrapper
        position absolute
        right 12px
        bottom 12px
      .buy
        position absolute
        padding 0 12px
        right 18px
        bottom 18px
        font-size 10px
        line-height 24px
        height 24px
        z-index 10
        background rgb(0, 160, 220)
        border-radius 12px
        color #fff
        &.buy-enter-active ,&.buy-leave-active
        {
            transition: all  0.3s;
            opacity 1
          }
        &.buy-enter, &.buy-leave-to
        {
          opacity 0
        }
    .info
      padding 18px
      background #fff
      margin-top 16px
      border-top 1px solid rgba(7, 17, 27, 0.1)
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      .text
        padding 6px 8px
        font-size 12px
        line-height 24px
        color rgb(77, 85, 93)
    .foodRatings
      padding 18px 18px 0 18px
      background #fff
      margin-top 16px
      border-top 1px solid rgba(7, 17, 27, 0.1)
    .rating-wrapper
      background rgb(255, 255, 255)
      padding 0 18px 0 18px
      .rating-item
        padding 16px 0 16px 0
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .user
          position absolute
          right 18px
          font-size 0
          .name
            display inline-block
            vertical-align top
            color rgb(147, 153, 159)
            font-size 10px
            line-height 12px
            height 12px
          .avatar
            border-radius 12px
            margin-left 3px
            line-height 12px
            height 12px
        .time
          color rgb(147, 153, 159)
          font-size 10px
          line-height 12px
          height 12px
        .text
          line-height 16px
          color rgb(7, 17, 27)
          font-size 12px
          padding-top 6px
          .icon-thumb_down
            font-size 12px
            line-height 24px
            color rgb(147, 153, 159)
            margin-right 4px
          .icon-thumb_up
            font-size 12px
            line-height 24px
            color rgb(0, 160, 220)
            margin-right 4px
      .rating-item:last-child
        border none
      .no-rating
        font-size 12px
        padding 16px 0
        color rgb(147, 153, 159)
</style>
