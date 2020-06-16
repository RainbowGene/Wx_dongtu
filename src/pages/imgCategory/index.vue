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
    <scroll-view @scrolltolower="handleToLower" class="cate_view" scroll-y v-if="vertical.length>0">
      <view class="cate_tab_content">
        <view class="cate_item" v-for="item in vertical" :key="item.id">
          <image :src="item.thumb" mode="widthFix"></image>
        </view>
      </view>
    </scroll-view>
  </view>
</template>

<script>
import { uniSegmentedControl } from "@dcloudio/uni-ui";
export default {
  data() {
    return {
      items: [
        { title: "最新", order: "new" },
        { title: "热门", order: "hot" }
      ],
      current: 0,
      params: {
        limit: 30,
        skip: 0,
        order: "new"
      },
      id: -1,
      vertical: [],
      hasMore: true
    };
  },
  onLoad(o) {
    this.id = o.id;
    this.getList();
  },
  methods: {
    // 选项卡单击事件
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }
      else{
        // 点击相同标题
        return;
      }
      this.params.order = this.items[e.currentIndex].order;
      // 重置条件
      this.hasMore = true;
      this.params.skip = 0;
      this.vertical = [];

      this.getList();
    },
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params
      }).then(res => {
        if (res.res.vertical.length === 0){
          this.hasMore = false;
          uni.showToast({
            title:'没有更多数据',
            icon:'none'
          }); 
          return;
        }
        this.vertical = [...this.vertical, ...res.res.vertical];
      });
    },
    handleToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "已经到底了!",
          icon: "none"
        });
      }
    }
  },
  components: {
    uniSegmentedControl
  }
};
</script>
<style lang='scss' scoped>
.home_tab {
  .home_tab_title {
    position: relative;
    .title_inner {
      width: 60%;
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

.cate_view {
  // 屏幕高度 - 标题高度
  height: calc(100vh - 36px);
  .cate_tab_content {
    display: flex;
    flex-wrap: wrap;
    .cate_item {
      width: 33.3%;
      border: 5rpx solid #fff;
    }
  }
}
</style>