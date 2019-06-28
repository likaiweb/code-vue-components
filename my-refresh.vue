/*
 * @Author: likai 
 * @Date: 2019-05-24 19:01:41 
 * @Last Modified by:   likai 
 * @Last Modified time: 2019-05-24 19:01:41 
 */
<template>
  <div class="refresh">
    <EasyRefresh
      :userSelect="false"
      :onRefresh="onRefresh"
      :loadMore="loadMore">
        <template v-slot:header>
          <ClassicsHeader
          :refreshText="'释放刷新'"
          :refreshReadyText="'准备刷新'"
          :refreshingText="'正在刷新'"
          :refreshedText="'刷新完成'"
          />
        </template>
        <slot></slot>
        <template v-slot:footer>
            <ClassicsFooter
            :loadText="'释放加载'"
            :loadReadyText="'准备加载'"
            :loadingText="'加载中'"
            :noMoreText="'没有更多'"
            :loadedText="'加载完成'"/>
        </template>
    </EasyRefresh>
  </div>
</template>

<script>
  export default {
    name:'refresh',
    methods:{
      onRefresh(done){
        this.$emit('onRefresh',done)
      },
      loadMore(done){
        this.$emit('loadMore',done)
      }
    }
  }
</script>

<style lang="less" scoped>
  .refresh{
    width: 100%;
    height: 100%;
  }
</style>