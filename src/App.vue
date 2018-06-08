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
    <transition :name="transitionName">
      <!-- <keep-alive> -->
        <router-view :seller="seller" keep-alive class="view"></router-view>
      <!-- </keep-alive> -->
    </transition>
    
  </div>
</template>

<script>
import header from "@/components/header/header";
import { urlParse } from "@/common/js/util.js";
export default {
  data() {
    return {
      transitionName: "",
      seller: {
        id: (() => {
          let queryParam = urlParse();
          return queryParam.id;
        })()
      }
    };
  },
  created() {
    this.$http.get("/api/seller?id=" + this.seller.id).then(response => {
      response = response.body;
      if (response.errno === 0) {
        // this.seller = response.data;
        this.seller = Object.assign({}, this.seller, response.data);
      }
    });
  },
  watch: {
    $route(to, from) {
      const toDepth = to.path.substr(1);
      const fromDepth = from.path.substr(1);
      const SELLER = 2;
      const RATINGS = 1;
      const GOODS = 0;
      // console.log(toDepth)
      const toVal =
        toDepth === "seller" ? SELLER : toDepth === "ratings" ? RATINGS : GOODS;
      const fromVal =
        fromDepth === "seller" ? SELLER : fromDepth === "ratings" ? RATINGS : GOODS;
      this.transitionName = toVal > fromVal ? "slide-left" : "slide-right";
      // console.log(toVal , fromVal,this.transitionName)
    }
  },

  components: {
    "v-header": header
  }
};
</script>

<style lang='scss'>
@import "./common/scss/mixin.scss";

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
          // box-shadow: 0 0 1px #000 inset;
        }
      }
    }
  }
  .view {
    transition: all 0.8s cubic-bezier(0.55, 0, 0.1, 1);
  }
  .slide-left-enter {
    transform: translateX(100%);
  }

  .slide-left-leave-active {
    transform: translateX(-100%);
  }
  .slide-right-enter {
    transform: translateX(-100%);
  }

  .slide-right-leave-active {
    transform: translateX(100%);
  }
}
</style>
