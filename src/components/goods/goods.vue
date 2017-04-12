<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul>
                <li @click="selectMenu(index,$event)" v-for="(item,index) in goods" class="menu-item" :class="{current: currentIndex===index}">
                    <p class="title-wrapper">
                        <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
                        {{item.name}}
                    </p>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul>
                <li v-for="item in goods" class="food-list food-list-hook">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                        <li @click="foodOk(food,$event)" v-for="food in item.foods" class="food-item">
                            <div class="icon">
                                <img :src="food.icon" />
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="des">{{food.description}}</p>
                                <div class="extra">
                                    <span>月售{{food.sellCount}}份</span>
                                    <span>好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span><i>¥</i>{{food.price}}</span>
                                    <span v-show="food.oldPrice"><i>¥</i>{{food.oldPrice}}</span>
                                </div>
                            </div>
                            <div class="cartcontrol-wrapper">
                                <cartcontrol :food="food"></cartcontrol>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
        <food :food="selectFood" ref="food"></food>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll'
    import shopcart from '@/components/shopcart/shopcart'
    import cartcontrol from '@/components/cartcontrol/cartcontrol'
    import food from '@/components/food/food'
    const ERR_OK = 0
    export default {
      props: {
        seller: {
          type: Object
        }
      },
      data () {
        return {
          goods: [],
          classMap: [
            'decrease',
            'discount',
            'special',
            'invoice',
            'guarantee'
          ],
          listHeight: [],
          scrollY: 0,
          selectFood: {}
        }
      },
      computed: {
        currentIndex () {
          for (let i = 0; i < this.listHeight.length; i++) {
            let height1 = this.listHeight[i]
            let height2 = this.listHeight[i + 1]
            if (!height2 || this.scrollY >= height1 && this.scrollY < height2) {
              return i
            }
          }
          return 0
        },
        selectFoods () {
          let foods = []
          this.goods.forEach((good) => {
            good.foods.forEach((food) => {
              if (food.count) {
                foods.push(food)
              }
            })
          })
          return foods
        }
      },
      created () {
        this.$http.get('./api/goods').then((response) => {
          if (response.body.errno === ERR_OK) {
            this.goods = response.body.data
            // $nextTick表示获取更新后的DOM
            this.$nextTick(() => {
            // 数据更新后执行延迟回调
              this._initScroll()
              this._calculateHeight()
            })
          }
        })
      },
      methods: {
        _initScroll () {
          this.menuScroll = new BScroll(this.$refs.menuWrapper, {
            click: true
          })
          this.foodScroll = new BScroll(this.$refs.foodsWrapper, {
            click: true,
            probeType: 3  // 参数表示监听事件的触发时间，3为延迟到事件完毕后触发
          })
          // BScroll插件接口，右边菜单滚动时触发
          this.foodScroll.on('scroll', (pos) => {
          // 得到纵坐标的值（绝对值）
            this.scrollY = Math.abs(Math.round(pos.y))
          })
        },
        // 计算右边菜单每个li的高度，将每个高度组成一个数组listHeight
        _calculateHeight () {
          // 获取class为foodsWrapper下面的每个li
          let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
          let height = 0
          this.listHeight.push(height)
          for (let i = 0; i < foodList.length; i++) {
            let item = foodList[i]
            height += item.clientHeight
            this.listHeight.push(height)
          }
        },
        // 点击左边菜单栏
        selectMenu (index, event) {
          if (!event._constructed) {
            return
          }
          let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
          let el = foodList[index]
            // BScroll插件接口
          this.foodScroll.scrollToElement(el, 300)
        },
        // 点选商品
        foodOk (food, event) {
          if (!event._constructed) {
            return
          }
          this.selectFood = food
          this.$refs.food.show()
        }
      },
      components: {
        shopcart,
        cartcontrol,
        food
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/base'
  .goods
    display flex
    position absolute
    top 174px
    bottom 46px
    width 100%
    overflow hidden
    .menu-wrapper
      width 80px
      background-color #f3f5f7
      .menu-item
        display table
        height 54px
        font-size 12px
        color rgb(7,17,27)
        line-height 14px
        padding 0 12px
        &.current
          position relative
          z-index 10
          margin-top -1px
          background-color #fff
          .title-wrapper
            font-weight 700
            border-none()
        .title-wrapper
          display table-cell
          vertical-align middle
          width 56px
          border-1px(rgba(7,17,27,0.2))
          .icon
            display: inline-table
            width: 12px
            height: 12px
            background-size: 12px
            background-repeat: no-repeat
            vertical-align top
            &.decrease
              bg-img('decrease_3')
            &.discount
              bg-img('discount_3')
            &.guarantee
              bg-img('guarantee_3')
            &.invoice
              bg-img('invoice_3')
            &.special
              bg-img('special_3')
    .foods-wrapper
      flex 1
      .title
        padding-left 14px
        line-height 26px
        border-left 2px solid #d9dde1
        font-size 12px
        color rgb(147,153,159)
        background-color #f3f5f7
      .food-item
        position relative
        display flex
        padding 18px 0
        margin 0 18px
        border-1px(rgba(7,17,27,0.1))
        &:last-child
            border-none()
        .icon
          width 57px
          height  57px
          overflow hidden
          border-radius 2px
          margin-right 10px
        .content
          flex 1
          .name
            font-size 14px
            color rgb(7,17,27)
            margin 2px 0 8px
          .des,.extra
            font-size 10px
            color rgb(147,153,159)
            margin-bottom 8px
            line-height 12px
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
          right 0
          bottom 6px
</style>
