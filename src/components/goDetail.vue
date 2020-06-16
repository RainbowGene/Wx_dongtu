<template>
  <view @click="handleClick">
    <slot></slot>
  </view>
</template>

<script>
export default {
  props: {
    list: Array, // 图片数组列表
    index: Number // 列表索引
  },
  methods: {
    handleClick() {
      // 将数据缓存
      getApp().globalData.imgList = this.list;
      getApp().globalData.imgIndex = this.index;
      // 点击跳转
      uni.navigateTo({
        url: "/pages/imgDetail/index"
      });
    }
  }
};
</script>
<style lang='scss' scoped>
</style>