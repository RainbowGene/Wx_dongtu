<template>
  <!-- 手势滑动组件 -->
  <view @touchstart="handleTouchstart" @touchend="handleTouchend">
    <slot></slot>
  </view>
</template>

<script>
export default {
  data() {
    return {
      startTime: 0,
      startX: 0,
      startY: 0
    };
  },
  methods: {
    handleTouchstart(e) {
      this.startTime = Date.now();
      this.startX = e.changedTouches[0].clientX;
      this.startY = e.changedTouches[0].clientY;
    },
    handleTouchend(e) {
      let endTime = Date.now();
      let endX = e.changedTouches[0].clientX;
      let endY = e.changedTouches[0].clientY;

      if (endTime - this.startTime > 2000) return;

      // 滑动方向
      let dire = "";
      // ①水平至少滑动10单位  ②上下移动距离不能太大（因为用户上下滑动可能触发①）
      if (Math.abs(endX - this.startX) > 10&& Math.abs(endY-this.startY)<10) {
        dire = endX - this.startX > 0 ? "r" : "l";
      } else {
        return;
      }
      this.$emit("swiperAction", { dire });
    }
  }
};
</script>
<style lang='scss' scoped>
</style>