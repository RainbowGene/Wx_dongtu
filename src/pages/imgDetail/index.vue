<template>
  <view>
    <!-- 用户信息 开始 -->
    <view class="user_info">
      <view class="user_icon">
        <image :src="imgDetail.user.avatar" mode="widthFix"></image>
      </view>
      <view class="user_desc">
        <view class="user_name">{{imgDetail.user.name}}</view>
        <view class="user_time">{{imgDetail.cnTime}}</view>
      </view>
    </view>
    <!-- 用户信息 结束 -->
    <!-- 大图 -->
    <view class="high_img">
      <swiper-action @swiperAction="handleSwiperAction">
        <image :src="imgDetail.thumb" mode="widthFix"></image>
      </swiper-action>
    </view>
    <!-- 大图 结束 -->
    <!-- 点赞 -->
    <view class="user_rank">
      <view class="rank">
        <!-- <image mode="widthFix" src="@/static/icon/sousuo.png"></image> -->
        <text class="icondianzan">{{imgDetail.rank}}</text>
      </view>
      <view class="user_collect">
        <text class="iconshoucang">收藏</text>
      </view>
    </view>
    <!-- 点赞 结束 -->
    <!-- 专辑 -->
    <view class="album_wrap" v-if="album.length">
      <view class="album_title">相关</view>
      <view class="album_list">
        <view class="album_item" v-for="item in album" :key="item.id">
          <view class="album_cover">
            <image :src="item.cover" mode="aspectFill"></image>
          </view>
          <view class="album_info">
            <view class="album_info_text">专辑</view>
            <view class="album_name">{{item.name}}</view>
            <view class="icon_gengduo">》</view>
          </view>
        </view>
      </view>
    </view>
    <!-- 专辑 结束 -->
    <!-- 最热评论 -->
    <view class="comment hot" v-if="hot.length">
      <view class="comment_title">
        <text class="iconfont iconthot1"></text>
        <text class="comment_text">热门评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in hot" :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <view class="user_icon">
              <image :src="item.user.avatar" mode="widthFix"></image>
            </view>
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <view class="user_badge">
              <image v-for="item2 in item" :key="item2.icon" :src="item2.icon"></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="dianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最热评论 结束 -->
    <!-- 最新评论 -->
    <view class="comment hot" v-if="comment.length">
      <view class="comment_title">
        <text class="iconfont iconnew"></text>
        <text class="comment_text">最新评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item" v-for="item in comment" :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <view class="user_icon">
              <image :src="item.user.avatar" mode="widthFix"></image>
            </view>
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <view class="user_badge">
              <image v-for="item2 in item" :key="item2.icon" :src="item2.icon"></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="dianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最新评论 结束 -->
    <!-- 下载 -->
    <view class="download">
      <view class="download_btn" @click="handleDownload">下载图片</view>
    </view>
    <!-- 下载 结束 -->
  </view>
</template>

<script>
import moment from "moment";
import swiperAction from "@/components/swiperAction";
// 设置中文
moment.locale("zh-cn");
export default {
  data() {
    return {
      // 图片信息对象
      imgDetail: {},
      album: [],
      comment: [],
      hot: [],
      id: -1,
      // 图片索引
      imgIndex: 0,
      imgList: []
    };
  },
  components: {
    swiperAction
  },
  onLoad() {
    const { imgList, imgIndex } = getApp().globalData;
    this.imgIndex = imgIndex;
    this.imgList = imgList;
    this.getData();
  },
  methods: {
    getData() {
      this.imgDetail = this.imgList[this.imgIndex];

      // 时间戳
      this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow();
      this.id = this.imgDetail.id;

      this.getList();
    },
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${this.id}/comment`
      })
        .then(res => {
          this.album = res.res.album;

          // 时间戳
          res.res.hot.forEach(
            v => (v.cnTime = moment(v.atime * 1000).fromNow())
          );
          res.res.comment.forEach(
            v => (v.cnTime = moment(v.atime * 1000).fromNow())
          );
          this.hot = res.res.hot;
          this.comment = res.res.comment;
          //console.log(res);
        })
        .catch(err => {
          return uni.showToast({
            title: "详情数据获取失败！",
            icon: "none"
          });
        });
    },
    // 滑动切换图片
    handleSwiperAction(e) {
      const { dire } = e;
      if (dire === "l" && this.imgIndex < this.imgList.length - 1) {
        this.imgIndex++;
        this.getData();
      } else if (dire === "r" && this.imgIndex > 0) {
        this.imgIndex--;
        this.getData();
      } else {
        uni.showToast({
          title: "没了",
          icon: "none"
        });
      }
    },
    // 图片下载
    async handleDownload() {
      await uni.showLoading({
        title:"下载中..."
      })
      // 将远程文件下载到小程序内存中
      const res1 = await uni.downloadFile({ url: this.imgDetail.img });
      const { tempFilePath } = res1[1];
      // 将内存中的图片资源下载到本地（小程序端直接下载到相册）
      const res2 = await uni.saveImageToPhotosAlbum({ filePath: tempFilePath });
      uni.hideLoading();
      uni.showToast({
        title: "下载成功！"
      });
    }
  }
};
</script>
<style lang='scss' scoped>
.user_info {
  display: flex;
  padding: 20rpx;
  .user_icon {
    padding: 0 20rpx;
    image {
      width: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
      color: black;
      font-weight: 600;
    }

    .user_time {
      color: #ccc;
      font-size: 24rpx;
      padding: 10rpx 0;
    }
  }
}

.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 5rpx solid #eee;
  .rank {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    image {
      width: 44rpx;
      height: 44rpx;
      padding: 0 8rpx;
    }
  }

  .user_collect {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    image {
      width: 44rpx;
      height: 44rpx;
      padding: 0 8rpx;
    }
  }
}

.high_img {
  image {
    border-bottom: 3rpx solid #eee;
  }
}

/* 专辑 */
.album_wrap {
  padding: 20rpx;
  .album_title {
    padding: 10rpx 0;
  }

  .album_list {
    .album_item {
      display: flex;
      padding: 10rpx 0;
      border-bottom: 10rpx solid #ddd;
      .album_cover {
        flex: 1;
        image {
          width: 180rpx;
          height: 180rpx;
        }
      }

      .album_info {
        flex: 3;
        padding-left: 20rpx;
        position: relative;
        .album_info_text {
          width: 100rpx;
          height: 50rpx;
          background-color: $color;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color: #888;
        }
        .icon_gengduo {
          font-size: 40rpx;
          position: absolute;
          top: 50%;
          transform: translateY(-50%);
          right: 10%;
        }
      }
    }
  }
}

.hot {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: $color;
      font-size: 40rpx;
    }

    .comment_text {
      font-weight: 600;
      font-size: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }

  .comment_list {
    .comment_item {
      border-bottom: 15rpx solid #eee;
      // 用户信息
      .comment_user {
        display: flex;
        padding: 20rpx 0;
        height: 140rpx;
        .user_icon {
          width: 15%;
          display: flex;
          justify-content: center;
          align-items: center;
          image {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;
          .user_nickname {
            color: #777;
          }

          .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 5rpx;
          }
        }

        .user_badge {
          image {
            width: 40rpx;
            height: 40rpx;
            display: inline-block;
          }
        }
      }

      .comment_desc {
        display: flex;
        padding: 30rpx 0;
        .comment_content {
          flex: 1;
          padding-left: 15%;
          color: #000;
        }

        .comment_like {
          text-align: right;
        }
      }
    }
  }
}

// 下载按钮
.download {
  height: 120rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  .download_btn {
    width: 90%;
    height: 75%;
    background-color: $color;
    color: #eee;
    font-size: 50rpx;
    font-weight: 600;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
</style>