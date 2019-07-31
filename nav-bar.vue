/*
 * @Author: likai 
 * @Date: 2019-05-30 16:53:18 
 * @Last Modified by: likai
 * @Last Modified time: 2019-07-25 10:57:53
 */
<template>
  <div class="nav-bar" :class="theme">
    <van-nav-bar
        :title="title||$route.meta.title"
        left-arrow
        @click-left="onClickLeft"
        :right-text="rigntText||''"
        @click-right="onClickRight">
        <slot></slot>
    </van-nav-bar>
  </div>
</template>

<script>
  export default {
    name:'nav-bar',
    props:[
      'title',
      'theme',
      'rigntText'
    ],
    data(){
      return {
        
      }
    },
    methods:{
      onClickLeft(){
        if(this.$parent.onClickLeft){
          this.$parent.onClickLeft();
        }else{
          this.$router.back();
        }
      },
      onClickRight(){
        this.$emit('onClickRight');
      },
    }
  }
</script>

<style lang="less" scoped>
  .nav-bar{
    width: 100%;
    height: 0.88rem;
    .van-nav-bar{
      width: 100%;
      height: 0.88rem;
      line-height: 0.88rem;
      background: @theme-color;
      border: 0;
      .van-nav-bar__title{
        color:#fff;
        font-size: @title-size;
        font-weight: 600;
      }
      .van-nav-bar__text{
        color:#fff;
        font-size: @sub-title-size;
      }
       .van-icon{
         color: @left-color;
       }
      .van-nav-bar__text:active{
        background: @theme-color;
      }
    }
    &.white{
      .van-nav-bar{
        background: #fff;
        .van-nav-bar__title, .van-icon, .van-nav-bar__text{
          color: @title-color;
        }
        .van-nav-bar__text:active{
          background: #fff;
        }
      }
    }
    &.bg{
      .van-nav-bar{
        background: @bg-color;
        .van-nav-bar__title, .van-icon, .van-nav-bar__text{
          color: @title-color;
        }
        .van-nav-bar__text:active{
          background: @bg-color;
        }
      }
    }
    &.transparent{
      .van-nav-bar{
        background: transparent;
        .van-nav-bar__title, .van-icon{
          color: #fff;
        }
        .van-nav-bar__text:active{
          background: transparent;
        }
      }
    }
  }
.van-hairline--bottom::after{
  border-bottom-width:0;
}
</style>