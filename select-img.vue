<template>
  <div class="select-img flex justify-center align-center">
    <slot></slot>
    <input
      type="file"
      @change="changeImage($event)">
  </div>
</template>

<script>
import { $uploadImg } from '@/api/components/selectImg';
const canvas1 = document.createElement("canvas");
const canvas2 = document.createElement("canvas");
const ctx1 = canvas1.getContext("2d");
const ctx2 = canvas2.getContext("2d");
const maxSize = 200 * 1024;
export default {
  name: "select-img",
  data() {
    return {};
  },
  created() {},
  methods: {
    /**
     * 上传图片
     */
    async uploadImg(file) {
      let fileForm = new FormData();
      fileForm.append('file', file);
      const resData = await $uploadImg(fileForm);
      if(resData&&resData.code === 0) {
        this.$emit('selectOver', resData.url);
      }
      
    },


    // 选择图片
    changeImage(event) {
      this.readFile(event.target.files[0]);
      event.srcElement.value = ""; // 清除路径, 解决(删除后重新选择/多次选择同一图片)
    },

    // 读取文件
    readFile(file) {
      //创建读取文件的对象
      const reader = new FileReader();
      //创建文件读取相关的变量
      let imgFile;
      //为文件读取成功设置事件
      reader.onload = e => {
        imgFile = e.target.result; //获取当前文件的内容
        var images = new Image();
        images.src = imgFile;
        images.onload = () => {
          if (file.size > maxSize) {
            imgFile = this.compress(images);
          }
          // const previewFile = imgFile;  // base64, 预览
          const resultFile = this.dataURLtoFile(imgFile, file.name);  // 文件, 上传
          
          // this.$emit('selectOver', {previewFile, resultFile});
          this.uploadImg(resultFile);
          
        };
      };
      //正式读取文件
      reader.readAsDataURL(file);
    },
    // 压缩图片
    compress(img) {
      let initSize = img.src.length;
      let width = img.width;
      let height = img.height;
      //如果图片大于四百万像素，计算压缩比并将大小压至400万以下
      let ratio;
      if ((ratio = (width * height) / 4000000) > 1) {
        ratio = Math.sqrt(ratio);
        width /= ratio;
        height /= ratio;
      } else {
        ratio = 1;
      }

      canvas1.width = width;
      canvas1.height = height;
      //  铺底色
      ctx1.fillStyle = "#fff";
      ctx1.fillRect(0, 0, canvas1.width, canvas1.height);
      //如果图片像素大于100万则使用瓦片绘制
      let count;
      if ((count = (width * height) / 1000000) > 1) {
        count = ~~(Math.sqrt(count) + 1); //计算要分成多少块瓦片
        //         计算每块瓦片的宽和高
        let nw = ~~(width / count);
        let nh = ~~(height / count);
        canvas2.width = nw;
        canvas2.height = nh;
        for (let i = 0; i < count; i++) {
          for (let j = 0; j < count; j++) {
            ctx2.drawImage(
              img,
              i * nw * ratio,
              j * nh * ratio,
              nw * ratio,
              nh * ratio,
              0,
              0,
              nw,
              nh
            );
            ctx1.drawImage(canvas2, i * nw, j * nh, nw, nh);
          }
        }
      } else {
        ctx1.drawImage(img, 0, 0, width, height);
      }
      //进行最小压缩
      let ndata = canvas1.toDataURL("image/jpeg", 0.1);
      console.log("压缩前：" + initSize);
      console.log("压缩后：" + ndata.length);
      console.log(
        "压缩率：" + ~~((100 * (initSize - ndata.length)) / initSize) + "%"
      );
      canvas2.width = canvas2.height = canvas1.width = canvas1.height = 0;
      return ndata;
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

  input {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
  }
}
</style>