/*
 * @Author: likai 
 * @Date: 2019-07-05 11:17:11 
 * @Last Modified by: likai
 * @Last Modified time: 2019-07-11 16:19:13
 */
<template>
  <div class="cropper">
      <div class="button flex justify-between align-center">
          <span @click="cancelCropper">取消</span><span @click="successCropper">完成</span>
      </div>
      <vueCropper
          ref="cropper"
          :info="false"
          :autoCrop="true"
          :autoCropWidth="355"
          :autoCropHeight="230"
          :fixedBox="true"
          :canScale="true"
          :canMoveBox="false"
          :img="myUrl"
      ></vueCropper>
  </div>
</template>

<script>
import { $uploadImg } from '@/api/components/selectImg';
import { $toolTip,$toolLoading,$toolClear } from '@/libs/utils';
  export default {
    name:'my-cropper',
    props:['url'],
    data(){
      return {
        myUrl:this.url,
      }
    },
    watch:{
      url(val){
        this.myUrl=val;
      }
    },
    methods:{
      // 取消截图
      cancelCropper(){
          this.$refs.cropper.clearCrop();
          this.$emit('cropperComplete','');
      },
      // 成功截图
      successCropper(){
        $toolLoading();
          this.$refs.cropper.getCropData((data) => {
              let resultFile=this.dataURLtoFile(data,this.myUrl.slice(this.myUrl.lastIndexOf('/')+1));
              this.uploadImg(resultFile);
          })
      },
      async uploadImg(file) {
        let fileForm = new FormData();
        fileForm.append('file', file);
        const resData = await $uploadImg(fileForm);
        if(resData&&resData.code === 0) {
          $toolClear();
          this.$emit('cropperComplete', resData.url);
        }
      },
      //将base64转换为文件
      dataURLtoFile(dataurl, filename) {
          let arr = dataurl.split(","),
              mime = arr[0].match(/:(.*?);/)[1],
              bstr = atob(arr[1]),
              n = bstr.length,
              u8arr = new Uint8Array(n);
          while (n--) {
              u8arr[n] = bstr.charCodeAt(n);
          }
          return new File([u8arr], filename, { type: mime });
      },
    }
  }
</script>

<style lang="less" scoped>
  .cropper{
      position: fixed;
      top: 0;
      left: 0;
      background: rgba(0,0,0,.3);
      z-index: 1000;
      width: 100%;
      height: 100%;
      .button{
          width: 100%;
          height: 0.8rem;
          position: absolute;
          top: 0;
          z-index: 2;
          padding: 0 0.3rem;
          span{
              font-size: @sub-title-size;
              color: #fff;
          }
      }
  }
</style>