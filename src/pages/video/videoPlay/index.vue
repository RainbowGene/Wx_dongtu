<template>
  <view class="video_play">
    <image :src="videoObj.img"></image>
    <!-- 工具栏 -->
    <view class="video_tool">
      <view class="iconfont icon_shengyin" @click="unSound=!unSound">{{unSound?'静':'声'}}</view>
      <view class="iconfont icon_zhuanfa">
        <button open-type="share"></button>
      </view>
    </view>
    <!-- 工具栏 结束 -->
    <!-- 视频 -->
    <view class="video_wrap">
      <video :src="videoObj.video" objectFit="fill" :muted="unSound"></video>
    </view>
    <!-- 视频 结束 -->
    <!-- 视频 -->
    <view class="download">
      <view class="download_btn" @click="handleDownVideo">下载高清</view>
    </view>
    <!-- 视频 结束 -->
  </view>
</template>

<script>
export default {
  data() {
    return {
      videoObj: {},
      unSound: true
    };
  },
  components: {},
  onLoad() {
    //const item = getApp().globalData.video;
    this.videoObj = getApp().globalData.video;
  },
  methods: {
    async handleDownVideo() {
      await uni.showLoading({
        title: "下载中..."
      });
      // 下载视频 1,远程资源下到内存  2，内存下到本地
      const { tempFilePath } = await uni.downloadFile({
        url: this.videoObj.video
      })[1];
      await uni.saveVideoToPhotosAlbum({ filePath: tempFilePath });
      uni.hideLoading();
      await uni.showToast({ title: "下载成功！" });
    }
  }
};
</script>
<style lang='scss' scoped>
.video_play {
  position: relative;
  image {
    position: absolute;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    //cs3 滤镜
    filter: blur(20px);
  }

  .video_tool {
    height: 80rpx;
    display: flex;
    justify-content: flex-end;
    .iconfont {
      width: 80rpx;
      color: #fff;
      font-size: 50rpx;
      border-radius: 40rpx;
      background-color: rgba(0, 0, 0, 0.2);
      justify-content: center;
      align-items: center;
      text-align: center;
      margin-right: 20rpx;
    }
    .icon_zhuanfa {
      position: relative;
      button {
        position: absolute;
        width: 100%;
        height: 100%;
        opacity: 0;
      }
    }
  }

  .video_wrap {
    display: flex;
    justify-content: center;
    video {
      width: 360rpx;
      height: 600rpx;
    }
  }

  .download {
    display: flex;
    justify-content: center;
    margin-top: 30rpx;
    .download_btn {
      width: 360rpx;
      height: 80rpx;
      border-radius: 40rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
      border: 1rpx solid #fff;
      background-color: rgba(0, 0, 0, 0.6);
    }
  }
}
</style>