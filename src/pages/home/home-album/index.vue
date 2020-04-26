<template>
  <scroll-view class="album_scroll_view" @scrolltolower="handleTolower" scroll-y>
    <!-- 轮播图开始 -->
    <!-- 小程序轮播图swiper
    1.自动轮播：autoplay
    2.指示器：indicator-dots
    3.衔接轮播：circular

    4.swiper默认高度150px
    5.image默认宽度320px=>基本样式中已重置100%
      默认高度240px
      当屏幕宽高发生改变时图片被拉变形
      希望容器的高度通过内容撑开，swiper不支持
      所以计算图片的宽高比例、吧swiper的高度变成等比的图片高度  图片宽高比640/284=2.3
    -->
    <view class="album_swiper">
      <swiper autoplay indicator-dots circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb" />
        </swiper-item>
      </swiper>
    </view>
    <!-- 轮播图结束 -->
    <!-- 列表开始 -->
    <view class="album_list">
      <!-- navigator实现超链接 -->
      <navigator
        class="album_item"
        v-for="item in album"
        :key="item.id"
        :url="`/pages/album/index?id=${item.id}`"
      >
        <!-- 图片区域 -->
        <view class="album_img">
          <image mode="aspectFill" :src="item.cover" />
        </view>
        <!-- 文字描述区域 -->
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
  </scroll-view>
</template>
<script>
export default {
  data() {
    return {
      banner: [],
      album: [],
      params: {
        limit: 30,
        order: "new",
        skip: 0
      },
      hasMore: true
    };
  },
  mounted() {
    uni.setNavigationBarTitle({ title: "专辑" });
    this.getList();
  },
  methods: {
    //获取专辑数据
    getList() {
      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params
      }).then(result => {
        if (this.banner.length === 0) {
          this.banner = result.res.banner;
        }
        if (result.res.album === 0) {
          this.hasMore = false;
          return;
        }
        this.album = [...this.album, ...result.res.album];
      });
    },
    handleTolower() {
      // 分页函数 改变请求参数中的分页     请求数据    添加到页面
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
.album_scroll_view {
  height: calc(100vh - 36px);
}
.album_swiper {
  swiper {
    //750calc(750rpx / 2.3)
    height: 326.1rpx;
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
    border-bottom: 1rpx solid #ccc;
    .album_img {
      padding: 10rpx;
      flex: 1;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }
    .album_info {
      padding: 0 10rpx;
      flex: 2;
      // 让文字折叠一行并且不成开容易
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rpx 0;
      }
      .album_desc {
        font-size: 24rpx;
        padding: 10rpx 0;
        // 让文字折叠一行
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }
      .album_btn {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: #f0f;
          border: 1rpx solid #f0f;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>