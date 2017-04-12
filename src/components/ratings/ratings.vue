<template>
    <div class="ratings" ref="ratingswrapper">
        <div class="ratings-content">
            <div class="overview">
                <div class="overview-left">
                    <div class="score">{{seller.score}}</div>
                    <div class="title">综合评分</div>
                    <div class="rank">高于周边商家{{seller.rankRate}}%</div>
                </div>
                <div class="overview-right">
                    <div class="score-wrapper">
                        <div class="title">服务态度</div>
                        <star :size="36" :score="seller.serviceScore"></star>
                        <div class="score">{{seller.serviceScore}}</div>
                    </div>
                    <div class="score-wrapper">
                        <div class="title">商品评分</div>
                        <star :size="36" :score="seller.foodScore"></star>
                        <div class="score">{{seller.foodScore}}</div>
                    </div>
                    <div class="score-wrapper">
                        <div class="title">送达时间</div>
                        <div class="time">{{seller.deliveryTime}}分钟</div>
                    </div>
                </div>
            </div>
            <split></split>
            <div class="ratingselect">
                <div class="rating-type">
                    <span @click=select(2,$event) class="positive" :class="{active: selectType===2}">全部<a>{{ratings.length}}</a></span>
                    <span @click=select(0,$event) class="positive" :class="{active: selectType===0}">满意<a>{{positives.length}}</a></span>
                    <span @click=select(1,$event) class="negative" :class="{active: selectType===1}">不满意<a>{{negatives.length}}</a></span>
                </div>
                <div @click="toggleContent" class="switch" :class="{on: onlyContent}">
                    <i class="icon-check_circle"></i>
                    <span class="text">只看有内容的评价</span>
                </div>
            </div>
            <div class="rating-content">
                <ul v-show="ratings &&　ratings.length">
                    <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
                        <div class="avatar">
                            <img :src="rating.avatar" width="28" height="28" />
                        </div>
                        <div class="content">
                            <div class="name-time">
                                <div class="name">{{rating.username}}</div>
                                <div class="time">{{rating.rateTime | formatDate}}</div>
                            </div>
                            <div class="star-wrapper">
                                <star :size="24" :score="rating.score"></star>
                                <div class="deliveryTime" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</div>
                            </div>
                            <p class="text">{{rating.text}}</p>
                            <dl class="recommend" v-show="rating.recommend && rating.recommend.length">
                                <dt class="icon-thumb_up"></dt>
                                <dd v-for="item in rating.recommend">{{item}}</dd>
                            </dl>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll'
    import {formatDate} from '@/common/js/date'
    import star from '@/components/star/star'
    import split from '@/components/split/split'

    const ERR_OK = 0
    const POSITIVE = 0
    const NEGATIVE = 1
    const ALL = 2
    export default{
      props: {
        seller: {
          type: Object
        }
      },
      data () {
        return {
          ratings: [],
          showFlag: false,
          selectType: ALL,
          onlyContent: true
        }
      },
      created () {
        this.$http.get('/api/ratings').then((response) => {
          if (response.body.errno === ERR_OK) {
            this.ratings = response.body.data
            this.$nextTick(() => {
              if (!this.scroll) {
                this.scroll = new BScroll(this.$refs.ratingswrapper, {
                  click: true
                })
              }
            })
          }
        })
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
          this.$nextTick(() => {
            this.scroll.refresh()
          })
        },
        // 只看有内容的评价
        toggleContent (event) {
          if (!event._constructed) {
            return
          }
          this.onlyContent = !this.onlyContent
          this.$nextTick(() => {
            this.scroll.refresh()
          })
        },
        // 过滤所显示的内容
        needShow (type, text) {
          if (this.onlyContent && !text) {
            return false
          }
          if (this.selectType === ALL) {
            return true
          } else {
            return type === this.selectType
          }
        }
      },
      filters: {
        formatDate (time) {
          let date = new Date(time)
          return formatDate(date, 'yyyy-MM-dd hh:mm')
        }
      },
      components: {
        star,
        split
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/base.styl'
.ratings
    position absolute
    top 174px
    bottom 0
    width 100%
    overflow hidden
    .overview
        display flex
        padding 18px 0
        .overview-left
            flex 0 0 137
            width 137px
            border-right 1px solid rgba(7,17,27,0.2)
            text-align center
            padding 6px 0
            @media only screen and (max-width 320px)
                flex 0 0 120px
                width 120px
            .score
                font-size 24px
                color rgb(255,153,0)
                line-height 24px
                margin-bottom 6px
            .title
                font-size 12px
                color rgb(7,17,27)
                line-height 12px
                margin-bottom 8px
            .rank
                font-size 10px
                color rgb(147,153,159)
                line-height 10px
        .overview-right
            flex 1
            padding 6px 0 6px 24px
            @media only screen and (max-width 320px)
                padding-left 8px
            .score-wrapper
                display flex
                align-items center
                margin-bottom 8px
                .title
                    font-size 12px
                    color rgb(7,17,27)
                    margin-right 6px
                .score
                    font-size 12px
                    color rgb(255,153,0)
                    margin-left 6px
                .time
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
    .rating-content
        padding 0 18px
        .rating-item
            display flex
            padding 16px 0
            border-1px(rgba(7,17,27,0.1))
            .avatar
                width 28px
                height 28px
                overflow hidden
                border-radius 50%
                margin-right 12px
            .content
                flex 1
                .name-time
                    display flex
                    justify-content space-between
                    margin-bottom 4px
                    .name
                        font-size 10px
                        color rgb(7,17,27)
                        line-height 12px
                    .time
                        font-size 10px
                        color rgb(147,153,159)
                        line-height 12px
                        font-weight 200
                .star-wrapper
                    display flex
                    margin-bottom 4px
                    .deliveryTime
                        font-size 10px
                        color rgb(147,153,159)
                        line-height 18px
                        font-weight 200
                        margin-left 6px
                .text
                    font-size 12px
                    color rgb(7,17,27)
                    line-height 18px
                .recommend
                    display flex
                    align-items center
                    flex-wrap wrap
                    .icon-thumb_up
                        font-size 12px
                        color rgb(0,160,220)
                        line-height 16px
                    dd
                        font-size 9px
                        color rgb(147,153,159)
                        line-height 16px
                        padding 0 6px
                        border 1px solid rgba(7,17,27,0.1)
                        margin 4px 0 0 8px
</style>
