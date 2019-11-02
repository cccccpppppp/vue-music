<template>
  <div class="slider" ref="slider">
      <div class="slider-group" ref="sliderGroup">
          <slot></slot>
      </div>
      <div class="dots"></div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import {addClass} from 'common/js/dom'

export default {
  props: {
    loop: {
      type: Boolean,
      default: true
    },
    autoPlay: {
      type: Boolean,
      default: true
    },
    interval: {
      type: Number,
      default: 4000
    }
  },
  mounted () {
    // Vue 异步执行 DOM 更新，而DOM渲染结束大约需要16-17ms，设置20ms的异步更新能保证DOM渲染完毕后再初始化slider
    setTimeout(() => {
      this._setSliderWidth()
      this._initSlider()
    }, 20)
    // 同理可使用Vue内置的$nextTick方法更新DOM
    // this.$nextTick(() => {
    //   this.scroll = new Bscroll(this.$refs.wrapper, {})
    // })
  },
  methods: {
    /**
     * 设置slider的宽度
     */
    _setSliderWidth () {
      this.children = this.$refs.sliderGroup.children // 获取sliderGroup的子元素

      let width = 0
      let sliderWidth = this.$refs.slider.clientWidth
      for (let i = 0; i < this.children.length; i++) {
        let child = this.children[i]
        addClass(child, 'slider-item')

        child.style.width = sliderWidth + 'px'
        width += sliderWidth
      }

      // 如果slider设置循环展示，宽度应再加两倍的sliderWidth
      if (this.loop) {
        width += 2 * sliderWidth
      }
      this.$refs.sliderGroup.style.width = width + 'px'
    },

    _initSlider () {
      this.slider = new BScroll(this.$refs.slider, {
        scrollX: true,
        scrollY: false,
        momentum: false,
        snap: {
          loop: this.loop,
          threshold: 0.3,
          speed: 400
        },
        // snap: true,
        // snapLoop: this.loop,
        // snapThreshold: 0.3,
        // snapSpeed: 400,
        click: true
      })
    }
  }
}
</script>

<style lang="stylus" scoped rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .slider
    min-height 1px
    .slider-group
      position relative
      overflow hidden
      white-space nowrap
      .slider-item
        float left
        box-sizing border-box
        overflow hidden
        text-align center
        a
          display block
          width 100%
          overflow hidden
          text-decoration none
        img
          display: block
          width: 100%
    .dots
      position: absolute
      right: 0
      left: 0
      bottom: 12px
      text-align: center
      font-size: 0
      .dot
        display: inline-block
        margin: 0 4px
        width: 8px
        height: 8px
        border-radius: 50%
        background: $color-text-l
        &.active
          width: 20px
          border-radius: 5px
          background: $color-text-ll
</style>