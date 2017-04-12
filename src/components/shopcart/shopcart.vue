<template>
    <div class="shopcart">
        <div class="content">
            <div @click="toggleList" class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{highlight: totalCount>0}"><i class="icon-shopping_cart"></i></div>
                    <div class="num" v-show="totalCount>0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{highlight: totalPrice>0}">¥ {{totalPrice}}</div>
                <div class="desc">另需配送费¥{{deliveryPrice}}元</div>
            </div>
            <div @click="pay" class="content-right" :class="{enough: isEnough}">{{payDesc}}</div>
        </div>
        <transition name="fold">
            <div class="shopcart-list" v-show="listShow">
                <div class="list-header">
                    <h1 class="title">购物车</h1>
                    <div class="empty" @click="empty">清空</div>
                </div>
                <div class="list-content" ref="listContent">
                    <ul>
                        <li class="food" v-for="food in selectFoods">
                            <div class="name">{{food.name}}</div>
                            <div class="price"><span>¥</span>{{food.price*food.count}}</div>
                            <cartcontrol :food="food"></cartcontrol>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
        <transition name="fade">
            <div class="list-mask" @click="hideList" v-show="listShow"></div>
        </transition>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll'
    import cartcontrol from '@/components/cartcontrol/cartcontrol'
    export default{
      props: {
        selectFoods: {
          type: Array,
          default () {
            return [
              {
                price: 30,
                count: 1
              }
            ]
          }
        },
        deliveryPrice: {
          type: Number
        },
        minPrice: {
          type: Number
        }
      },
      data () {
        return {
          isEnough: false,
          fold: true
        }
      },
      computed: {
        totalPrice () {
          let total = 0
          this.selectFoods.forEach((food) => {
            total += food.price * food.count
          })
          return total
        },
        totalCount () {
          let count = 0
          this.selectFoods.forEach((food) => {
            count += food.count
          })
          return count
        },
        payDesc () {
          if (this.totalPrice === 0) {
            this.isEnough = false
            return `¥${this.minPrice}元起送`
          } else if (this.totalPrice < this.minPrice) {
            this.isEnough = false
            let diff = this.minPrice - this.totalPrice
            return `还差¥${diff}元起送`
          } else {
            this.isEnough = true
            return '去结算'
          }
        },
        listShow () {
          if (!this.totalCount) {
            this.fold = true
            return
          }
          let show = !this.fold
          if (show) {
            this.$nextTick(() => {
              if (!this.scroll) {
                this.scroll = new BScroll(this.$refs.listContent, {
                  click: true
                })
              } else {
                this.scroll.refresh()
              }
            })
          }
          return show
        }
      },
      methods: {
        toggleList () {
          if (!this.totalCount) {
            return
          }
          this.fold = !this.fold
        },
        hideList () {
          this.fold = true
        },
        empty () {
          this.selectFoods.forEach((food) => {
            food.count = 0
          })
        },
        pay () {
          if (this.totalPrice < this.minPrice) {
            let diff = this.minPrice - this.totalPrice
            window.alert(`还差${diff}元，请再选择一些产品吧！`)
            return
          }
          window.alert(`共支付${this.totalPrice}元`)
        }
      },
      components: {
        cartcontrol
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/base'
.shopcart
    position fixed
    left 0
    bottom 0
    width 100%
    height 48px
    z-index 50
    .content
        display flex
        height 48px
        .content-left
            display flex
            align-items center
            flex 1
            background-color #141d27
            .logo-wrapper
                position relative
                width 56px
                height 56px
                background-color #141d27
                border-radius 50%
                margin -10px 12px 2px
                padding 6px
                box-sizing border-box
                .logo
                    width 100%
                    height 100%
                    background-color #2b343c
                    border-radius 50%
                    margin 0 auto
                    text-align center
                    &.highlight
                        background-color rgb(0,160,220)
                        .icon-shopping_cart
                            color #fff
                    .icon-shopping_cart
                        font-size 24px
                        color rgba(255,255,255,0.4)
                        line-height 44px
                .num
                    position absolute
                    top 0
                    right 0
                    width 24px
                    height 16px
                    line-height 16px
                    text-align center
                    border-radius 16px
                    font-size 9px
                    font-weight 700
                    color #fff
                    background-color rgb(240,20,20)
                    box-shadow 0 4px 8px 0 rgba(0,0,0,0.4)
            .price
                font-size 16px
                color rgba(255,255,255,0.4)
                font-weight 700
                margin-right 12px
                &.highlight
                    color #fff
            .desc
                font-size 10px
                color rgba(255,255,255,0.4)
                padding-left 12px
                line-height 24px
                border-left 1px rgba(255,255,255,0.1) solid
        .content-right
            flex 0 0 105px
            width 105px
            background-color #2b333b
            text-align center
            font-size 10px
            color rgba(255,255,255,0.4)
            font-weight 700
            line-height 48px
            &.enough
                background-color #00b43c
                color #fff
    .shopcart-list
        position absolute
        bottom 48px
        left 0
        z-index -1
        width 100%
        &.fold-enter-active, &.fold-leave-active
            transition: all .3s
        &.fold-enter, &.fold-leave-to
            transform translate3d(0,100%,0)
        .list-header
            display flex
            justify-content space-between
            align-items center
            height 40px
            padding 0 18px
            background-color #f3f5f7
            border-bottom 1px solid rgba(7,17,27,0.1)
            .title
                font-size 14px
                color rgb(7,17,27)
            .empty
                font-size 12px
                color rgb(0,160,220)
        .list-content
            padding 0 18px
            max-height 217px
            overflow hidden
            background-color #fff
            .food
                display flex
                align-items center
                height 48px
                border-1px(rgba(7,17,27,0.1))
                .name
                    flex 1
                    font-size 14px
                    color rgb(7,17,27)
                .price
                    font-size 14px
                    color rgb(240,20,20)
                    font-weight 700
                    margin 0 12px 0 18px
                    span
                        font-size 10px
                        font-weight normal
    .list-mask
        position fixed
        top 0
        left 0
        width 100%
        height 100%
        z-index -10
        background-color rgba(7,17,27,0.6)
        backdrop-filter blur(10px)
        &.fade-enter-active, &.fade-leave-active
            transition: opacity 0.5s
        &.fade-enter, &.fade-leave-to
            opacity: 0
            background-color rgba(7,17,27,0)
</style>
