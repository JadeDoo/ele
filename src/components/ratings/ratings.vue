<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="deliveryTime-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @select="select" @toggle="toggleContent" :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="ratings"></ratingselect> 
      <div class="rating-wrapper">
        <ul>
          <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
            <div class="avatar">
              <img :src="rating.avatar" alt="" width="28" height="28">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime"></span>
              </div>
              <p class="text"><span v-if="rating.text">{{rating.text}}</span><span v-else>此用户暂未评价</span></p>
              <div class="recommend-wrapper" v-show="rating.recommend&&rating.recommend.length">
                <span class="iconfont" :class="{'icon-good':rating.rateType==0,'icon-bad':rating.rateType==1}"></span>
                <span v-for="recommend in rating.recommend" class="recommend">{{recommend}}</span>
              </div>
              <div class="time">
                {{rating.rateTime | formatDate}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import star from "../star/star";
import split from "../split/split";
import ratingselect from "../ratingselect/ratingselect";
import { formatDate } from "../../common/js/date";
import BScroll from "better-scroll";

const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      ratings: [],
      showFlag: false,
      selectType: ALL,
      onlyContent: false,
      desc: {
        all: "全部",
        positive: "推荐",
        negative: "吐槽"
      }
    };
  },
  created() {
    this.$http.get("./api/ratings").then(response => {
      response = response.body;
      if (response.errno === 0) {
        this.ratings = response.data;
      }
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.ratings, {
            click: true
          });
        }
      });
    });
  },
  filters: {
    formatDate: function(time) {
      let date = new Date(time);
      return formatDate(date, "yyyy-MM-dd hh:mm");
    }
  },
  methods: {
    select(type) {
      this.selectType = type;
    },
    toggleContent() {
      this.onlyContent = !this.onlyContent;
    },needShow(type, text) {
      if (this.onlyContent && !text) {
        return false;
      }
      if (this.selectType === ALL) {
        return true;
      } else {
        return type === this.selectType;
      }
    },
  },
  components: {
    star,
    ratingselect,
    split
  }
};
</script>

<style lang="scss">
@import "../../common/scss/mixin.scss";

.ratings {
  position: absolute;
  top: 174px;
  bottom: 0;
  width: 100%;
  left: 0;
  overflow: hidden;
  .overview {
    display: flex;
    padding: 18px 0;
    .overview-left {
      flex: 0 0 137px;
      width: 137px;
      border-right: 1px solid rgba(7, 17, 27, 0.1);
      text-align: center;
      @media only screen and (max-width: 320px) {
        flex: 0 0 120px;
        width: 120px;
      }
      .score {
        margin-bottom: 6px;
        line-height: 28px;
        font-size: 24px;
        color: rgb(255, 153, 0);
        padding-top: 6px;
      }
      .title {
        margin-bottom: 8px;
        line-height: 12px;
        font-size: 12px;
        color: rgb(7, 17, 27);
      }
      .rank {
        line-height: 10px;
        font-size: 10px;
        color: rgb(147, 153, 159);
        padding-bottom: 6px;
      }
    }
    .overview-right {
      flex: 1;
      padding: 6px 0 6px 24px;
      @media only screen and (max-width: 320px) {
        padding-left: 6px;
      }
      .score-wrapper {
        margin-bottom: 8px;
        font-size: 0;
        .title {
          display: inline-block;
          line-height: 18px;
          font-size: 12px;
          color: rgb(7, 17, 27);
        }
        .star {
          display: inline-block;
          vertical-align: top;
          margin: 0 12px;
        }
        .score {
          line-height: 18px;
          display: inline-block;
          font-size: 12px;
          vertical-align: top;
          color: rgb(255, 153, 0);
        }
      }
      .deliveryTime-wrapper {
        .title {
          margin-bottom: 8px;
          line-height: 12px;
          font-size: 12px;
          color: rgb(7, 17, 27);
        }
        .delivery {
          margin-left: 12px;
          font-size: 12px;
          color: rgb(147, 153, 159);
        }
      }
    }
  }
  .rating-wrapper {
    padding: 0 18px;
    .rating-item {
      display: flex;
      padding: 18px 0;
      @include border-1px(rgba(7, 17, 27, 0.1));
      .avatar {
        flex: 0 0 28px;
        width: 28px;
        margin-right: 12px;
        overflow: hidden;
        img {
          border-radius: 50%;
        }
      }
      .content {
        flex: 1;
        position: relative;
        .name {
          line-height: 12px;
          font-size: 10px;
          color: rgb(7, 17, 27);
          margin-bottom: 4px;
        }
        .star-wrapper {
          margin-bottom: 6px;
          font-size: 0;
          .star {
            display: inline-block;
            vertical-align: top;
            margin-right: 6px;
          }
          .delivery {
            display: inline-block;
            vertical-align: top;
            font-size: 10px;
            line-height: 12px;
            color: rgb(147, 153, 159);
          }
        }
        .text {
          margin-bottom: 8px;
          line-height: 18px;
          color: rgb(7, 17, 27);
          font-size: 12px;
        }
        .recommend-wrapper {
          line-height: 16px;
          font-size: 0;
          .icon-good,
          .recommend {
            margin: 0 8px 4px 0;
            display: inline-block;
            font-size: 10px;
          }
          .icon-good {
            color: rgb(0, 160, 220);
          }
          .recommend {
            padding: 0 6px;
            border: 1px solid rgba(7, 17, 27, 0.1);
            border-radius: 1px;
            color: rgb(147, 153, 159);
            background: #fff;
          }
        }
        .time {
          position: absolute;
          font-size: 10px;
          font-weight: 200;
          color: rgb(147, 153, 159);
          line-height: 12px;
          top: 0px;
          right: 0px;
        }
      }
    }
  }
}
</style>


