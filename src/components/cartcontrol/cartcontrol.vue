<template>
  <div class="cartcontrol">
    <transition name="move">
    <div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" @click.stop.event="decreaseCart"></div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.event="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      // 点击增加
      addCart(event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          // this.food.count = 1
          // 对新增的属性count,需要全局引入Vue,用Vue.set方法添加
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
//      this.dispatch('cart.add', event.target)
      },
      // 点击减少
      decreaseCart(event) {
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
    font-size: 0
    .move-enter-active, .move-leave-active
      transition: all .3s linear
    .move-enter, .move-leave-to
      opacity: 0
      transform: translateX(10px)
    .move-enter-to
      transform: rotate(180deg)
    .cart-decrease, .cart-add
      display: inline-block
      padding: 6px //小技巧：设置padding可扩大点击范围
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      text-align: center
      font-size: 10px
      color: rgb(147, 153, 159)

  /*.cart-add*/

</style>
