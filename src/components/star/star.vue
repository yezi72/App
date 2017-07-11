<template>
  <div class="star" :class="starType">
    <span class="star-item" :class="itemClass" v-for="itemClass in itemClasses"></span>
  </div>
</template>

<script type="text/ecmascript-6">
  const LENGTH = 5
  const CLS_ON = 'on'
  const CLS_HALF = 'half'
  const CLS_OFF = 'off'

  export default {
    props: {
      size: {
        type: Number
      },
      score: {
        type: Number
      }
    },
    computed: {
      starType() {
        return 'star-' + this.size
      },
      itemClasses() {
        let result = []
        let score = Math.floor(this.score * 2) / 2  // 小技巧，将分数转换为显示的星星数量（向下取0.5倍数）
        let hasDecimal = score % 1 !== 0  // 取小数,控制半星
        let integer = Math.floor(score)  // 取整数,控制全星
        for (let i = 0; i < integer; i++) {
          result.push(CLS_ON)
        }
        if (hasDecimal) {
          result.push(CLS_HALF)
        }
        while (result.length < LENGTH) {
          result.push(CLS_OFF)
        }
        return result
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .star
    font-size: 0
    .star-item
      display: inline-block
      background-repeat: no-repeat
    &.star-48
      .star-item
        width: 20px
        height: 20px
        margin-right: 22px
        background-size: 20px 20px
        &:last-child
          margin-right: 0
        &.on
          bg-image('star48_on') //全星
        &.half
          bg-image('star48_half') //半星
        &.off
          bg-image('star48_off') //无星
    &.star-36
      .star-item
        width: 15px
        height: 15px
        margin-right: 6px
        background-size: 15px 15px
        &:last-child
          margin-right: 0
        &.on
          bg-image('star36_on') //全星
        &.half
          bg-image('star36_half') //半星
        &.off
          bg-image('star36_off') //无星
    &.star-24
      .star-item
        width: 10px
        height: 10px
        margin-right: 3px
        background-size: 10px 10px
        &:last-child
          margin-right: 0
        &.on
          bg-image('star24_on') //全星
        &.half
          bg-image('star24_half') //半星
        &.off
          bg-image('star24_off') //无星
</style>
