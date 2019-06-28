<template>
  <div class="select-alert">
    <van-action-sheet v-model="myShow" :title="title||'请选择类型'">
      <van-radio-group v-model="radio.content">
        <van-cell-group>
          <van-cell v-for="(v,i) in typeArr" :key="i" :title="v.content" clickable @click="radio = v">
            <van-radio :name="v.content" />
          </van-cell>
        </van-cell-group>
      </van-radio-group>
      <div class="button" :style="{'opacity':radio?1:0.6}" @click="submitType">确定</div>
    </van-action-sheet>
  </div>
</template>

<script>
  export default {
    name:'select-alert',
    props:['show','title','typeArr'],
    data(){
      return {
        myShow:this.show,
        radio:{}
      }
    },
    watch:{
      show(val){
        this.myShow=val;
      },
      myShow(val){
        this.$emit('showChange',val)
      },
    },
    methods:{
      submitType(){
        if(this.radio){
          this.myShow=false;
          this.$emit('submitType',this.radio)
        }
      }
    }
  }
</script>

<style lang="less" scoped>
  .select-alert{
    max-height: 8rem;
    .van-cell{
      justify-content: space-between;
    }
    .van-cell__title, .van-cell__value{
      flex: none;
    }
    .button{
      width: 90%;
      height: 0.9rem;
      background: @btn-color;
      border-radius: 0.5rem;
      margin: 0.2rem 5%;
      font-size: @title-size;
      line-height: 0.9rem;
      text-align: center;
      color: #fff;
      box-shadow: @btn-shadow;
      font-weight: 600;
      letter-spacing: 0.2rem;
    }
  }
</style>