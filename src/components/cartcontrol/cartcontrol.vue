<template>
  <div class="cartcontrol">
    <transition name="" enter-active-class="animated fadeInRight" leave-active-class="animated rollOut" :duration="700">
      <div class="cart-decrease iconfont icon-msnui-sub" v-show="food.count>0" @click.stop.prevent="decreaseCart"></div>
    </transition>
    
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add iconfont icon-msnui-add" @click.stop.prevent="addCart($event.target)">
    </div>

    
    
  </div>
</template>

<script>
import Vue from "vue";
export default {
  props:{
    food:{
      type:Object
    }
  },
  data(){
    return {
     
      
    }
  },
  created(){
    // console.log(this.food)
  },
  methods:{
    addCart(that){
      // console.log(1):
      if(!this.food.count){
        // this.food.count = 1;
        Vue.set(this.food,'count',1);
      }else{
        this.food.count++;
      };
      // console.log(that)
      // let ele = that;
      // createball(this._createball(that))
      // this.show = !this.show;
      this.$emit('getTarget',that);
    },
    decreaseCart(){
      if(this.food.count){
        this.food.count--;
      }
    },
  }
}
</script>

<style lang="scss">
  .cartcontrol{
    font-size: 0;
    .cart-decrease{
      display: inline-block;
      line-height: 24px;
      font-size: 24px;
      padding: 6px;
      color: rgb(0,160,220);
      // background: #fff;
      // color: #fff;
    }
    .cart-count{
      display: inline-block;
      vertical-align: top;
      width: 12px;
      padding-top: 6px;
      line-height: 24px;
      text-align: center;
      font-size: 10px;
      color: rgb(147,153,159);
    }
    .cart-add{
      @extend .cart-decrease;
      color: rgb(0,160,220);
    }
   
  }
</style>


