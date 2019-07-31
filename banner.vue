/*
 * @Author: likai
 * @Date: 2019-05-30 16:53:39
 * @Last Modified by: likai
 * @Last Modified time: 2019-06-28 15:17:32
 */

<template>
  <div class="banner-box" v-if="data">
    <van-swipe
      :autoplay="3000"
      indicator-color="#2B3787"
      :show-indicators="false"
      class="swipe-box"
      @change="changeBannerIndex">
      <van-swipe-item
        v-for="(item,index) in data"
        :key="index">
        <div class="item-box flex justify-center">
          <van-image
          width="7.1rem"
          :height="height?String(height)+'rem':'2.8rem'"
          fit="cover"
          @click="goDetail(item)"
          :src="item.bannerSrc"></van-image>
        </div>
      </van-swipe-item>
    </van-swipe>
    <div class="dot-list flex flex justify-center align-center">
      <div class="dot"
        :class="{'activeDot': activeBannerIndex == index}"
        v-for="(item,index) in data"
        :key="index"></div>
    </div>
  </div>
</template>

<script>
  export default {
    name:'banner-box',
    props:[
      'data',
      'height'
    ],
    data(){
        return {
          activeBannerIndex: 0,  // banner下标
        }
    },
    methods:{
      // 切换轮播图
      changeBannerIndex(index) {
        this.activeBannerIndex = index;
      },
      goDetail(v){
        if(v.bannerUrl){
          location.href=v.bannerUrl;
        }
      }
    }
  }
</script>

<style lang="less" scoped>

  // banner
  .banner-box {
    padding: 0.2rem 0 0;
    width: 100%;
    .item-box {
      width: 100%;
      .van-image{
        border-radius: 0.24rem;
        background: #fff;
        overflow: hidden;
      }
    }
    .dot-list {
      margin: 0.2rem 0;
      .dot {
        margin-right: .1rem;
        width: .1rem;
        height: .1rem;
        background: #E3E3E4;
        border-radius: 50%;
        &:nth-last-of-type(1) {
          margin: 0;
        }
        &.activeDot {
          background: #2B3787;
        }
      }
    }
  }
</style>
