/*
 * @Author: likai 
 * @Date: 2019-05-24 19:01:35 
 * @Last Modified by: likai
 * @Last Modified time: 2019-06-13 17:29:27
 */
<template>
  <div class="goods-order" :style="{'height':height||'1rem'}">
    <div class="order-list">
      <div class="order-item"
        :class="{'order-item-select':orderIndex==i}"
        :style="{'color':orderIndex==i?selectColor:''}"
        v-for="(v,i) in innerOrderList" 
        :key="i" 
        @click="setOrder(v,i)">
        <span>{{v.name}}</span>
        <div class="icon" v-if="v.order">
          <span class="icon-ghy icontup"></span><span class="icon-ghy icontdown"></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name:'goods-order',
    props:['selectColor','height','orderList'],
    data(){
      return {
        innerOrderList:this.orderList?this.orderList:[{
          name:'综合排序',
          value:'all'
        },{
          name:'销量优先',
          value:'sales'
        },{
          name:'价格',
          value:'price',
          order:'desc'
        },{
          name:'上新',
          value:'new'
        }],
        orderIndex:0
      }
    },
    methods:{
      setOrder(v,i){
        this.orderIndex=i;
        if(v.order){
          v.order=v.order=='desc'?'asc':'desc'
        }
        this.$emit('setOrder',v);
      }
    }
  }
</script>

<style lang="less" scoped>
  .goods-order{
    width: 100%;
    .order-list{
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: space-around;
      .order-item{
        font-size: @big-size;
        color:@sub-title-color;
        position: relative;
        transition: all .3s;
        .icon{
          position: absolute;
          top: 0;
          right: -0.3rem;
          display: flex;
          flex-direction: column;
          justify-content: center;
          line-height: 0.18rem;
        }
      }
      .order-item-select{
        color:@theme-color;
      }
    }
  }
</style>