/*
 * @Author: likai 
 * @Date: 2019-06-06 15:55:56 
 * @Last Modified by: likai
 * @Last Modified time: 2019-06-27 14:49:57
 */
<template>
  <div class="pay-alert">
    <!-- 支付弹窗 -->
    <van-action-sheet v-model="myPayShow" title="确认支付">
      <div class="pay-box">
        <div class="price">￥{{priceFixed(allPrice)}}</div>
        <div class="text">支付方式</div>
        <van-radio-group v-model="radio"  class="card-box">
          <van-cell-group>
            <van-cell class="card" clickable @click="radio = '1'">
              <van-image
                width="0.5rem"
                height="0.5rem"
                fit="contain"
                :src="payIcons[0]"
              />
              <label for="">礼品卡({{cardData.length}})</label>
              <div class="over">{{blessMoney}}云豆</div>
              <van-radio name="1" />
            </van-cell>
            <van-cell class="card" v-if="!wxHide" clickable @click="radio = '2'">
              <van-image
                width="0.5rem"
                height="0.5rem"
                fit="contain"
                :src="payIcons[1]"
              />
              <label for="">微信支付 <span>微信安全支付</span></label>
              <van-radio name="2" />
            </van-cell>
          </van-cell-group>
        </van-radio-group>
        <div class="button"
        @click="paySubmit">{{radio=='1'?'云豆':'微信'}}支付</div>
      </div>
    </van-action-sheet>
  </div>
</template>

<script>
import {$myWelfareCard} from '@/api/market/myOrder'
  export default {
    name:'pay-alert',
    props:['payShow','allPrice','wxHide'],
    data(){
      return {
        myPayShow:this.payShow,
        radio:'1',  // 支付类型
        cardData:[],  // 礼品卡信息
        payIcons:[
          require("@/assets/images/market/my-order/icons-card.png"),
          require("@/assets/images/market/my-order/icons-wx.png")
        ],
      }
    },
    watch:{
      payShow(val){
        this.myPayShow=val;
      },
      myPayShow(val){
        this.$emit('showChange',val)
      }
    },
    computed:{
      // 云豆余额
      blessMoney(){
        return this.cardData.length?this.cardData.reduce((pre,cur)=>pre.blessMoney+cur.blessMoney):0;
      }
    },
    created(){
      this.myWelfareCard();
    },
    methods:{
      // 获取礼品卡
      async myWelfareCard(){
        const data=await $myWelfareCard();
        if(data&&data.code===0){
          this.cardData=data.data;
        }
      },
      paySubmit(){
        this.myPayShow=false;
        this.$emit('paySubmit',this.radio);
      }
    }
  }
</script>

<style lang="less" scoped>
  .pay-alert{
    .van-action-sheet{
        border-radius: 0.2rem 0.2rem 0 0;
        .pay-box{
          display: flex;
          flex-direction: column;
          align-items: center;
          .price{
            font-size: 0.72rem;
            color:@title-color;
            margin: 0.3rem 0;
          }
          .text{
            width: 100%;
            height: 0.6rem;
            padding: 0 0.2rem;
            text-align: left;
            font-size: @big-size;
            color: @msg-time-color;
          }
          .card-box{
            width: 100%;
            padding: 0.2rem;
            .card{
              width: 100%;
              height: 1rem;
              margin: 0.2rem 0;
              background: #fff;
              box-shadow: @box-shadow;
              border-radius: 0.1rem;
              align-items: center;
              padding: 0 0.2rem;
              .van-cell__value--alone{
                width: 100%;
                height: 100%;
                display: flex;
                align-items: center;
                label{
                  margin:0 0.2rem;
                  margin-right:auto;
                  display: flex;
                  flex-direction: column;
                  line-height: 0.3rem;
                  span{
                    color:@msg-time-color;
                    font-size: @small-size;
                  }
                }
                .over{
                  margin-right: 0.4rem;
                  color: rgba(255, 149, 53, 1);
                }
              }
            }
          }
          .button{
            width: 80%;
            height: 0.9rem;
            font-size: @title-size;
            line-height: 0.9rem;
            text-align: center;
            border-radius: 0.5rem;
            background: @btn-color;
            box-shadow: @btn-shadow;
            color: #fff;
            margin: 0.2rem 0 0.4rem;
          }
        }
      }
  }
</style>