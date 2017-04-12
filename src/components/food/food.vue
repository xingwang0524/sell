<template>
    <transition name="move">
        <div v-show="showFlag" class="food" ref="foodwrapper">
            <div class="food-content">
               <div class="img-header">
                   <img :src="food.image" />
                   <div class="back" @click="back"><i class="icon-arrow_lift"></i></div>
               </div>
                <div class="content">
                    <h1 class="name">{{food.name}}</h1>
                    <div class="extra">
                        <span>月售{{food.sellCount}}份</span>
                        <span>好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                        <span><i>¥</i>{{food.price}}</span>
                        <span v-show="food.oldPrice"><i>¥</i>{{food.oldPrice}}</span>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <div @click.stop.prevent="addFirst" class="jion" v-show="!food.count || food.count===0">加入购物车</div>
                        <cartcontrol :food="food" v-show="food.count"></cartcontrol>
                    </div>
                </div>
                <split v-show="food.info"></split>
                <div class="info" v-show="food.info">
                    <h1>商品介绍</h1>
                    <p>{{food.info}}</p>
                </div>
                <split></split>
                <div class="rating">
                    <h1 class="title">商品评价</h1>
                </div>

                <div class="ratingselect">
                    <div class="rating-type">
                        <span @click=select(2,$event) class="positive" :class="{active: selectType===2}">全部<a>{{food.ratings.length}}</a></span>
                        <span @click=select(0,$event) class="positive" :class="{active: selectType===0}">推荐<a>{{positives.length}}</a></span>
                        <span @click=select(1,$event) class="negative" :class="{active: selectType===1}">吐槽<a>{{negatives.length}}</a></span>
                    </div>
                    <div @click="toggleContent" class="switch" :class="{on: onlyContent}">
                        <i class="icon-check_circle"></i>
                        <span class="text">只看有内容的评价</span>
                    </div>
                </div>
                <div class="rating-wrapper">
                    <ul v-show="food.ratings && food.ratings.length">
                        <li v-show="needShow(rating.rateType,rating.text)" class="rating-item" v-for="rating in food.ratings">
                            <div class="use">
                                <div class="time">{{rating.rateTime | formatDate}}</div>
                                <div class="name">{{rating.username}}</div>
                                <img width="12" height="12" :src="rating.avatar" />
                            </div>
                            <div class="text">
                                <i :class="{'icon-thumb_up': rating.rateType===0,'icon-thumb_down': rating.rateType===1}"></i>
                                <p>{{rating.text}}</p>
                            </div>
                        </li>
                    </ul>
                    <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
                </div>
            </div>
        </div>
    </transition>
</template>

<script type="text/ecmascript-6">
    import Vue from 'vue'
    import BScroll from 'better-scroll'
    import {formatDate} from '@/common/js/date'
    import cartcontrol from '@/components/cartcontrol/cartcontrol'
    import split from '@/components/split/split'
    const POSITIVE = 0
    const NEGATIVE = 1
    const ALL = 2
    export default {
      props: {
        ratings: {
          type: Array,
          default () {
            return []
          }
        },
        food: {
          type: Object
        }
      },
      data () {
        return {
          showFlag: false,
          selectType: ALL,
          onlyContent: true
        }
      },
      computed: {
        // 获取满意的评价数量
        positives () {
          return this.food.ratings.filter((rating) => {
            return rating.rateType === POSITIVE
          })
        },
        // 获取不满意的评价数量
        negatives () {
          return this.food.ratings.filter((rating) => {
            return rating.rateType === NEGATIVE
          })
        }
      },
      methods: {
        show () {
          this.showFlag = true
          this.selectType = ALL
          this.onlyContent = true
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.foodwrapper, {
                click: true
              })
            } else {
              this.scroll.refresh()
            }
          })
        },
        back () {
          this.showFlag = false
        },
        addFirst (event) {
          if (!event._constructed) {
            return
          }
          Vue.set(this.food, 'count', 1)
        },
        needShow (type, text) {
          if (this.onlyContent && !text) {
            return false
          }
          if (this.selectType === ALL) {
            return true
          } else {
            return type === this.selectType
          }
        },
        select (type, event) {
          if (!event._constructed) {
            return
          }
          this.selectType = type
          this.$nextTick(() => {
            this.scroll.refresh()
          })
        },
        toggleContent (event) {
          if (!event._constructed) {
            return
          }
          this.onlyContent = !this.onlyContent
          this.$nextTick(() => {
            this.scroll.refresh()
          })
        }
      },
      filters: {
        formatDate (time) {
          let date = new Date(time)
          return formatDate(date, 'yyyy-MM-dd hh:mm')
        }
      },
      components: {
        cartcontrol,
        split
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/base.styl'
.food
    position fixed
    left 0
    top 0
    bottom 48px
    z-index 30
    width 100%
    background-color #fff
    .img-header
        position relative
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
            top 10px
            left 0
            .icon-arrow_lift
                display block
                padding 10px
                font-size 20px
                color #fff
    .content
        position relative
        padding 18px
        .name
            font-size 14px
            color rgb(7,17,27)
            font-weight 700
            margin-bottom  8px
        .extra
            font-size 10px
            color rgb(147,153,159)
            margin-bottom 18px
            line-height 12px
            span:first-child
                margin-right 8px
        .price
            font-size 10px
            color rgb(147,153,159)
            span:first-child
                font-size 14px
                color rgb(240,20,20)
                font-weight 700
                margin-right 8px
                i
                    font-size 10px
            span:last-child
                font-weight 700
                text-decoration line-through
        .cartcontrol-wrapper
            position absolute
            right 18px
            bottom 18px
            .jion
                width 84px
                height 36px
                background-color rgb(0,160,220)
                border-radius 18px
                font-size 10px
                color #fff
                line-height 36px
                text-align center
    .info
        padding 18px
        h1
            font-size 14px
            color rgb(7,17,27)
            margin-bottom 6px
        p
            font-size 12px
            color rgb(77,85,93)
            line-height 24px
            padding 0 8px
    .rating
        h1
            font-size 14px
            color rgb(7,17,27)
            padding 18px 18px 0 18px
    .rating-wrapper
        padding 0 18px
        .rating-item
            padding 16px 0
            border-1px(rgba(7,17,27,0.1))
            .use
                display flex
                align-items center
                margin-bottom 6px
                .time,.name
                    font-size 10px
                    color rgb(147,153,159)
                .time
                    flex 1
                img
                    border-radius 50%
                    overflow hidden
                    margin-left 6px
            .text
                display flex
                .icon-thumb_up
                    font-size 12px
                    color rgb(0,160,220)
                    line-height 16px
                .icon-thumb_down
                    font-size 12px
                    color rgb(147,153,159)
                    line-height 16px
                p
                    font-size 12px
                    color rgb(7,17,27)
                    line-height 16px
                    margin-left 4px
        .no-rating
            padding 16px 0
            font-size 12px
            color rgb(147,153,159)
    .rating-type
        padding 18px 0
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
    .move-enter-active, &.move-leave-active
        transition: all .2s linear
    .move-enter, &.move-leave-to
        transform translate3d(100%,0,0)
</style>
