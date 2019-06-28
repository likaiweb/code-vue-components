<template>
  <div class="send-message flex justify-between align-center" v-show="myShow">
    <span>{{myAcceptUser}}</span>
    <input type="text"
        placeholder="评论"
        v-model="myMessage"
        v-focus="myShow"
        @blur="blurClick"
    >
    <div class="button" @click="sendClick">发送</div>
  </div>
</template>

<script>
  export default {
    name:'send-message',
    props:['message','show','acceptUser'],
    data(){
      return{
        myAcceptUser:'',
        myShow:false,
        myMessage:'',
      }
    },
    watch:{
      myShow(v){
        this.$emit('setShow',v);
      },
      show(v){
        this.myShow=v;
      },
      message(v){
        this.myMessage=v;
      },
      acceptUser(v){
        this.myAcceptUser=v;
      }
    },
    created(){
      
    },
    methods:{
      blurClick(){
        if(!this.myMessage){
          this.myShow=false;
        }
      },
      sendClick(){
        if(this.myMessage){
          this.$emit('sendMessage',this.myMessage);
        }
      }
    }
  }
</script>

<style lang="less" scoped>
  .send-message{
    width: 100%;
    height: 1rem;
    position: absolute;
    bottom: 0;
    left: 0;
    background: @bg-color;
    padding: 0 0.2rem;
    z-index: 100;
    span{
      min-width: 0.4rem;
      height: 0.6rem;
      background: #fff;
      border-radius: 0.3rem 0 0 0.3rem;
      line-height: 0.6rem;
      text-align: center;
      font-size: @sub-title-size;
      padding: 0 0.1rem;
      color: @theme-color;
      flex-shrink: 0;
    }
    input {
      padding: 0.1rem 0.3rem 0.1rem 0.1rem;
      width: calc(100% - 2.2rem);
      height: 0.6rem;
      line-height: 0.6rem;
      border-radius: 0 0.3rem 0.3rem 0;
      background: #fff;
      font-size:  @big-size;
      color:@title-color;
      flex-shrink: 1;
    }
    .button {
      width: 1.8rem;
      height: 0.6rem;
      text-align: center;
      line-height: 0.6rem;
      border-radius: 0.3rem;
      color: #fff;
      margin-left: 0.2rem;
      flex-shrink: 0;
      font-size: @sub-title-size;
      background: linear-gradient(
        157deg,
        rgba(245, 81, 95, 1) 0%,
        rgba(248, 5, 41, 1) 100%
      );
      box-shadow: 0px 2px 6px 0px rgba(208, 2, 27, 1);
    }
  }
</style>