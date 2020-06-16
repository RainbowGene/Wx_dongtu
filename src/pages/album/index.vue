<template>
  <view>
    <!-- 顶部专题 -->
    <view class="album_bg">
      <image mode="widthFix" :src="wallpaper[0].thumb"></image>
      <view class="album_info">
        <view class="album_name">{{wallpaper[0].desc}}</view>
        <view class="album_btn">关注专辑</view>
      </view>
    </view>
    <!-- 顶部专题 end -->

    <!-- 专辑作者 -->
    <view class="album_author">
      <view class="album_author_info">
        <image mode="widthFix" :src="wallpaper[0].user.avatar"></image>
        <view class="author_name">{{wallpaper[0].user.name}}</view>
      </view>
      <!-- view 使用不了  \n  标签 -->
      <text class="album_author_desc">{{album.desc}}</text>
    </view>
    <!-- 专辑作者 end -->

    <!-- 列表开始 -->
    <view class="album_list">
      <view class="album_item" v-for="(item,index) in wallpaper" :key="item.id">
        <go-detail :list="wallpaper" :index="index">
          <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)"></image>
        </go-detail>
      </view>
    </view>
    <!-- 列表结束 -->
  </view>
</template>

<script>
import goDetail from '@/components/goDetail';
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
        // first为0，只返回列表
        first: 0
      },
      id: -1,
      album: {},
      wallpaper: [],
      hasMore:true
    };
  },
  components: {
    goDetail
  },
  onLoad(options) {
    this.id = options.id;
    this.getList();
  },
  // 页面触底: 整体页面触底
  onReachBottom(){
    if(this.hasMore){
      this.params.first = 0; //不是第一次请求
      this.params.skip += this.params.limit;
      this.getList()
    }
    else{
      uni.showToast({
        title:'已经到底了！',
        icon:'none'
      })
    }
  },
  methods: {
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params
      }).then(res => {
        //console.log(res);
        this.album = res.res.album;
        if(res.res.wallpaper.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title:'已经到底了！',
            icon:'none'
          })
          return;
        }
        this.wallpaper = [...this.wallpaper,...res.res.wallpaper];
      });
    }
  }
};
</script>
<style lang='scss' scoped>
.album_bg {
  position: relative;
  .album_info {
    position: absolute;
    width: 100%;
    left: 0;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    height: 80rpx;
    align-items: center;
    color: #fff;
    padding: 0 15rpx;
    .album_name {
      font-size: 40rpx;
    }
    .album_btn {
      background-color: $color;
      width: 152rpx;
      height: 60rpx;
      display: flex;
      justify-content: center;
      align-items: center;
      border: 10rpx;
    }
  }
}

/**作者信息 */
.album_author {
  padding: 10rpx;
  .album_author_info {
    padding: 10rpx 0;
    display: flex;
    image {
      width: 50rpx;
    }
    .author_name {
      color: #000;
      margin-left: 15rpx;
    }
  }
}

.album_list {
  display: flex;
  flex-wrap: wrap;
  .album_item {
    width: 33.3%;
    border: 3rpx solid #fff;
    image{
      height: 160rpx;
    }
  }
}
</style>