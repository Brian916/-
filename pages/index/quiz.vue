<template>
  <view>
    <uni-section title="自定义icon" type="line" :radius="100">
      <uni-search-bar placeholder="自定义" @confirm.stop="search" bgColor="#EEEEEE" @cancel="search" cancel-text="搜索">
        <uni-icons slot="searchIcon" color="#999999" size="18" type="camera" @click.stop="photograph" />
      </uni-search-bar>
    </uni-section>
    <view style="padding: 10px;" v-show="show">
      <view v-show="list.type">
        <view class="uni-box"><uni-title class="h4" type="h4" title="垃圾种类"></uni-title></view>
        <view>
          <text class="uni-text">{{ type[list.type] }}</text>
        </view>
      </view>
      <view v-show="list.category">
        <view class="uni-box"><uni-title class="h4" type="h4" title="垃圾种类"></uni-title></view>
        <view>
          <text class="uni-text">{{ list.category }}</text>
        </view>
      </view>
      <view v-show="list.rubbish">
        <view class="uni-box"><uni-title class="h4" type="h4" title="分类解释"></uni-title></view>
        <view>
          <text class="uni-text">{{ list.rubbish }}</text>
        </view>
      </view>
      <view v-show="list.explain">
        <view class="uni-box"><uni-title class="h4" type="h4" title="分类解释"></uni-title></view>
        <view>
          <text class="uni-text">{{ list.explain }}</text>
        </view>
      </view>
      <view v-show="list.contain">
        <view class="uni-box"><uni-title class="h4" type="h4" title="包含类型"></uni-title></view>
        <view>
          <text class="uni-text">{{ list.contain }}</text>
        </view>
      </view>
      <view v-show="list.tip">
        <view class="uni-box"><uni-title class="h4" type="h4" title="投放提示"></uni-title></view>
        <view>
          <text class="uni-text">{{ list.tip }}</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import { upload, img } from '@/api/index.js';
import { BASEURL } from '@/common/utils.js';
export default {
  data() {
    return {
      type: ['可回收垃圾', '有害垃圾', '厨余垃圾', '其他垃圾'],
      list: {},
      show: false
    };
  },
  methods: {
    search(e) {
      uni.request({
        url: 'https://apis.tianapi.com/lajifenlei/index', //仅为示例，并非真实接口地址。
        data: {
          key: 'db662cd3e0fde863fccb7409f8388b38',
          word: e.value,
          mode: 1
        },
        success: res => {
          if (res.data.code === 200) {
            this.list = res.data.result.list[0];
            this.show = true;
          } else {
            this.show = false;
          }
        }
      });
    },
    cancel(e) {
      console.log(e);
    },
    photograph() {
      var that = this
      uni.chooseImage({
        count: 1, //默认9
        success: function(res) {
          uni.showLoading({
            title: '加载中'
          });
          uni.uploadFile({
            url: `${BASEURL}/upload`,
            filePath: res.tempFilePaths[0],
            header: {
              'Content-Type': 'multipart/form-data'
            },
            name: 'file', // 后端接收的文件名
            complete: res => {
              var ress = JSON.parse(res.data);
              if (+ress.returnCode === 1) {
                img({ imgUrl: ress.result }).then(res => {
                  that.list = res
                  that.show = true;
                  uni.hideLoading();
                });
              } else {
                uni.showToast({
                  title: '接口错误，请稍后重试',
                  duration: 1500,
                  mask: false,
                  icon: 'none'
                });
                uni.hideLoading();
              }
            }
          });
        }
      });
    }
  }
};
</script>

<style></style>
