<template>
  <view style="background-color: #F9F9F9;">
    <view class="item" v-for="(item, index) in result" :key="index" @click="toPage(item)">
      <view class="title text-ellipsis">{{ item.title }}</view>
      <view class="content flex-row">
        <view class="info">
          <view class="summary">{{ item.description }}</view>
          <view class="flex-row">
            <text class="date">{{ item.ctime }}</text>
            <text class="views">200 阅读</text>
          </view>
        </view>
        <view class="cover"><image class="img" :src="item.picUrl"></image></view>
      </view>
    </view>
    <view class="bottomLoad" v-show="show">没有更多了</view>
    <zero-back-top :scrollTop="scrollTop"></zero-back-top>
  </view>
</template>

<script>
import { getList } from '@/api/index.js';
export default {
  data() {
    return {
      scrollTop: 0,
      result: [],
      currentPage: 1,
      bottom: true,
      show: false
    };
  },
  onPageScroll(e) {
    this.scrollTop = e.scrollTop;
  },
  onLoad() {
    this.getList();
  },
  onPullDownRefresh() {
    this.currentPage = 1;
    this.result = [];
    if (this.bottom) {
      this.getList();
      uni.stopPullDownRefresh();
    }
  },
  onReachBottom(e) {
    if (this.bottom && !this.show) {
      this.currentPage += 1;
      this.getList();
    }
  },
  methods: {
    getList() {
      this.bottom = false;
      uni.showLoading({
        title: '加载中'
      });
      getList({ currentPage: this.currentPage, pageSize: 10 }).then(res => {
        console.log(res);
        this.bottom = true;
        uni.hideLoading();
        if (+res.returnCode === 0) {
          for (var i = 0; i < res.result.length; i++) {
            this.result.push(res.result[i]);
            if (res.result.length < 10) {
              this.show = true;
            }
          }
        }
      });
    },
    toPage(e) {
      //在起始页面跳转到test.vue页面并传递参数
      uni.navigateTo({
        url: `/pages/consult/detail?url=${e.url}`
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.bottomLoad {
  text-align: center;
  padding-bottom: 20rpx;
}

.item {
  padding: 30rpx;
  margin-bottom: 30rpx;
  background-color: #fff;

  .title {
    font-weight: bold;
    padding-bottom: 30rpx;
    border-bottom: 2rpx solid #f5f5f5;
  }

  .content {
    padding-top: 30rpx;
    align-items: flex-start;

    .info {
      width: calc(100% - 160rpx);

      .summary {
        color: #777;
        height: 80rpx;
        font-size: 24rpx;
        line-height: 1.6;
        margin-bottom: 10rpx;
        @include text-ellipsis(2);
      }

      .date {
        font-size: 24rpx;
        color: #ff5500;
        opacity: 0.6;
      }

      .views {
        color: #999;
        font-size: 24rpx;
      }
    }

    .cover {
      width: 140rpx;
      height: 120rpx;

      .img {
        width: 100%;
        height: 100%;
        border-radius: 4rpx;
      }
    }
  }
}
</style>
