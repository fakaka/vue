<template>
    <div class="progress-bar" ref="progressBar" @click="progressClick">
        <div class="bar-inner">
            <div class="progress" ref="progress"></div>
            <div class="progress-btn-wrapper" @touchstart.prevent="progressTouchStart" @touchmove.prevent="progressTouchMove" @touchend="progressTouchEnd" ref="progressBtn">
                <div class="progress-btn"></div>
            </div>
        </div>
    </div>
</template>

<script>

const progressBtnWidth = 16

export default {
    name: 'progress-bar',
    props: {
        percent: {
            type: Number,
            default: 0
        }
    },
    data() {
        return {

        }
    },
    methods: {
        progressTouchStart(e) {
            this.touch.initeiated = true
            this.touch.startX = e.touches[0].pageX
            this.touch.left = this.$refs.progress.clientWidth
        },
        progressTouchMove(e) {
            if (!this.touch.initeiated) {
                return
            }
            const deltaX = e.touches[0].pageX - this.touch.startX
            const offsetWidth = Math.min(this.$refs.progressBar.clientWidth - progressBtnWidth, Math.max(0, this.touch.left + deltaX))
            this._offset(offsetWidth)
        },
        progressTouchEnd() {
            this.touch.initeiated = false
            this._triggerPercent()
        },
        progressClick(e) {
            const rect = this.$refs.progressBar.getBoundingClientRect()
            let offsetWidth = e.pageX - rect.left
            offsetWidth = Math.min(offsetWidth, this.$refs.progressBar.clientWidth)
            this._offset(offsetWidth)
            // 这里当我们点击 progressBtn 的时候，e.offsetX 获取不对
            // this._offset(e.offsetX)
            this._triggerPercent()
        },
        _offset(offsetWidth) {
            this.$refs.progress.style.width = `${offsetWidth}px`
            this.$refs.progressBtn.style['transform'] = `translate3d(${offsetWidth}px,0,0)`
        },
        _triggerPercent() {
            const barWidth = this.$refs.progressBar.clientWidth - progressBtnWidth
            const percent = this.$refs.progress.clientWidth / barWidth
            this.$emit('percentchange', percent)
        },
    },
    computed: {
    },
    watch: {
        percent(newPercent) {
            if (newPercent >= 0 && !this.touch.initeiated) {
                const barWidth = this.$refs.progressBar.clientWidth - progressBtnWidth
                const offsetWidth = newPercent * barWidth
                this._offset(offsetWidth)
            }
        }
    },
    created() {
        this.touch = {}
    }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .progress-bar
    height: 30px
    .bar-inner
      position: relative
      top: 13px
      height: 4px
      background: rgba(0, 0, 0, 0.3)
      .progress
        position: absolute
        height: 100%
        background: $color-theme
      .progress-btn-wrapper
        position: absolute
        left: -8px
        top: -13px
        width: 30px
        height: 30px
        .progress-btn
          position: relative
          top: 7px
          left: 7px
          box-sizing: border-box
          width: 16px
          height: 16px
          border: 3px solid $color-text
          border-radius: 50%
          background: $color-theme
</style>