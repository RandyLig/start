<template>
  <div class="shopcart">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight': totalCount > 0}">
            <i class="icon-shopping_cart" :class="{'highlight': 0 < totalCount}"></i>
          </div>
          <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalPrice>0}">¥{{totalPrice}}</div>
        <div class="desc">另需配送费¥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click.stop.prevent="pay">
        <div class="pay" :class="payClass">
          {{payDesc}}
        </div>
      </div>
    </div>
    <div class="ball-container">
      <div transition="drop" v-for="ball in balls" v-show="ball.show" class="ball">
        <div class="inner"></div>
      </div>
    </div>
    <!--购物车列表-->
    <transition name="fold-transition">
    <div class="shopcart-list" v-show="listShow">
      <div class="header">
        <span class="title">购物车</span>
        <span class="empty" @click="empty">清空</span>
      </div>
      <div class="content" ref="listContent">
        <ul>
          <li  class="food" v-for="food in selectFoods">
            <span class="name">{{food.name}}</span>
            <span class="price">¥{{food.price*food.count}}</span>
            <div class="cartControl-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
    </transition>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="hideList"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue';
  export default{
    props: {
      selectFoods: {
        type: Array,
        default () {
          return [
            {}
          ];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data () {
      return {
        balls: [{
          show: false
        },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }],
        fold: true
      };
    },
    computed: {
      totalPrice () {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total = total + food.price * food.count;
        });
        return total;
      },
      totalCount () {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count = count + food.count;
        });
        return count;
      },
      payDesc () {
        if (this.totalPrice === 0) {
          return `¥${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差¥${diff}元起送`;
        } else {
          return '去结算';
        }
      },
      payClass () {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      },
      listShow () {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
    methods: {
      drop (el) {
        console.log(el);
      },
      toggleList () {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      hideList () {
        this.fold = true;
      },
      empty () {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      pay () {
        if (this.totalPrice < this.minPrice) {
          return;
        }
        window.alert('您的价格为{this.totalPrice}元');
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="stylus" ref="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .shopcart
    position: fixed
    left: 0
    bottom: 0
    z-index: 50
    width: 100%
    height: 48px
    .content
      display: flex
      background: #141d27
      font-size: 0
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          vertical-align: top
          border-radius: 50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            text-align: center
            background: #2b343c
            &.highlight
              background: rgb(0, 160, 220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.highlight
                color: #fff
        .num
          position: absolute
          top: 0
          right: 0
          width: 24px
          height: 16px
          line-height: 16px
          text-align: center
          border-radius: 16px
          font-size: 9px
          font-weight: 700
          color: #fff
          background: rgb(240, 20, 20)
          box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display: inline-block
          vertical-align: top
          line-height: 24px
          font-weight: 700
          font-size: 16px
          margin-top: 12px
          margin-left: 12px
          padding-right: 12px
          color: #80858a
          box-sizing: border-box
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          &.highlight
            color: #fff
        .desc
          display: inline-block
          vertical-align: top
          line-height: 24px
          margin: 12px 0 0 12px
          color: rgba(255, 255, 255, 0.4)
          font-size: 10px
      .content-right
        flex: 0 0 105px
        width: 105px
        background: #2b343c
        .pay
          font-size: 12px
          color: rgba(255, 255, 255, 0.4)
          font-weight: 700
          height: 48px
          line-height: 48px
          text-align: center
          &.not-enough
            background: #2b333b
          &.enough
            color: #fff
            background: #00b43c
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index：200
        &.drop-transition
          transition: all 0.4s
          .inner
            width: 16px
            height: 16px
            border-radius: 50%
            background: rgb(0, 160, 220)
            transition: all 0.4s
    .shopcart-list
      position: absolute;
      left: 0;
      bottom: 48px;
      z-index: -1;
      width: 100%;
      &.fold-transition-enter-active ,&.fold-transition-leave-active {
        transition: all 0.5s;
        opacity: 1;
      }
      &.fold-transition-enter, &.fold-transition-leave-to {
        opacity:0;
      }
      .header
        height: 40px;
        background: #f3f5f7;
        border-bottom:1px solid rgba(7, 17, 27,0.1);
        .title
          font-weight: 200;
          font-size: 14px;
          color: rgb(7, 17, 27);
          line-height: 40px;
          margin-left: 18px;
        .empty
          font-size: 12px;
          color: rgb(0, 160, 220);
          line-height 40px;
          margin-right 18px;
          float right;
      .content
        background #fff;
        max-height 217px;
        overflow hidden;
        .food
          display block
          box-sizing border-box
          padding 12px 0
          float right
          top 0
          left 0
          right 0
          width 375px
          border-1px(rgba(7, 17, 27,0.1))
          .name
            color:rgb(7, 17, 27);
            font-size 14px;
            line-height 24px;
            margin: 12px 18px 12px 18px;
          .price
            position: absolute
            font-size: 14px;
            color: rgb(240, 20, 20);
            font-weight 700;
            line-height 24px;
            right: 120px
          .cartControl-wrapper
            position: absolute
            right 12px
            bottom 6px
    .list-mask
      position fixed
      top 0
      left 0
      width 100%
      height 100%
      z-index -2
      background rgba(0, 17, 27, 0.6)
      -webkit-backdrop-filter blur(10px)
      &.fade-enter-active ,&.fade-leave-active {
        transition: all  0.5s;
        background: rgba(0, 17, 27, 0.6)
      }
      &.fade-enter, &.fade-leave-to {
        background: rgba(0, 17, 27, 0)
      }
</style>
