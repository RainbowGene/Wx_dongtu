<template>
  <scroll-view class="album_scroll_view" scroll-y @scrolltolower="handleToLower">
    <view>
      <!-- 轮播图开始 
      三个属性：autoplay indicator circular
      * 问题：图片显示问题（或不全、或过短）
      * 因为 swiper 默认高度 150px  而 image 图片宽度 320px、高度240px，base.css 中我们重置成 100%
      解决：计算图片宽高比例,将比例写到swiper标签样式
       mode="widthFix" 高度自适应
      -->
      <swiper autoplay indicator-dots circular class="album_swiper">
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb"></image>
        </swiper-item>
      </swiper>
      <!-- 轮播图结束 -->
      <!-- 列表开始 -->
      <view class="album_list">
        <navigator class="album_item" v-for="item in album" :key="item.id" :url="`/pages/album/index?id=${item.id}`">
          <view class="album_img">
            <image mode="aspectFill" :src="item.cover"></image>
          </view>
          <view class="album_info">
            <view class="album_name">{{item.name}}</view>
            <view class="album_desc">{{item.desc}}</view>
            <view class="album_btn">
              <view class="album_attention">关注</view>
            </view>
          </view>
        </navigator>
      </view>
      <!-- 列表结束 -->
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0
      },
      banner: [],
      album: [],
      hasMore: true //是否还有数据
    };
  },
  mounted() {
    // 修改页面标题
    wx.setNavigationBarTitle({ title: "专辑" });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params
      }).then(res => {
        if (res.res.album.length === 0) {
          uni.showToast({
            title: "已经到底了！",
            icon: "none"
          });
          this.hasMore = false;
          return;
        }

        if (this.banner.length === 0) {
          this.banner = res.res.banner;
        }

        this.album = [...this.album, ...res.res.album];
      });
    },
    // 下拉加载更多
    handleToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "已经到底了！",
          icon: "none"
        });
      }
    }
  }
};
</script>
<style lang='scss' scoped>
.album_scroll_view {
  height: calc(100vh - 36px);
}
.album_swiper {
  swiper {
    height: clac(750rpx / 2.3);
    image {
      height: 100%;
    }
  }
}

.album_list {
  padding: 10rpx;
  .album_item {
    padding: 10rpx 0;
    display: flex;
    flex-wrap: wrap;
    .album_img {
      flex: 1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 0 10rpx;
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rpx 0;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;

        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        padding: 10rpx 0;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: $color;
          border: 1rpx solid $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>