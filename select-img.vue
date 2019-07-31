<template>
  <div class="select-img flex justify-center align-center">
    <slot></slot>
    <input
      type="file"
      @change="changeImage($event)">
      <div v-show="cropperShow" class="cropperBox">
        <div class="box_imageShow">
          <vueCropper
            class="cropper"
            ref="cropper"
            :img="option.img"
            :info="false"
            :outputSize="option.size"
            :outputType="option.outputType"
            :canScale="option.canScale"
            :autoCrop="option.autoCrop"
            :fixed="option.fixed"
            :fixedNumber="option.fixedNumber"
            :canMove="option.canMove"
            :mode="option.mode"
            :autoCropWidth="autoCropWidth"
            :autoCropHeight="autoCropHeight"
            :fixedBox="fixedBox"
            >
          </vueCropper>
        </div>
        <div class="box_operation flex align-center justify-between">
          <button class="cancel" @click="exitCropper">取消</button>
          <van-image
          width="0.69rem"
          height="0.69rem"
          :src="leftIcon"
           @click="rotateLeft"></van-image>
          <van-image
          width="0.69rem"
          height="0.69rem"
          :src="rightIcon"
           @click="rotateRight"></van-image>
          <button @click="sureCropper">确定</button>
        </div>
      </div>
  </div>
</template>

<script>

import { $toolTip,$toolLoading,$toolClear } from '@/libs/utils';
import { $uploadImg } from '@/api/components/selectImg';
export default {
  name: "select-img",
  props:['autoCropWidth','autoCropHeight','fixedBox'],
  data() {
    return {
      cropperShow:false,
      leftIcon:require('@/assets/images/common/leftRotate.png'),
      rightIcon:require('@/assets/images/common/rightRotate.png'),
      option: {
        img: '',
        size: 0.5,
        outputType: 'png',
        canScale: true,
        autoCrop: true,
        fixed: true,
        fixedNumber: [1.6,1],
        canMove: true,
        mode: '100% auto'
      },
      fileName:''
    };
  },
  created() {

  },
  methods: {
    changeImage(e){
      let that=this;
      this.fileName=new Date().getTime() +e.target.files[0].name;
      let reader = new FileReader();
      reader.onload = function (evt) {
          var replaceSrc = evt.target.result;
          that.cropperShow=true;
          that.option.img = evt.target.result
      }
      reader.readAsDataURL(e.target.files[0]);
      e.srcElement.value = "";
    },
    sureCropper() {
      var that = this;
      this.$refs.cropper.getCropData((data) => {
        let file=that.dataURLtoFile(data,this.fileName);
        that.uploadImg(file)
        $toolLoading();
        this.cropperShow=false;
      })
    },
     async uploadImg(file) {
      let fileForm = new FormData();
      fileForm.append('file', file);
      const data = await $uploadImg(fileForm);
      $toolClear();
      if(data&&data.code === 0) {
        this.$emit('selectOver', data.url);
      }else{
        $toolTip('图片过大，请重新选择！');
      }
    },
    rotateLeft() {
      this.$refs.cropper.rotateLeft()
    },
    rotateRight() {
      this.$refs.cropper.rotateRight()
    },
    exitCropper() {
      this.cropperShow=false;
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
    }
  }
};
</script>

<style lang='less' scoped>
.select-img {
  position: relative;
  width: 100%;
  height: 100%;
  *{
      -webkit-overflow-scrolling:auto;
  }
  input {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
  }
  .cropperBox{
    position: fixed;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    z-index: 99999;
    background: rgba(0,0,0,0.5);
    .box_imageShow{
      width: 100%;
      height: 100%;
    }
    .box_operation{
      position: absolute;
      padding: 0 0.2rem;
      left: 0;
      bottom: 1rem;
      width: 100%;
      height: 1rem;
      button{
        padding: 0.1rem 0.3rem;
        margin: 0 0.2rem;
        color: #fff;
        font: initial;
        font-size: @sub-title-size;
        background: #66C51F;
        border-radius: 0.1rem;
      }
      .van-image{
        margin: 0 0.2rem;
      }
      .cancel{
        background: transparent;
      }
    }
  }
}
</style>