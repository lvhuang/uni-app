<template>
  <!-- 初次进页面，需要请求数据，页面存在刷新卡顿数据一时半会请求不回来，因此要加v-if="recommends.length>0" -->
  <scroll-view
    class="recommends_view"
    scroll-y
    @scrolltolower="scrollTolower"
    v-if="recommends.length>0"
  >
    <!-- 推荐开始 -->
    <view class="recommend_wrap">
      <view class="recommend_item" v-for="item in recommends" :key="item.id">
        <!-- 图片高自适应mode='widthFix' -->
        <image mode="widthFix" :src="item.thumb" />
      </view>
    </view>
    <!-- 推荐结束 -->
    <!-- 月份开始 -->
    <view class="month_wrap">
      <view class="month_title">
        <view class="month_title_info">
          <view class="month_info">
            <text>{{months.DD}} /</text>
            {{months.MM}}月
          </view>
          <view class="month_text">{{months.title}}</view>
        </view>
        <view class="month_title_more">更多</view>
      </view>
      <view class="month_content">
        <view class="month_item" v-for="item in months.items" :key="item.id">
          <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)" />
        </view>
      </view>
    </view>
    <!-- 月份结束 -->
    <!-- 热门开始 -->
    <view class="host_wrap">
      <view class="host_title">
        <text>热门</text>
      </view>
      <view class="host_content">
        <view class="host_item" v-for="item in host" :key="item.id">
          <image mode="widthFix" :src="item.thumb" />
        </view>
      </view>
    </view>
    <!-- 热门结束 -->
  </scroll-view>
</template>
<script>
import moment from "moment";
export default {
  data() {
    return {
      //
      recommends: [],
      months: [],
      host: [],
      params: {
        //要获取几条
        limit: 30,
        //关键字
        order: "hot",
        //哟跳过几条
        skip: 0
      },
      hasMore: true
    };
  },
  mounted() {
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
        data: this.params
      }).then(result => {
        if (result.res.vertical.length === 0) {
          this.hasMore = false;
          return;
        }
        if (this.recommends.length === 0) {
          //第一次发请求获取页面固定资源，只多次请求分页
          //推荐模块
          this.recommends = result.res.homepage[1].items;
          //月份模块
          this.months = result.res.homepage[2];
          //将时间戳改为18号/月  moment.js
          this.months.MM = moment(this.months.stime).format("MM");
          this.months.DD = moment(this.months.stime).format("DD");
        }

        //获取热门   分页数据叠加
        this.host = [...this.host, ...result.res.vertical];
      });
    },
    //分页处理函数
    scrollTolower() {
      //改变请求数据的参数skip
      //请求数据
      //叠加数据。把之前请求的数据也算在里面
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        //弹窗提示用户没有数据了
        uni.showToast({
          title: "没有数据了",
          icon: "none"
        });
      }
    }
  }
};
</script>

<style lang='scss'>
.recommends_view {
  // height=屏幕高度-头部标题高度
  height: calc(100vh - 36px);
}
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    width: 50%;
    border: 5rpx solid #fff;
  }
}

.month_wrap {
  .month_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .month_title_info {
      color: #f0f;
      font-size: 30rpx;
      font-weight: 600;
      display: flex;
      .month_info {
        text {
          font-size: 36rpx;
        }
      }
      .month_text {
        font-size: 36rpx;
        color: #666;
        margin-left: 15rpx;
      }
    }
  }
  .month_content {
    display: flex;
    flex-wrap: wrap;
    .month_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}

.host_wrap {
  .host_title {
    padding: 20rpx;
    text {
      border-left: 20rpx solid #f0f;
      padding-left: 20rpx;
      font-size: 34rpx;
      font-weight: 600;
    }
  }

  .host_content {
    display: flex;
    flex-wrap: wrap;
    .host_item {
      width: 33.33%;
      border: 5rpx solid #fff;
    }
  }
}
</style>