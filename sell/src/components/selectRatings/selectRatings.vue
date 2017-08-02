<template>
  <div class="ratings">
    <div class="selectType">
      <span @click="select(2, $event)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0, $event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1, $event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div class="chose" :class="{'on':ContentOnly}" @click="content($event)">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>

  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;
  export default{
    props: {
      ratings: {
        type: Array,
        default () {
          return [];
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      ContentOnly: {
        type: Boolean,
        default: true
      },
      desc: {
        type: Object,
        default () {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          };
        }
      }
    },
    methods: {
//      口碑好坏切换方法,并用$emit向父组件传递消息
      select (type, event) {
        if (!event._constructed) {
          return;
        }
        this.selectType = type;
        this.$emit('ratingSelect', type);
      },
//      只看内容切换方法，并用$emit向父组件传递消息
      content (event) {
        if (!event._constructed) {
          return;
        }
        this.ContentOnly = !this.ContentOnly;
        this.$emit('toggleContent', this.ContentOnly);
      }
    },
    computed: {
//      计算出满意的评价数量
      positives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE;
        });
      },
      //      计算出吐槽的评价数量
      negatives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
      }
    }
  };
</script>

<style lang="stylus" ref="stylesheet/stylus">
  .ratings
    background #fff
    .selectType
      padding 22px 18px
      border-bottom 1px solid rgba(1, 17, 27, 0.1)
      font-size 0
      .block
        padding 8px 12px
        height 16px
        line-height 16px
        font-size 12px
        color rgb(77, 85, 93)
        margin-right 8px
        border-radius 2px
        &.active
          color rgb(255, 255, 255)
        &.positive
          background rgba(0, 160, 220, 0.2)
          &.active
            background rgb(0, 160, 220)
        &.negative
          background rgba(77, 85, 93, 0.2)
          &.active
            background rgb(147, 153, 159)
        .count
          font-size 8px
          margin-left 4px
    .chose
      height 24px
      line-height 24px
      font-size 0
      padding 16px 18px
      border-bottom 1px solid rgba(1, 17, 27, 0.1)
      &.on
        .icon-check_circle
          color #00c850
      .icon-check_circle
        color rgb(147, 153, 159)
        display inline-block
        vertical-align top
        font-size 24px
        margin-right 6px
      .text
        color rgb(147, 153, 159)
        display inline-block
        vertical-align top
        height 24px
        line-height 24px
        font-size: 12px
</style>
