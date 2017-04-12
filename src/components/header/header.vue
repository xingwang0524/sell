<template>
    <div class="header">
      <div class="content-wrapper">
        <div class="avatar">
          <img :src="seller.avatar" width="64" height="64" />
        </div>
        <div class="content">
          <h1 class="title">
            <span class="brand"></span>
            <span class="name">{{seller.name}}</span>
          </h1>
          <div class="description">
            {{seller.description}}/{{seller.deliveryTime}}分钟送达
          </div>
          <div v-if="seller.supports" class="supports">
            <span class="icon" :class="classMap[seller.supports[0].type]"></span>
            <span class="text">{{seller.supports[0].description}}</span>
          </div>
        </div>
        <div v-if="seller.supports" class="support-count" @click="showDetail">
          <span class="count">{{seller.supports.length}}个</span>
          <span class="icon-keyboard_arrow_right"></span>
        </div>
      </div>
      <div class="buttetin-wrapper" @click="showDetail">
        <div class="buttetin-icon"></div>
        <div class="buttetin-text">{{seller.bulletin}}</div>
        <div class="icon-keyboard_arrow_right"></div>
      </div>
      <div class="bg">
        <img :src="seller.avatar" width="100%" height="100%" />
      </div>

      <!--弹框详情-->
      <transition name="fade">
        <div v-show="detailShow" class="detail" transition="fade">
          <div class="detail-wrapper">
            <div class="detail-main">
              <h1 class="name">{{seller.name}}</h1>
              <div class="star-wrapper">
                <star :size="48" :score="seller.score"></star>
              </div>
              <div class="title">
                <div class="line"></div>
                <div class="text">优惠信息</div>
                <div class="line"></div>
              </div>
              <ul v-if="seller.supports" class="supports">
                <li class="support-item" v-for="(item,index) in seller.supports">
                  <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                  <span class="text">{{seller.supports[index].description}}</span>
                </li>
              </ul>
              <div class="title">
                <div class="line"></div>
                <div class="text">商家公告</div>
                <div class="line"></div>
              </div>
              <p class="buttetin">{{seller.bulletin}}</p>
            </div>
          </div>
          <div class="detail-close" @click="closeDetail">
            <i class="icon-close"></i>
          </div>
        </div>
      </transition>
    </div>
</template>

<script type="text/ecmascript-6">
    import star from '@/components/star/star'
    export default{
      props: {
        seller: {
          type: Object
        }
      },
      data () {
        return {
          classMap: [
            'decrease',
            'discount',
            'special',
            'invoice',
            'guarantee'
          ],
          detailShow: false
        }
      },
      methods: {
        showDetail () {
          this.detailShow = true
        },
        closeDetail () {
          this.detailShow = false
        }
      },
      components: {
        star
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/base'
  .header
    position: relative
    color: #fff
    background rgba(7,17,27,0.5)
    overflow hidden
    .bg
      position: absolute
      top: 0
      left: 0
      width 100%
      height 100%
      z-index: -1
      filter blur(10px)
    .content-wrapper
      position: relative
      display: flex
      padding: 24px 12px 18px 24px
      .avatar
        width 64px
        height 64px
        overflow: hidden
        margin-right: 16px
        border-radius: 2px
      .content
        font-size: 12px
        .title
          display: flex
          align-items center
          margin: 2px 0 8px 0
          .brand
            display: block
            width : 30px
            height: 18px
            margin-right: 6px
            bg-img('brand')
            background-size: 30px 18px
            background-repeat: no-repeat
          .name
            font-size: 16px
            font-weight: bold
        .supports
          display: flex
          align-items: center
          margin-top: 10px
          .icon
            display: blcok
            width: 12px
            height: 12px
            background-size: 12px
            background-repeat: no-repeat
            margin-right: 4px
            &.decrease
              bg-img('decrease_1')
            &.discount
              bg-img('discount_1')
            &.guarantee
              bg-img('guarantee_1')
            &.invoice
              bg-img('invoice_1')
            &.special
              bg-img('special_1')
          .text
            font-size: 10px
      .support-count
         position: absolute
         right: 12px
         bottom: 14px
         width: 42px
         height: 24px
         font-size: 10px
         border-radius: 14px
         background-color: rgba(0,0,0,0.2)
         text-align: center
        .count,.icon-keyboard_arrow_right
            line-height 24px
    .buttetin-wrapper
      display flex
      height: 28px
      line-height: 28px
      font-size: 10px
      align-items: center
      background rgba(7,17,27,0.2)
      .buttetin-icon
        width: 22px
        height: 12px
        bg-img('bulletin')
        background-size: 22px 12px
        background-repeat: no-repeat
        margin-left: 12px
      .buttetin-text
        width 78%
        overflow: hidden
        text-overflow: ellipsis
        white-space: nowrap
        line-height 24px
        margin 0 4px
    .detail
      position: fixed
      top: 0
      left: 0
      z-index: 200
      width 100%
      height 100%
      overflow auto
      background rgba(7,17,27,0.8)
      -webkit-backdrop-filter blur(10px)
      backdrop-filter: blur(10px)
      .detail-wrapper
        min-height 100%
        padding-bottom 64px
        box-sizing border-box
        .detail-main
          padding-top 48px
          font-size 12px
          .name
            font-size 16px
            font-weight 700
            text-align center
          .title
            display flex
            align-items center
            padding 0 36px
            margin-bottom 24px
            .line
              flex 1
              height 1px
              background rgba(255,255,255,0.2)
            .text
              font-size 14px
              font-weight 700
              padding 0 12px
          .supports
            padding 0 36px
            margin-bottom 24px
            .support-item
              display flex
              align-items center
              padding 0 12px
              margin-bottom 12px
              .icon
                display block
                width 16px
                height 16px
                background-size 16px 16px
                background-repeat no-repeat
                margin-right: 4px
                &.decrease
                  bg-img('decrease_2')
                &.discount
                  bg-img('discount_2')
                &.guarantee
                  bg-img('guarantee_2')
                &.invoice
                  bg-img('invoice_2')
                &.special
                  bg-img('special_2')
              .text
                font-size 12px
          .buttetin
            padding 0 48px
            margin-top 24px
            font-size 12px
            line-height 24px
      .detail-close
        margin -64px auto 0
        font-size 32px
        color rgba(255,255,255,0.5)
        text-align center
    .star-wrapper
      margin 16px 0 28px 0
      text-align center
    .fade-enter-active, .fade-leave-active
      transition: opacity .5s
    .fade-enter, .fade-leave-to
      opacity: 0
</style>
