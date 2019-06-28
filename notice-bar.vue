<template>
  <div class="notice-box flex justify-between align-center">
    <img
      class="icon notice-icon"
      :src="icons.notice">
    <div class="notice-title">{{title||'公告'}}</div>
    <van-swipe
      class="notice-list"
      :autoplay="5000"
      :show-indicators="false"
      :vertical="true">
      <van-swipe-item
        v-for="v in data"
        :key="v">
        <van-notice-bar
          :text="v"
          color='#6c6c6c'
          background="#e8eafd"
        />
    </van-swipe-item>
    </van-swipe>
    <img
      class="icon arrow-icon"
      @click="$emit('rightClick')"
      :src="icons.arrowRight">
  </div>
</template>

<script>
  export default {
    name:'notice-bar',
    props:['title','data'],
  }
</script>

<style lang="less" scoped>
  // 公告
  .notice-box {
    padding: 0 0.3rem;
    width: 100%;
    height: 0.9rem;
    background: #e8eafd;
    margin: 0.2rem 0;
    .notice-icon {
      flex-shrink: 0;
    }
    .notice-title {
      margin: 0 0.2rem 0 0.1rem;
      font-size: @sub-title-size;
      color: @theme-color;
      font-weight: 600;
      line-height: 0.45rem;
      flex-shrink: 0;
    }
    .notice-list {
      flex-grow: 1;
      height: 0.9rem;
      color: @notice-color;
      .van-notice-bar{
        height: 0.9rem;
      }
    }
    .arrow-icon {
      flex-shrink: 0;
    }
  }
  .icon {
      width: 0.32rem;
      height: 0.32rem;
    }
</style>