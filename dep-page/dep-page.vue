
<template>
  <div class="dep-page">
    <div class="search-box flex align-center justify-center">
      <van-search placeholder="请输入人员名称" shape="round" v-model="value" />
    </div>
    <dep-tree class='dep-tree' :selectData="selectData" :parentId='0' v-if="!value&&treeShow" :data="data" :isClick="isClick" @itemClick="itemClick" @itemChange="itemChange"></dep-tree>
    <div class="people-box" v-if="value">
      <van-checkbox-group v-model="userResult">
        <div class="people-item flex align-center" v-for="(v,i) in peopleData" :key="i"
        @click="itemClick(v)">
          <van-checkbox v-if="!isClick" :name="v.workUserId"></van-checkbox>
          <van-image
          width="0.8rem"
          height="0.8rem"
          :src="v.avatar"></van-image>
          <div class="content">
            <div class="name">{{v.name}}</div>
            <div class="text">{{v.position||'无职称'}}</div>
          </div> 
        </div>
      </van-checkbox-group>
    </div>
  </div>
</template>

<script>
/**
 * isClick  true 是否是人员点击，否则为勾选
 * isAll  true 是否加载全部人员，否则为信息健全人员
 * selectData  // 选中人员
 * @itemClick(v)  返回当前人员信息
 * @selectChange(v)  返回选中人员数组名单
 */
import {$getUserListByDept,$getTreeUsers} from '@/api/main/index'
import {$toolLoading,$toolClear} from '@/libs/utils'
import depTree from '@/components/dep-page/dep-tree.vue'
  export default {
    name:'dep-page',
    components:{depTree},
    props:['isClick','isAll','selectData'],
    data(){
      return {
        value:'',
        peopleData:[],  // 人员列表
        userResult:[],  // 选中
        treeShow:false,
        data:[],  // 人员树参数
      }
    },
    watch:{
      value(){
        this.getUserListByDept();
      },
      userResult(v){
        this.$emit('selectChange',v);
      }
    },
    created(){
      this.getTreeUsers();
      if(this.selectData&&this.selectData.length){
        this.userResult=this.selectData;
      }
    },
    methods: {
      // 获取人员树
      async getTreeUsers(){
        let obj={};
        if(!this.isAll){
          obj.wapFlag=1;
        }
        $toolLoading();
        const data=await $getTreeUsers(obj);
        if(data&&data.code===0){
          this.data=data.data;
          this.treeShow=true;
          $toolClear();
        }
      },
      // 获取人员
      async getUserListByDept(){
        let obj={
          name:this.value
        }
        if(!this.isAll){
          obj.wapFlag=1;
        }
        const data=await $getUserListByDept(obj);
        if(data&&data.code===0){
          this.peopleData=data.page.list;
        }
      },
      itemChange(v){
        // console.log(v)
        this.$emit('selectChange',v);
      },
      itemClick(v){
        this.$emit('itemClick',v)
      }
    }
  }
</script>

<style lang="less" scoped>
  .dep-page{
    width: 100%;
    height: 100%;
    .search-box{
      width: 100%;
      height: 1rem;
      background: @bg-color;
      .van-search{
        width: 100%;
        padding: 0.2rem 0.5rem;
      }
    }
    .dep-tree{
      width: 100%;
      height: calc(100% - 1rem);
      overflow: auto;
    }
    .people-box{
      width: 100%;
      height: calc(100% - 1rem);
      overflow: auto;
      background: #fff;
      .people-item{
        width: 100%;
        height: 1.2rem;
        padding: 0 0.3rem;
        .van-image{
          border-radius: 50%;
          overflow: hidden;
          margin: 0 0.3rem;
        }
        .content{
          color: @sub-title-color;
          font-size: @sub-title-size;
          .text{
            color: @msg-time-color;
            font-size: @normal-size;
          }
        }
      }
    }
  }
</style>