<template>
  <scroll-view class="cate_scroll_view" scroll-y>
    <view class="home_category">
      <navigator :url="`/pages/imgCategory/index?id=${item.id}`" class="cate_item" v-for="item in category" :key="item.id">
        <image mode="aspectFill" :src="item.cover"></image>
        <view class="cate_name">{{item.name}}</view>
      </navigator>
    </view> 
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      category: []
    };
  },
  mounted() {
    // 修改页面标题
    wx.setNavigationBarTitle({ title: "分类" });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/vertical/category"
      }).then(res => {
        this.category = res.res.category;
      });
    }
  }
};
</script>
<style lang='scss' scoped>
.cate_scroll_view{
  height: calc(100vh - 36px);
}

.home_category {
  display: flex;
  flex-wrap: wrap;
  .cate_item {
    width: 33.3%;
    position: relative;
    border: 5rpx solid #eee;
    image {
      height: 240rpx;
    }

    .cate_name {
      position: absolute;
      width: 100%;
      height: 40rpx;
      left: 0;
      bottom: 0;
      color: #fff;
      // cs3 渐变
      background-image: linear-gradient(to right top,rgba(0,0,0,.2),rgba(0,0,0,0));
      font-size: 40rpx;
      display: flex;
      align-items: center;
      padding-left: 20rpx;
    }
  }
}
</style>