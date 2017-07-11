<template>
  <div class="shopcart">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight': totalCount>0}">
            <span class="icon-shopping_cart" :class="{'highlight': totalCount>0}"></span>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalPrice>0}">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click.stop.event="pay">
        <div class="pay" :class="payClass">{{payDesc}}</div>
      </div>
    </div>
    <!--添加小球-->
    <div class="ball-container">
      <transition-group>
        <div v-for="ball in balls" v-show="ball.show" class="ball" key="drop">
          <div class="inner inner-hook" key="innerDrop"></div>
        </div>
      </transition-group>
    </div>
    <!--购物车列表-->
    <!--此处应有动画-->
    <transition name="fold">
    <div class="shopcart-list" v-show="listShow">
      <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class="empty" @click="empty">清空</span>
      </div>
      <div class="list-content" ref="listContent">
        <ul>
          <li class="food" v-for="food in selectFoods">
            <span class="name">{{food.name}}</span>
            <div class="price">
              <span>￥{{food.price * food.count}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
    </transition>
  </div>
  <!--购物车详情页模糊背景-->
  <!--<div class="list-mask" v-show="listShow"></div>-->
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import cartcontrol from '../cartcontrol/cartcontrol.vue'

  export default {
    props: {
      // 定义一个数组，包含选择商品的价格和数量
      // 购物车所有数据变化都依赖于selectFoods
      selectFoods: {
        type: Array,
        default() {
          return []
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: [],
        // 购物车详情页默认折叠
        fold: true
      }
    },
    computed: {
      // 选择商品的总价格
      totalPrice() {
        let total = 0
        this.selectFoods.forEach((food) => {
          total += food.price * food.count
        })
        return total
      },
      // 选择商品的总数
      totalCount() {
        let count = 0
        this.selectFoods.forEach((food) => {
          count += food.count
        })
        return count
      },
      // 反应右侧价格变化(与总价相关)
      payDesc() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}起送`
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice
          return `还差￥${diff}元起送`
        } else {
          return '去结算'
        }
      },
      // 反应右侧样式的变化
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough'
        } else {
          return 'enough'
        }
      },
      // 控制购物车详情页的显示状态
      listShow() {
        if (!this.totalCount) {
          this.fold = true
          return false
        }
        let show = !this.fold
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs['listContent'], {
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
    components: {
      cartcontrol
    },
    methods: {
      toggleList() {
        if (!this.totalCount) {
          return
        }
        this.fold = !this.fold
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0
        })
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return
        }
        window.alert(`支付${this.totalPrice}元`)
      }
//      drop(el) {
//        for (let i = 0; i < this.balls.length; i++) {
//          let ball = this.balls[i]
//          if (!ball.show) {
//            ball.show = true
//            ball.el = el
//            this.dropBalls.push(ball)
//            return
//          }
//        }
//      }
//    },
//    transitions: {
//      drop: {
//        beforeEnter(el) {
//          let count = this.balls.length
//          while (count--) {
//            let ball = this.balls[count]
//            if (ball.show) {
//              //获取元素相对屏幕视口的位置
//              let rect = ball.el.getBoundingClientRect()
//              let x = rect.left - 32
//              let y = -(window.innerHeight - rect.top - 22)
//              el.style.display = ''
//              el.style.webkitTransform = `translate3d(0, ${y}px, 0)` // 有变量时用反引号
//              el.style.transform = `translate3d(0, ${y}px, 0)`
//              // 内层元素
//              let inner = el.getElementsByClassName('inner-hook')[0]
//              inner.style.webkitTransform = `translate3d(0, ${x}px, 0)`
//              inner.style.transform = `translate3d(0, ${x}px, 0)`
//            }
//          }
//        },
//        enter(el) {
//          /* eslint-disable no-unused-vars */
//          let rf = el.offsetHeight
//          this.$nextTick(() => {
//            el.style.webkitTransform = 'translate3d(0,0, 0)' // 无变量时用引号
//            el.style.transform = 'translate3d(0,0, 0)'
//            let inner = el.getElementsByClassName('inner-hook')[0]
//            inner.style.webkitTransform = 'translate3d(0,0, 0)'
//            inner.style.transform = 'translate3d(0,0, 0)'
//          })
//        },
//        afterEnter(el) {
//          let ball = this.dropBalls.shift()
//          if (ball) {
//            ball.show = false
//            el.style.display = 'none'
//          }
//        }
//      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .shopcart
    position: fixed
    left: 0
    bottom: 0
    z-index: 50
    width: 100%
    height: 48px
    .content
      display: flex
      background: #141d27
      /*height: 100%*/
      font-size: 0
      color: rgba(255, 255, 255, 0.4)
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          vertical-align: top
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box //小技巧：设置怪异模式，避免计算
          border-radius: 50% //小技巧：设置圆形
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            text-align: center
            background: #2b343c
            &.highlight
              background: rgb(0, 160, 220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            color: #fff
            background: rgb(240, 20, 20)
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display: inline-block
          vertical-align: top
          margin-top: 12px
          line-height: 24px //用margin-top:12px + line-height:24px 替代 line-height:48px ，保证右边框的高度
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          font-weight: 700
          &.highlight
            color: #fff
        .desc
          display: inline-block
          vertical-align: top
          margin: 12px 0 0 12px
          line-height: 24px
          font-size: 10px
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 12px
          font-weight: 700
          &.not-enough
            background: #2b333b
          &.enough
            color: #fff
            background: #00b43c
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        &.drop-enter-active, &.drop-leave-active
          transition: all .4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
        .inner
          width: 16px
          height: 16px
          border-radius: 50%
          background: rgb(0, 160, 220)
          transition: all .4s linear
    /*.fold-enter-active, .fold-leave-active
      transition: all 0.4s linear
    .fold-enter, .fold-leave-to
      tarnsform: translate3d(0, 0, 0)
    .fold-enter-to, .fold-leave
      transform: translate3d(0, -100%, 0)*/
    .shopcart-list
      position: absolute
      left: 0
      top: 0
      z-index: -1
      width: 100%
      transition: all 0.4s
      transform: translate3d(0, -100%, 0)
      .list-header
        height: 40px
        line-height: 40px
        padding: 0 18px
        background: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          float: left
          font-style: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          font-style: 12px
          color: rgb(0, 160, 220)
      .list-content
        padding: 0 18px
        max-height: 217px
        overflow: hidden
        background: #fff
        .food
          position: relative
          padding: 12px 0
          box-sizing: border-box
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7, 17, 27)
          .price
            position: absolute
            right: 90px
            bottom: 12px
            line-height: 24px
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 6px
    /*.list-mask
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: 40
      backdrop-filter: blur(10px)
      // 此处应有动画
      opacity: 1
      background: rgba(7, 17, 27, 0.6)*/
</style>
