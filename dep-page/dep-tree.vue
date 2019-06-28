<template>
<!-- 人员树 -->
  <div class="dep-tree">
    <van-checkbox-group v-model="depResult">
      <div class="tree-item" v-for="(v,i) in depData" :key="i">
        <!-- 部门 -->
        <div class="content">
          <van-checkbox v-if="!isClick" :name="v.deptId"></van-checkbox>
          <div class="name"> {{v.deptName}} </div>
          <van-icon name="arrow" v-show="v.open===false" @click="$set(depData[i],'open',true)"></van-icon>
          <van-icon name="arrow-down" v-show="v.open||v.open===undefined" @click="$set(depData[i],'open',false)"></van-icon>
        </div>
        <!-- 子部门 -->
        <div class="childen" v-show="v.open||v.open===undefined">
          <dep-tree :parentId="v.deptId" @itemChange="itemChange" :isClick="isClick" :isSelectDep="v.isSelectDep"  @itemClick="itemClick" @depChange="depChange"></dep-tree>
        </div>
      </div>
    </van-checkbox-group>
      <!-- 当前部门用户 -->
    <van-checkbox-group v-model="result">
      <section>
        <div class="user-list flex align-center" v-for="(val,index) in userData" :key="index"
          @click="itemClick(val)">
          <van-checkbox v-if="!isClick" :name="val.workUserId"></van-checkbox>
          <van-image
          width="0.8rem"
          height="0.8rem"
          fit="cover"
          :src="val.avatar"></van-image>
          <div class="text">
            <div class="name">{{val.name}}</div>
            <div class="pos">{{val.position||'无职位'}}</div>
          </div>
        </div>
      </section>
    </van-checkbox-group>
  </div>
</template>

<script>
/**
 * parentId  // 父节点id
 * isClick  // 是否点击事件
 * isSelectDep  // 是否选中当前节点
 * @itemChange  // 人员改变事件
 * @depChange  // 部门改变事件
 */
import {$getDeptList,$getUserListByDept} from '@/api/main/index'
  export default {
    name:'dep-tree',
    props:['parentId','isClick','isSelectDep'],
    data(){
      return {
        // 人员
        userData:[],
        result:[],
        sonArr:[],
        concatArr:[],

        // 部门
        depData:[],
        depResult:[],
        sonDepArr:[]
      }
    },
    watch:{
      // 监听选中部门数据变化
      depResult(v){
        this.depData.forEach(val=>{
          if(v.indexOf(val.deptId)!=-1){
            val.isSelectDep=true;
          }else{
            val.isSelectDep=false;
          }
        })
      },
      // 人员变化
      result(v){
        this.$emit('itemChange',v,this.parentId);
      },
      // 当前部门选中变化
      isSelectDep(v){
        if(v){
          this.result=this.userData.map(val=>val.workUserId);
          this.depResult=this.depData.map(val=>val.deptId);
        }else{
          this.result=[];
          this.depResult=[];
        }
      }
    },
    created(){
      this.getDeptList();
      if(this.parentId){
        this.getUserListByDept();
      }
    },
    methods:{
      // 人员子节点返回
      itemChange(v,id){
        // 整合所有人员
        if(!this.sonArr.some(val=>val.id==id)){
          this.sonArr.push({
            id:id,
            arr:v
          })
        }else{
          this.sonArr.forEach(val=>{
            if(val.id==id){
              val.arr=v;
            }
          })
        }
        this.concatArr=this.sonArr.map(val=>val.arr).flat();
        this.$emit('itemChange',[...new Set([...this.result,...this.concatArr])]);
      },
      // 部门子节点返回，暂时无用
      depChange(v){
        this.sonDepArr=v;
        this.$emit('depChange',[...new Set([...this.depResult,...this.sonDepArr])]);
      },
      // 获取部门树
      async getDeptList(){
        const data=await $getDeptList(this.parentId);
        if(data&&data.code===0){
          this.depData=data.data;
          this.depData.forEach((v,i)=>{
            this.$set(this.depData[i],'open',false);
            this.$set(this.depData[i],'isSelectDep',this.isSelectDep);
            this.$set(this.sonArr,i,{
              id:v.deptId,
              arr:[]
            })
          })
        }
      },
      // 获取部门下人员树
      async getUserListByDept(){
        const data=await $getUserListByDept({
          wapFlag:1,
          deptId:this.parentId
        });
        if(data&&data.code===0){
          this.userData=data.page.list;
        }
      },
      // 人员点击事件
      itemClick(v){
        if(this.isClick){
          this.$emit('itemClick',v);
        }
      }
    }
  }
</script>

<style lang="less" scoped>
  .dep-tree{
    width: 100%;
    height: 100%;
    .tree-item{
      width: 100%;
      .content{
        height: 0.75rem;
        display: flex;
        align-items: center;
        font-size: .32rem;
        color: #5c5c5c;
        padding: 0 0.2rem;
        .name{
          margin-left: 0.2rem;
          margin-right: auto;
        }
      }
      .childen,.user-list{
        padding: 0 0.2rem;
      }
      .user-list{
        height: 1.2rem;
        .van-image{
          border-radius: 50%;
          overflow: hidden;
          margin:0 0.2rem;
        }
        .name{
          font-size: @big-size;
          color: @title-color;
        }
        .pos{
          font-size: @small-size;
          color: @msg-time-color;
        }
      }
    }
  }
</style>