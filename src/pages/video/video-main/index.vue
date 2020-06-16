<template>
  <scroll-view class="video_main" @scrolltolower="handleToLower" enable-flex scroll-y>
    <view class="video_item" v-for="item in videowp" :key="item.id">
      <image mode="widthFix" :src="item.img" @click="handleGoVideo(item)"></image>
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      videowp: [],
      hasMore: true
    };
  },
  props: {
    urlobj: Object
  },
  mounted() {
    this.getList();
  },
  watch: {
    // 监听urlobj 的变化
    urlobj() {
      this.getList();
      this.hasMore = true;
      this.videowp = [];
    }
  },
  methods: {
    getList() {
      this.request({
        url: this.urlobj.url,
        data: this.urlobj.params
      }).then(res => {
        if (res.res.videowp.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title: "没有更多数据了!",
            icon: "none"
          });
          return;
        }
        this.videowp = [...this.videowp, ...res.res.videowp];
      });
    },
    handleToLower() {
      if (this.hasMore) {
        this.urlobj.params.skip += this.urlobj.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "已经到底了!",
          icon: "none"
        });
      }
    },
    handleGoVideo(item){
      getApp().globalData.video = item;
      // 跳转到播放界面
      uni.navigateTo({
        url:'/pages/video/videoPlay/index'
      })
    }
  }
};
</script>
<style lang='scss' scoped>
.video_main {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .video_item {
    width: 33.3%;
    border: 5rpx solid #eee;
  }
}
</style>