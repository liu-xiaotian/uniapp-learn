<template>
  <view class="container">
    <view class="layout">
      <view class="box" v-for="item in pets" :key="item._id">
        <view class="pic">
          <image
            lazy-load
            :src="item.url"
            mode="widthFix"
            @click="onPreview"
          ></image>
        </view>
        <view class="text"> {{ item.content }} </view>
        <view class="author">--- {{ item.author }}</view>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref } from "vue";
const pets = ref([]);

const onPreview = (e) => {
  console.log(e);
  const urls = pets.value.map((item) => item.url);
  uni.previewImage({
    current: e,
    urls,
  });
};

// 发送网络请求
function network() {
  // uni.showLoading();
  uni.showNavigationBarLoading();
  uni
    .request({
      url: "https://tea.qingnian8.com/tools/petShow",
      data: {
        size: 10,
      },
    })
    .then((res) => {
      if (res.data.errCode === 0) {
        pets.value = res.data.data;
      } else if (res.data.errCode === 400) {
        uni.showToast({
          title: res.data.errMsg,
          icon: "none",
        });
      }
    })
    .catch((err) => {
      uni.showToast({
        title: "网络请求失败,请稍后再试",
        icon: "none",
      });
    })
    .finally(() => {
      // uni.hideLoading();
      uni.hideNavigationBarLoading();
    });
}

network();
</script>

<style scoped>
.container {
  .layout {
    padding: 50rpx;
    .box {
      margin-bottom: 60rpx;
      box-shadow: 0 10rpx 30rpx rgba(0, 0, 0, 0.08);
      border-radius: 15rpx;
      overflow: hidden;
      .pic {
        image {
          width: 100%;
        }
      }
      .text {
        padding: 30rpx;
        color: #333;
        font-size: 36rpx;
      }
      .author {
        padding: 0 30rpx 30rpx;
        text-align: right;
        color: #888;
        font-size: 28rpx;
      }
    }
  }
}
</style>
