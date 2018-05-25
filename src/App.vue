<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods" exact>商品</router-link>
        </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    
    <router-view></router-view>
  </div>
</template>

<script>
import header from "@/components/header/header";
export default {
  data() {
    return {
      seller: {}
    };
  },
  created() {
    this.$http.get("/api/seller").then(response => {
      response = response.body;
      if (response.errno === 0) {
        this.seller = response.data;
      }
    });
  },
  components: {
    "v-header": header
  }
};
</script>

<style lang='scss'>
@import './common/scss/mixin.scss';

#app {
  .tab {
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    // border-bottom: 1px solid rgba(7,15,27,0.1);
    @include border-1px(rgba(7, 15, 27, 0.1));

    .tab-item {
      flex: 1;
      text-align: center;

      & > a {
        display: block;
        font-size: 14px;
        color: rgb(77, 85, 93);

        &.router-link-active {
          color: red;
          box-shadow: 0 0 5px #000 inset;
        }
      }
    }
  }
}
</style>
