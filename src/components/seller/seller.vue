<template>
    <div class="seller" ref="sellerwrapper">
        <div class="seller-content">
            <div class="overview">
                <div class="title">
                    <div class="name-star">
                        <h1 class="name">{{seller.name}}</h1>
                        <div class="star-wrapper">
                            <star :size="36" :score="seller.score"></star>
                            <div class="ratingCount">({{seller.ratingCount}})</div>
                            <div class="sellCount">月售{{seller.sellCount}}单</div>
                        </div>
                    </div>
                    <div @click="toggleFavorite" class="collection">
                        <i class="icon-favorite" :class="{active: favorite}"></i>
                        <div class="word">{{favoriteText}}</div>
                    </div>
                </div>
                <div class="info">
                    <div class="info-row">
                        <p>起送价</p>
                        <div class="number">{{seller.minPrice}}<span>元</span></div>
                    </div>
                    <div class="info-row">
                        <p>商家配送</p>
                        <div class="number">{{seller.deliveryPrice}}<span>元</span></div>
                    </div>
                    <div class="info-row">
                        <p>平均配送时间</p>
                        <div class="number">{{seller.deliveryTime}}<span>分钟</span></div>
                    </div>
                </div>
            </div>
            <split></split>
            <div class="bulletin-wrapper">
                <h1 class="title">公告与活动</h1>
                <p class="bulletin">{{seller.bulletin}}</p>
            </div>
            <ul v-if="seller.supports" class="supports">
                <li class="support-item" v-for="(item,index) in seller.supports">
                    <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                    <span class="text">{{seller.supports[index].description}}</span>
                </li>
            </ul>
            <split></split>
            <div class="pics">
                <h1 class="title">商家实景</h1>
                <div class="pic-wrapper" ref="picWrapper">
                    <ul class="pic-list" ref="picList">
                        <li class="pic-item" v-for="pic in seller.pics">
                            <img :src="pic" width="120" height="90" />
                        </li>
                    </ul>
                </div>
            </div>
            <split></split>
            <div class="infos-list">
                <h1 class="title">商家信息</h1>
                <ul>
                    <li class="list" v-for="info in seller.infos">{{info}}</li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll'
    import star from '@/components/star/star'
    import split from '@/components/split/split'
    export default{
      props: {
        seller: {
          type: Object
        }
      },
      data () {
        return {
          favorite: false,
          classMap: [
            'decrease',
            'discount',
            'special',
            'invoice',
            'guarantee'
          ]
        }
      },
      watch: {
        seller: function (n, o) {
          this._initScroll()
          this._initPics()
        }
      },
      computed: {
        favoriteText () {
          return this.favorite ? '已收藏' : '收藏'
        }
      },
      mounted () {
        this._initScroll()
        this._initPics()
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
            this.scroll = new BScroll(this.$refs.sellerwrapper, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        },
        _initPics () {
          if (this.seller.pics) {
            let picWidth = 120
            let magin = 6
            let width = (picWidth + magin) * this.seller.pics.length - 6
            this.$refs.picList.style.width = width + 'px'
            this.$nextTick(() => {
              if (!this.picScroll) {
                this.picScroll = new BScroll(this.$refs.picWrapper, {
                  scrollX: true
                })
              } else {
                this.picScroll.refresh()
              }
            })
          }
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
.seller
    position absolute
    top 174px
    bottom 0
    width 100%
    overflow hidden
    .overview
        padding 18px
        .title
            display flex
            justify-content space-between
            border-1px(rgba(7,17,27,0.1))
            padding-bottom 18px
            .name-star
                .name
                    font-size 14px
                    color rgb(7,17,27)
                    margin-bottom 8px
                .star-wrapper
                    display flex
                    align-items center
                    .ratingCount,.sellCount
                        font-size 10px
                        color rgb(77,85,93)
                        margin-left 10px
            .collection
                width 36px
                text-align center
                .icon-favorite
                    font-size 24px
                    color #d4d6d9
                    &.active
                        color rgb(240,20,20)
                .word
                    font-size 10px
                    color rgb(77,85,93)
                    margin-top 4px
                    text-align center

        .info
            display flex
            box-sizing border-box
            padding-top 18px
            .info-row
                flex 1
                border-left 1px solid rgba(7,17,27,0.1)
                &:first-child
                    border-left none
                p
                    font-size 10px
                    color rgb(147,153,159)
                    margin-bottom 4px
                    text-align center
                .number
                    font-size 24px
                    color rgb(7,17,27)
                    font-weight 200
                    text-align center
                    span
                        font-size 10px
    .bulletin-wrapper
        padding 18px
        .title
            font-size 14px
            color rgb(7,17,27)
            margin-bottom 8px
        .bulletin
            font-size 12px
            color rgb(240,20,20)
            line-height 24px
            font-weight 200
    .supports
        padding 0 18px
        .support-item
            display flex
            align-items center
            padding 16px 12px
            border-top(rgba(7,17,27,0.1))
            .icon
                display: blcok
                width: 16px
                height: 16px
                background-size: 16px
                background-repeat: no-repeat
                margin-right: 6px
                &.decrease
                    bg-img('decrease_4')
                &.discount
                    bg-img('discount_4')
                &.guarantee
                    bg-img('guarantee_4')
                &.invoice
                    bg-img('invoice_4')
                &.special
                    bg-img('special_4')
            .text
                font-size: 12px
                color rgb(7,17,27)
                font-weight 200
    .pics
        padding 18px
        .title
            font-size 14px
            color rgb(7,17,27)
            margin-bottom 12px
        .pic-wrapper
            width 100%
            overflow hidden
            .pic-list
                display flex
                .pic-item
                    width 120px
                    height 90px
                    margin-right 6px
                    &:last-child
                        margin-right 0
    .infos-list
        padding 18px
        .title
            font-size 14px
            color rgb(7,17,27)
            margin-bottom 12px
        .list
            padding 12px
            font-size 12px
            color rgb(7,17,27)
            border-top(rgba(7,17,27,0.1))
</style>
