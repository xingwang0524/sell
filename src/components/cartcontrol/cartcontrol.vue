<template>
    <div class="cartcontrol">
        <transition name="slide-fade">
            <div class="cart-decrease icon-remove_circle_outline" @click.stop.prevent="decrease" v-show="food.count>0"></div>
        </transition>
        <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
        <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
    </div>
</template>

<script type="text/ecmascript-6">
    import Vue from 'vue'
    export default{
      props: {
        food: {
          type: Object
        }
      },
      methods: {
        addCart () {
          if (!event._constructed) {
            return
          }
          if (!this.food.count) {
            Vue.set(this.food, 'count', 1)
          } else {
            this.food.count++
          }
        },
        decrease () {
          if (!event._constructed) {
            return
          }
          if (this.food.count) {
            this.food.count--
          }
        }
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
.cartcontrol
    display flex
    align-items center
    .cart-add,.cart-decrease
        font-size 24px
        color rgb(0,160,220)
        padding 6px
    .cart-count
        font-size 10px
        color rgb(147,153,159)
    .slide-fade-enter-active
        transition: all .3s ease
    .slide-fade-leave-active
        transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0)
    .slide-fade-enter, .slide-fade-leave-to
        transform translate3D(24px,0,0)
        opacity 0

</style>
