<template>
  <view>
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
          <image :src="item.thumb" />
        </view>
      </view>
    </view>
    <!-- 月份结束 -->
  </view>
</template>
<script>
import moment from "moment";
export default {
  data() {
    return {
      //
      recommends: [],
      months: []
    };
  },
  mounted() {
    this.request({
      url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
      data: {
        //要获取几条
        limit: 30,
        //关键字
        order: "hot",
        //哟跳过几条
        skip: 0
      }
    }).then(result => {
      console.log(result);
      //推荐模块
      this.recommends = result.res.homepage[1].items;
      //月份模块
      this.months = result.res.homepage[2];
      //将时间戳改为18号/月  moment.js
      this.months.MM = moment(this.months.stime).format("MM");
      this.months.DD = moment(this.months.stime).format("DD");
      console.log(this.months);
    });
  }
};
</script>

<style lang='scss'>
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
}
</style>