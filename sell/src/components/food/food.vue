<template>
  <transition name="move">
  <div v-show="flag"  class="food">
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
      </div>
    </div>
  </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        flag: false
      };
    },
    methods: {
      show () {
        this.flag = true;
      },
      hide () {
        this.flag = false;
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
      width 100%
      height 72px
      background #fff
      padding 18px
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
</style>
