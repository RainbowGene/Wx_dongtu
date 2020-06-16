<template>
  <view>
    <view class="home_tab">
      <view class="home_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="items.map(v=>v.title)"
            @clickItem="onClickItem"
            style-type="text"
            active-color="#d4237a"
          ></uni-segmented-control>
        </view>
        <view class="iconsearch">
          <image mode="widthFix" src="@/static/icon/sousuo0.png"></image>
        </view>
      </view>
    </view>
    <view class="home_tab_content">
      <view v-if="current<4">
        <video-main :urlobj="{url:items[current].url,params:items[current].params}"></video-main>
      </view>
      <view v-if="current === 4">
        <video-category></video-category>
      </view>
    </view>
  </view>
</template>

<script>
import { uniSegmentedControl } from "@dcloudio/uni-ui";
import videoMain from "./video-main";
import videoCategory from "./video-category";
export default {
  data() {
    return {
      items: [
        {
          title: "推荐",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/featured",
          params: { limit: 30, skip: 0, order: "hot" }
        },
        {
          title: "娱乐",
          url:
            "http://157.122.54.189:9088/videoimg/v1/videowp/category/59b25abbe7bce76bc834198a",
          params: { limit: 30, skip: 0, order: "new" }
        },
        {
          title: "最新",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/videowp",
          params: { limit: 30, skip: 0, order: "new" }
        },
        {
          title: "热门",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/videowp",
          params: { limit: 30, skip: 0, order: "hot" }
        },
        {
          title: "分类",
          url: "http://157.122.54.189:9088/videoimg/v1/videowp/category",
          params: {}
        }
      ],
      current: 0
    };
  },
  methods: {
    // 选项卡单击事件
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      } else {
        // 点击相同标题
        return;
      }
    }
  },
  components: {
    uniSegmentedControl,
    videoCategory,
    videoMain
  }
};
</script>
<style lang='scss' scoped>
.home_tab {
  .home_tab_title {
    position: relative;
    .title_inner {
      width: 75%;
      margin: 0 auto;
    }
    .iconsearch {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 5%;
      image {
        width: 44rpx;
        height: 44rpx;
        padding: 0 8rpx;
      }
    }
  }
}
</style>