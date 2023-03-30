<template>
  <view class="contentsu">
    <view v-show="show">
      <view>
        <view class="card">
          <view style="float: left;">
            <image class="img" src="../../static/images/banner_02.jpg"></image>
          </view>
          <view style="float: right;">
            <view class="introduce">排名：2</view>
            <view class="introduce">姓名：微信用户</view>
          </view>
        </view>
      </view>
      <view style="overflow: hidden;">
        <view style="float: left;margin-left: 40rpx;" class="card1" @click="show = false">
          <view>
            <uni-icons type="compose" size="30"></uni-icons>
            <view>答题</view>
          </view>
        </view>
        <view style="float: right;margin-right: 40rpx;" class="card1" @click="open">
          <view>
            <uni-icons type="list" size="30"></uni-icons>
            <view>排名</view> 
          </view>
        </view>
      </view>
    </view>
    
    <uni-popup ref="popup" type="center" :animation="false">
      <view>
          <div class="rank">
            <div class="title">排名</div>
            <div class="list">
              <div class="item" v-for="(item,index) in rankList" :key="index">
                <div class="rank-num" v-if="index > 2">{{index+1}}</div>
                <div class="rank-num" v-else style="background-color: brown;">{{index+1}}</div>
                <div class="name">{{item.name}}</div>
                <div class="score">{{item.score}}</div>
              </div>
            </div>
          </div>
      </view>
    </uni-popup>

    <view v-show="!show" class="container">
      <view class="exam" v-if="is_have">
        <view class="exam_count">
          {{ index }}
          <text class="ex_count_sum">/ {{ count }}</text>
        </view>
        <view class="exam_content" v-for="(item, index) in detail" :key="index" v-if="item.is_show">
          <view class="exam_title">{{ item.name }}</view>
          <view class="exam_xuan">
            <!-- <text class="exam_item" bindtap="bindExam" wx:for="{{fenlei_detail}}" wx:for-item="j" data-type="{{j.id}}" data-type_name="{{j.name}}" data-id="{{i.id}}">{{j.name}}</text> -->
            <button
              hover-class="exam_item_clover"
              class="exam_item"
              @tap="bindExam({ type: items.id, type_name: items.name, id: item.id })"
              v-for="(items, indexs) in fenlei_detail"
              :key="indexs"
              hover-stay-time="800"
            >
              {{ items.name }}
            </button>
          </view>
        </view>
      </view>
      <canvas canvas-id="share" style="width:375px;height:580px" v-if="!canvasHidden"></canvas>
      <view class="exam_over" v-if="!is_have">
        <view class="exam_over_title">垃圾分类随堂小测试</view>
        <view class="exam_iver_scope">{{ scope }}分</view>
        <view class="exam_over_content">
          <view class="wxam_over_heaser">
            <view>题目</view>
            <view>我的答案</view>
            <view>正确答案</view>
          </view>
          <view class="wxam_over_item" v-for="(item, index) in detail" :key="index">
            <view>{{ item.name }}</view>
            <view v-if="item.type != item.is_type" style="text-decoration:line-through">{{ item.is_type_name }}</view>
            <view v-else>{{ item.is_type_name }}</view>
            <view>{{ item.type_name }}</view>
          </view>
        </view>
      </view>
      <view class="exam_over_btn" v-if="!is_have">
        <!-- #ifdef MP -->
        <button class="exam_over_btns" @tap="bindImage">
          <image src="../../static/images/download.png" mode="widthFix"></image>
          保存成绩单
        </button>
        <button @tap="show = true" class="exam_over_btns">
          <image src="../../static/images/restart-line.png" mode="widthFix"></image>
          返回
        </button>
        <button open-type="share" class="exam_over_btns">
          <image src="../../static/images/guide_tag.png" mode="widthFix"></image>
          考考别人
        </button>
        <!-- #endif -->
        <!-- #ifndef MP -->
        <button @tap="bindAgain" class="exam_over_btns">
          <image src="../../static/images/restart-line.png" mode="widthFix"></image>
          再考一次
        </button>
        <!-- #endif -->
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      show: true,
      canvasHidden: true,
      detail: [],
      count: 10,
      index: '01',
      scope: 0,
      is_have: true,
      share_title: '垃圾分类知多少？快来测一测就知道了~',
      rankList: [
        { name: '张三', score: 90 },
        { name: '李四', score: 85 },
        { name: '王五', score: 80 },
        { name: '赵六', score: 75 },
        { name: '钱七', score: 70 },
      ],
      fenlei_detail: [
        {
          id: '1',
          name: '其他垃圾'
        },
        {
          id: '2',
          name: '餐厨垃圾'
        },
        {
          id: '3',
          name: '可回收物'
        },
        {
          id: '4',
          name: '有害垃圾'
        }
      ]
    };
  },
  onShow: function() {
    // if (!uni.getStorageSync('city_fenlei')) {
    // 	app.userInfoReadyCallback = rubbish_list => {
    // 		this.downLoadQuestion();
    // 	}
    // } else {
    this.downLoadQuestion();
    // }
  },
  // onShareAppMessage(res) {
  // 	if (res.from === 'button') {
  // 		// 来自页面内转发按钮
  // 	}
  // 	return {
  // 		title: this.share_title,
  // 		path: '/pages/examination/examination',
  // 		imageUrl: '/static/images/examp_share.jpg'
  // 	}
  // },
  methods: {
    open() {
      this.$refs.popup.open('popup')
    },
    //加载题目数据
    downLoadQuestion: function() {
      uni.showLoading({
        title: '加载中'
      });
      this.detail = [
        {
          id: 1,
          is_show: true,
          is_type: 1, // 正确答案
          name: '啤酒瓶', // 问题
          type: 1 // 答案
        },
        {
          id: 2,
          is_show: false,
          is_type: '',
          name: '修正液',
          type: 1
        },
        {
          id: 3,
          is_show: false,
          is_type: '',
          name: '金属瓶盖',
          type: 1
        },
        {
          id: 4,
          is_show: false,
          is_type: '',
          name: '衣服吊牌',
          type: 1
        },
        {
          id: 5,
          is_show: false,
          is_type: '',
          name: '无纺布袋',
          type: 1
        },
        {
          id: 6,
          is_show: false,
          is_type: '',
          name: '刀',
          type: 1
        },
        {
          id: 7,
          is_show: false,
          is_type: '',
          name: '塑料袋',
          type: 1
        },
        {
          id: 8,
          is_show: false,
          is_type: '',
          name: '烟蒂',
          type: 1
        },
        {
          id: 9,
          is_show: false,
          is_type: '',
          name: '剩饭',
          type: 1
        },
        {
          id: 10,
          is_show: false,
          is_type: '',
          name: '木板',
          type: 1
        }
      ];
      this.count = 10;
      this.scope = 0;
      uni.hideLoading();
    },
    bindAgain: function() {
      this.is_have = true;
      this.index = '01';
      this.downLoadQuestion();
    },
    bindExam: function(e) {
      var type = e.type;
      var id = e.id;
      var is_type_name = e.type_name;
      var scopes = this.scope;
      //判断答案是否正确 答对加分 并将答案写入
      if (type == this.detail[id - 1].type) {
        this.scope = Number(scopes) + Number(10);
        this.share_title = '我在垃圾分类随堂小测试中获得了' + (Number(scopes) + Number(10)) + '分，你也来试试吧~';
      }
      var detail_list = [];
      var is_haves = true;
      //当是最后一题时，出结果页
      var number = '';
      if (id == this.detail.length) {
        this.detail[id - 1].is_show = false;
        this.detail[id - 1].is_type = type;
        detail_list = this.detail;
        is_haves = false;
        number = this.detail.length;
      } else {
        //跳下一题
        this.detail[id - 1].is_show = false;
        this.detail[id - 1].is_type = type;
        this.detail[id].is_show = true;
        detail_list = this.detail;
        number = Number(id) + Number(1);
        if (number < 10) {
          number = '0' + number;
        }
        is_haves = true;
      }
      if (id == this.detail.length) {
        for (var j = 0; j < detail_list.length; j++) {
          detail_list[j].type_name = this.fenlei_detail[detail_list[j].type - 1].name;
          detail_list[j].is_type_name = this.fenlei_detail[detail_list[j].is_type - 1].name;
          number = 11;
        }
      }
      this.detail = detail_list;
      this.index = number;
      this.is_have = is_haves;
    },
    bindImage: function() {
      var that = this;
      const ctx = uni.createCanvasContext('share');
      //背景图
      var bgImgPath = '../../static/images/share_bg.jpg';
      //分数
      var fenshu = this.scope + '分';
      //姓名
      var nickname = '就不告诉你';
      //答案
      var detail_list = this.detail;
      //写入背景图
      ctx.drawImage(bgImgPath, 0, 0, 375, 568);
      //写入小程序码
      ctx.drawImage('../../static/images/gh_500a9fb63489_430.jpg', 30, 460, 100, 100);
      //定义姓名和分数部分字体大小和颜色及导入数据
      ctx.setFontSize(23);
      ctx.setFillStyle('red');
      ctx.fillText(fenshu, 220, 123);
      ctx.setFillStyle('#000');
      ctx.fillText(nickname, 220, 85);
      //题目部分
      ctx.setFontSize(13);
      var d_y = 185;
      for (var i = 0; i < detail_list.length; i++) {
        //写入题目
        ctx.fillText(detail_list[i].name, 10, d_y);
        //写入我的答案
        if (detail_list[i].type == detail_list[i].is_type) {
          ctx.setFillStyle('green');
        } else {
          ctx.setFillStyle('red');
        }
        ctx.fillText(detail_list[i].is_type_name, 230, d_y);
        ctx.setFillStyle('#000');
        //写入正确答案
        ctx.fillText(detail_list[i].type_name, 305, d_y);
        d_y = Number(d_y) + Number(27);
      }
      uni.showToast({
        title: '成绩单生成中...',
        icon: 'loading',
        duration: 1000
      });
      ctx.draw(false, function() {
        uni.canvasToTempFilePath({
          x: 0,
          y: 0,
          width: 750,
          height: 1136,
          destWidth: 750,
          destHeight: 1136,
          fileType: 'jpg',
          quality: '1',
          canvasId: 'share',
          success: function(res) {
            uni.saveImageToPhotosAlbum({
              filePath: res.tempFilePath,
              success: function(event) {
                uni.showToast({
                  title: '保存成功'
                });
              }
            });
          }
        });
      });
    }
  }
};
</script>

<style>
page {
  background-color: #f7f7f7;
}

.exam {
  width: 100%;
}

.exam_count {
  margin: 20rpx;
  font-weight: bold;
}

.ex_count_sum {
  color: #dedede;
}

.exam_content {
  text-align: center;
}

.exam_title {
  font-size: 40rpx;
  font-weight: bold;
  letter-spacing: 2rpx;
}

.card {
  margin: 40upx;
  background-color: #ccc;
  box-shadow: 2px 2px 5px #000;
  border-radius: 20upx;
  overflow: hidden;
}

.card1 {
  width: 40%;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #FFF;
  box-shadow: 2px 2px 5px #76DAB5;
  /* text-align: center;
  padding-top: 20px; */
}

.introduce {
  margin: 50rpx;
  font-size: 32rpx;
}

.img {
  width: 200upx;
  height: 200upx;
  border-radius: 100upx;
  margin: 20upx;
}

.exam_xuan {
  margin-top: 30rpx;
  display: flex;
  flex-direction: column;
}

.exam_item {
  border: 1rpx solid #dedede;
  width: 300rpx;
  padding: 20rpx;
  border-radius: 50rpx;
  margin: 20rpx auto;
  background-color: #fff;
  font-size: 30rpx;
  line-height: 1;
  box-sizing: none;
}

.exam_xuan button::after {
  width: 0 !important;
  height: 0 !important;
}

.exam_item_clover {
  border: 1rpx solid #1296db;
  background-color: #1296db;
}

.exam_over {
  width: 90%;
  margin: 20rpx auto;
  border-radius: 20rpx;
  box-shadow: 1px 1px 3px #7c7272, 1px -1px 3px #dedede, -1px 1px 3px #dedede, -1px -1px 3px #dedede;
  -moz-box-shadow: 1px 1px 3px #7c7272, 1px -1px 3px #dedede, -1px 1px 3px #dedede, -1px -1px 3px #dedede;
  -webkit-box-shadow: 1px 1px 3px #7c7272, 1px -1px 3px #dedede, -1px 1px 3px #dedede, -1px -1px 3px #dedede;
}

.exam_over_title,
.exam_iver_scope {
  text-align: center;
  margin: 20rpx auto;
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  font-weight: bold;
  letter-spacing: 2rpx;
}

.exam_iver_scope {
  color: red;
  font-size: 40rpx;
}

.exam_over_content {
  display: flex;
  flex-direction: column;
  margin: 20rpx auto;
  font-size: 25rpx;
}

.wxam_over_heaser {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  padding: 10rpx 20rpx;
  background-color: #f0f0f0;
  font-weight: bold;
}

.wxam_over_heaser view:first-child {
  width: 60%;
  text-align: left;
}

.wxam_over_heaser view {
  width: 20%;
  text-align: center;
  border-right: 1rpx solid #7c7272;
}

.wxam_over_heaser view:last-child {
  border: none;
}

.wxam_over_item {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  padding: 10rpx 20rpx;
}

.wxam_over_item view:first-child {
  width: 60%;
  text-align: left;
}

.wxam_over_item view {
  width: 20%;
  text-align: center;
  border-right: 1rpx solid #7c7272;
}

.wxam_over_item view:last-child {
  border: none;
}

.wxam_over_item:nth-child(odd) {
  background-color: #f0f0f0;
}

.exam_over_btn {
  width: 90%;
  margin: 20rpx auto;
  display: flex;
  flex-direction: row;
}

.exam_over_btns {
  border-radius: 35rpx;
  font-size: 25rpx;
  letter-spacing: 3rpx;
  background-color: rgba(255, 255, 255, 0.3);
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  box-shadow: 1px 1px 3px #7c7272, 1px -1px 3px #dedede, -1px 1px 3px #dedede, -1px -1px 3px #dedede;
  -moz-box-shadow: 1px 1px 3px #7c7272, 1px -1px 3px #dedede, -1px 1px 3px #dedede, -1px -1px 3px #dedede;
  -webkit-box-shadow: 1px 1px 3px #7c7272, 1px -1px 3px #dedede, -1px 1px 3px #dedede, -1px -1px 3px #dedede;
}

.exam_over_btns::after {
  border: none;
}

.exam_over_btns image {
  width: 30rpx;
  margin-right: 20rpx;
}

.rank {
  padding: 20px;
  width: 600rpx;
  background-color: #fff;
}
.title {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 10px;
}
.list {
  display: flex;
  flex-direction: column;
}
.item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
.rank-num {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  background-color: #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #fff;
  margin-right: 10px;
}
.name {
  flex: 1;
}
.score {
  font-weight: bold;
}
</style>
