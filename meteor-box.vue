/*
 * @Author: likai 
 * @Date: 2019-06-05 18:48:58 
 * @Last Modified by:   likai 
 * @Last Modified time: 2019-06-05 18:48:58 
 */
<template>
  <div class="meteor-box" :style="{'height':height||''}">
    <div class="animation-box">
      <img
        class="user-bg-img"
        :src="userBg.main"
      >
      <img
        class="user-bg-img bounce"
        :src="userBg.sub"
      >
      <img
        class="shooting-star shooting-star-move1"
        :src="userBg.star"
      >
      <img
        class="shooting-star shooting-star-move2"
        :src="userBg.star"
      >
      <img
        class="shooting-star shooting-star-move3"
        :src="userBg.star"
      >
    </div>
  </div>
</template>

<script>
  export default {
    name:'meteor-box',
    props:['height'],
    data(){
      return {
        userBg: {
          main: require("@/assets/images/main/mine/bg-main.png"),
          sub: require("@/assets/images/main/mine/bg-sub.png"),
          star: require("@/assets/images/main/mine/icon-star.png")
        }
      }
    }
  }
</script>

<style lang="less" scoped>
  .meteor-box{
    width: 100%;
    height: 3rem;
    .animation-box {
      position: absolute;
      top: 0;
      left: 0;
      height: 100%;
      width: 100%;
      box-shadow: 0 1rem 0 @theme-color inset;
      overflow: hidden;
      .user-bg-img {
        position: absolute;
        bottom: 0;
        left: 0;
        height: 100%;
        width: 100%;
      }
      .shooting-star {
        position: absolute;
        &:nth-of-type(3) {
          top: -0.4rem;
          left: 30%;
          width: 0.25rem;
          height: 0.4rem;
        }
        &:nth-of-type(4) {
          top: -1rem;
          left: 73%;
          width: 0.65rem;
          height: 1rem;
        }
        &:nth-of-type(5) {
          top: -0.4rem;
          left: 88%;
          width: 0.35rem;
          height: 0.54rem;
        }
      }
    }
  }
/* 动画效果 */
.bounce {
  animation: bg-bounce 1s cubic-bezier(0, 0, 1, 1) infinite alternate;
}

.shooting-star-move1 {
  animation: shooting-star-move1 5s infinite;
}

.shooting-star-move2 {
  animation: shooting-star-move2 3s infinite;
}

.shooting-star-move3 {
  animation: shooting-star-move3 4s infinite;
}

@keyframes bg-bounce {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@keyframes shooting-star-move1 {
  0% {
    top: -0.4rem;
    left: 30%;
    // opacity: 1;
  }
  50% {
    // opacity: 1;
  }
  51% {
    // opacity: 0;
  }
  100% {
     opacity: 0;
    top: 100%;
    left: 10%;
  }
}

@keyframes shooting-star-move2 {
  0% {
    top: -1rem;
    left: 83%;
  }
  100% {
    top: 100%;
    left: 50%;
  }
}

@keyframes shooting-star-move3 {
  0% {
    top: -.35rem;
    left: 88%;
  }
  100% {
    top: 100%;
    left: 60%;
  }
}
</style>