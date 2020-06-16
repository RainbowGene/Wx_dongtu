<template>
  <!-- 有数据才显示数据 -->
  <scroll-view
    @scrolltolower="handleToLower"
    class="recommends_view"
    scroll-y
    v-if="recommends.length>0"
  >
    <!-- 推荐开始 -->
    <view class="recommend_wrap">
      <navigator :url="`/pages/album/index?id=${item.target}`" class="recommend_item" v-for="item in recommends" :key="item.id">
        <image mode="widthFix" :src="item.thumb"></image>
      </navigator>
    </view>
    <!-- 推荐结束 -->
    <!-- 月份开始 -->
    <view class="months_wrap">
      <view class="months_title">
        <view class="months_title_info">
          <view class="months_info">
            <text>{{monthes.DD}}/</text>
            {{monthes.MM}} 月
          </view>
          <view class="months_text">{{monthes.title}}</view>
        </view>
        <view class="months_title_more">更多></view>
      </view>
      <view class="months_content">
        <view class="months_item" v-for="(item,index) in monthes.items" :key="item.id">
          <go-detail :list="monthes.items" :index="index">
            <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)"></image>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 月份结束 -->

    <!-- 热门开始 -->
    <view class="hots_wrap">
      <view class="hots_title">
        <text>热门</text>
      </view>
      <view class="hots_content">
        <view class="hots_item" v-for="(item,index) in hots" :key="item.id">
          <go-detail :list="hots" :index="index">
            <image mode="widthFix" :src="item.thumb"></image>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 热门结束 -->
  </scroll-view>
</template>

<script>
import moment from "moment";
import goDetail from '@/components/goDetail'
export default {
  data() {
    return {
      // 推荐列表
      recommends: [],
      monthes: {},
      hots: [],
      // 请求参数
      params: {
        limit: 30, //获取多少条
        order: "hot", // 关键字
        skip: 0 //跳过多少条
      },
      hasMore:true  //是否还有更多数据
    };
  },
  components: {
    goDetail
  },
  methods: {
    getList() {
      // 封装的request
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.params
      })
        .then(res => {
          /** 返回
           *  res.homepage[1].items :  推荐列表
           *  res.homepage[2]   月份列表
           *  res.vertical  热门列表
           *  */
          // 判断有没有更多数据
          if(res.res.vertical.length===0) {
            uni.showToast({
              title:'已经到底了！',
              icon:'none'
            })
            return this.hasMore = false;
          }

          // 推荐、月份列表渲染
          if (this.recommends.length === 0) {
            //第一次请求
            this.recommends = res.res.homepage[1].items;
            this.monthes = res.res.homepage[2];
            // 时间戳变换 : moment.js
            this.monthes.MM = moment(this.monthes.stime).format("MM");
            this.monthes.DD = moment(this.monthes.stime).format("DD");
          }
          // 数组拼接
          this.hots = [...this.hots, ...res.res.vertical];
        })
        .catch(err => {
          console.log(err);
        });
    },
    // 触底事件
    handleToLower() {
      /**
       * 触底一次：1, skip+=limit 2,重发请求  3,数组叠加
       */
      if(this.hasMore){
        this.params.skip += this.params.limit;
        this.getList();
      }
      else{
        uni.showToast({
          title:'已经到底了!',
          icon:'none'
        })
      }
    }
  },
  mounted() {
    this.getList();
  }
};
</script>
<style lang='scss' scoped>
.recommends_view {
  // 屏幕高度 - 标题高度
  height: calc(100vh - 36px);
}
.recommend_wrap {
  // flex 布局
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    width: 50%;
    border: 5rpx solid #fff;
  }
}

.months_wrap {
  .months_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .months_title_info {
      display: flex;
      color: $color;
      font-size: 30rpx;
      font-weight: 600;
      .months_info {
        text {
          font-size: 36rpx;
        }
      }

      .months_text {
        font-size: 34rpx;
        color: #666;
        margin-left: 30rpx;
      }
    }

    .months_title_more {
      font-size: 24rpx;
      color: $color;
    }
  }

  .months_content {
    display: flex;
    flex-wrap: wrap;
    .months_item {
      width: 33.3%;
      border: 5rpx solid #fff;
    }
  }
}

.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {
      border-left: 5rpx solid $color;
      padding-left: 20rpx;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hots_item {
      width: 33.3%;
      border: 5rpx solid #fff;
    }
  }
}
</style>