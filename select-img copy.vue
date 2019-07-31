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
import { $toolTip,$toolLoading,$toolClear } from '@/libs/utils';
const canvas1 = document.createElement("canvas");
const canvas2 = document.createElement("canvas");
const ctx1 = canvas1.getContext("2d");
const ctx2 = canvas2.getContext("2d");
const maxSize = 200 * 1024;
export default {
  name: "select-img",
  data() {
    return {
      headerImage:'',picValue:'',Orientation:''
    };
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
        $toolClear();
        this.$emit('selectOver', resData.url);
      }else{
        $toolTip('图片过大，请重新选择！');
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
        let self = this;
        $toolLoading();
        imgFile = e.target.result; //获取当前文件的内容
        var images = new Image();
        images.src = imgFile;
        images.onload = () => {
          // 压缩
          this.EXIF.getData(file, function () {
            self.Orientation = self.EXIF.getAllTags(this).Orientation // 此处打印的为选中图片的数据
            if (file.size > maxSize) {
              imgFile = self.compress(images,self.Orientation);
            }
            const resultFile = self.dataURLtoFile(imgFile, file.name);  // 文件, 上传
            // const previewFile = imgFile;  // base64, 预览
            // this.$emit('selectOver', {previewFile, resultFile});
            self.uploadImg(resultFile);
          })
        };
      };
      //正式读取文件
      reader.readAsDataURL(file);
    },
    // 压缩图片
    compress(img,Orientation) {
      let initSize = img.src.length;
      let width = img.width;
      let height = img.height;
      if(Orientation != "" && Orientation != 1){
        switch(Orientation){
          case 6://需要顺时针（向左）90度旋转
            this.rotateImg(img,'left',canvas1);
            break;
          case 8://需要逆时针（向右）90度旋转
            this.rotateImg(img,'right',canvas1);
            break;
          case 3://需要180度旋转
            this.rotateImg(img,'right',canvas1);//转两次
            this.rotateImg(img,'right',canvas1);
            break;
        }
      }
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
      let ndata = canvas1.toDataURL("image/jpeg", 0.2);
      // console.log("压缩前：" + initSize);
      // console.log("压缩后：" + ndata.length);
      // console.log(
      //   "压缩率：" + ~~((100 * (initSize - ndata.length)) / initSize) + "%"
      // );
      canvas2.width = canvas2.height = canvas1.width = canvas1.height = 0;
      return ndata;
    },

    //rotateImg  图片旋转问题
 rotateImg (img, direction,canvas1) {
        //最小与最大旋转方向，图片旋转4次后回到原方向
        const min_step = 0;
        const max_step = 3;
        if (img == null)return;
        //img的高度和宽度不能在img元素隐藏后获取，否则会出错
        let height = img.height;
        let width = img.width;
        let step = 2;
        if (step == null) {
          step = min_step;
        }
        if (direction == 'right') {
          step++;
          //旋转到原位置，即超过最大值
          step > max_step && (step = min_step);
        } else {
          step--;
          step < min_step && (step = max_step);
        }
        //旋转角度以弧度值为参数
        let degree = step * 90 * Math.PI / 180;
        let ctx = canvas1.getContext('2d');
        switch (step) {
          case 0:
            canvas1.width = width;
            canvas1.height = height;
            ctx.drawImage(img, 0, 0);
            break;
          case 1:
            canvas1.width = height;
            canvas1.height = width;
            ctx.rotate(degree);
            ctx.drawImage(img, 0, -height);
            break;
          case 2:
            canvas1.width = width;
            canvas1.height = height;
            ctx.rotate(degree);
            ctx.drawImage(img, -width, -height);
            break;
          case 3:
            canvas1.width = height;
            canvas1.height = width;
            ctx.rotate(degree);
            ctx.drawImage(img, -width, 0);
            break;
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