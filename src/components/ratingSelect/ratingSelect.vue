<template>
    <div class="ratingselect">
        <div class="rating-type">
            <span @click=select(2,$event) class="positive" :class="{active: selectType===2}">{{desc.all}}<a>{{ratings.length}}</a></span>
            <span @click=select(0,$event) class="positive" :class="{active: selectType===0}">{{desc.positive}}<a>{{positives.length}}</a></span>
            <span @click=select(1,$event) class="negative" :class="{active: selectType===1}">{{desc.negative}}<a>{{negatives.length}}</a></span>
        </div>
        <div @click="toggleContent" class="switch" :class="{on: onlyContent}">
            <i class="icon-check_circle"></i>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
    const POSITIVE = 0
    const NEGATIVE = 1
    const ALL = 2
    export default{
      props: {
        ratings: {
          type: Array,
          default () {
            return []
          }
        },
        selectType: {
          type: Number,
          default: ALL
        },
        onlyContent: {
          type: Boolean,
          default: false
        },
        desc: {
          type: Object,
          default () {
            return {
              all: '全部',
              positive: '满意',
              negative: '不满意'
            }
          }
        }
      },
      computed: {
        // 获取满意的评价数量
        positives () {
          return this.ratings.filter((rating) => {
            return rating.rateType === POSITIVE
          })
        },
        // 获取不满意的评价数量
        negatives () {
          return this.ratings.filter((rating) => {
            return rating.rateType === NEGATIVE
          })
        }
      },
      methods: {
        select (type, event) {
          if (!event._constructed) {
            return
          }
          this.selectType = type
        },
        toggleContent (event) {
          if (!event._constructed) {
            return
          }
          this.onlyContent = !this.onlyContent
        }
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/base.styl'
.ratingselect
   .rating-type
        padding-bottom 18px
        margin 0 18px
        border-1px(rgba(7,17,27,0.1))
        span
            padding 8px 12px
            margin-right 8px
            border-radius 1px
            font-size 12px
            color rgb(77,85,93)
            &.active
                color #fff
                a
                    color #fff
            a
                font-size 8px
                margin-left 4px
            &.positive
                background-color rgba(0,160,220,0.2)
                &.active
                    background-color rgb(0,160,220)
            &.negative
                background-color rgba(77,85,93,0.2)
                &.active
                    background-color rgb(77,85,93)
   .switch
        padding 12px 18px
        line-height 24px
        border-bottom 1px solid rgba(7,17,27,0.1)
        color rgb(147,153,159)
        font-size 0
        &.on
            .icon-check_circle
                color #00c850
        .icon-check_circle
            display inline-table
            vertical-align middle
            font-size 24px
            margin-right 4px
        .text
            display inline-table
            font-size 12px
            vertical-align middle
</style>
