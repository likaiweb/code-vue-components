<template>
    <div class="fullImg flex justify-center align-center">
       <div class="img"   @click="screenShow=true">
           <img  :src="url" :class="fit" >
       </div>
        <transition name="slide-fade">
        <div class="fullScreenImg flex justify-center align-center" v-if="screenShow"  @click="screenShow=false">
            <img  :src="url" :class="fit">
        </div>
        </transition>
    </div>
</template>

<script>
    export default {
        name: "fullScreen",
        props:['url','fit'],
        data(){
            return{
                screenShow:false
            }
        }
    }
</script>

<style lang="scss" scoped>
 /*图片显示方式*/
.fill{
    object-fit: fill;
}
.cover{
    object-fit: cover;
}
.contain{
    object-fit: contain;
}

.fullImg{

    .img{
        width: 100%;
        height: 100%;
        img{
            width: 100%;
            height: 100%;
        }
    }
    .fullScreenImg {
        background: rgba(0,0,0,0.7);
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        padding: 100px;
        z-index: 9;
        img{
            height: 600px;
            width: 450px;
             /*box-shadow: 0 0 3px #ddd;*/
        }
    }
}
</style>
