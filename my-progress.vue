<template>
  <div class="my-progress" :style="style">
    <div class="circle" :style="{'background':color}">
      <div class="pie_left" :style="leftStyle">
        <div class="left" :style="{...leftStyle,'transform':leftTransform}"></div>
      </div>
      <div class="pie_right" :style="rightStyle">
        <div class="right" :style="{...rightStyle,'transform':rightTransform}"></div>
      </div>
      <div class="mask flex align-center justify-center" :style="markStyle">
        <div class='num'>
          <span>{{percent}}</span>%
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name:'my-progress',
    props:['width','percent','color'],
    data(){
      return {
        style:{
          width:this.width?(this.width+'px'):'100%',
          height:this.width?(this.width+'px'):'100%',
        },
        leftStyle:{
          clip:'rect(0,auto,auto,'+(this.width/2)+'px)'
        },
        rightStyle:{
          clip:'rect(0,'+(this.width/2)+'px,auto,0)'
        },
        markStyle:{
          width: (this.width-20)+'px',
          height: (this.width-20)+'px',
        },
        leftTransform:'rotate('+((this.percent>50?50:this.percent)*3.6)+'deg)',
        rightTransform:'rotate('+((this.percent-50>0?(this.percent-50):0)*3.6)+'deg)',
      }
    }
  }
</script>

<style lang="scss" scoped>
  .my-progress{
    position: relative;
    .circle {
      width: 100%;
      height: 100%;
      position: absolute;
      border-radius: 50%;
      left:0;
      background: linear-gradient(180deg, rgba(74,183,255,1), rgba(133,103,255,1));
      .pie_left, .pie_right {
        width:100%; 
        height:100%;
        position: absolute;
        top: 0;
        left: 0;
      }
      .left, .right {
        width:100%; 
        height:100%;
        background:#F1F6FD;
        border-radius: 50%;
        position: absolute;
        top: 0;
        left: 0;
      }
      .mask {
        border-radius: 50%;
        left: 10px;
        top: 10px;
        background: #FFF;
        position: absolute;
        z-index: 10;
        .num{
          color: #5c5c5c; 
          font-size: 20px;
          font-weight: bold;
        }
      }
    }
  }
</style>