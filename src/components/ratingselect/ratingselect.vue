<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span @click="select(2,$event)" class="block all" :class="{'active':selectType===2}">{{desc.all}}<span>{{ratings.length}}</span></span>
      <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span>{{positive.length}}</span></span>
      <span @click="select(1,$event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span>{{negative.length}}</span></span>
    </div>
    <div class="switch" @click="toggleContent($event)" :class="{'on':onlyContent}">
      <span class="iconfont icon-dui" ></span>
      <span class="text">只看有内容评价</span>
    </div>
  </div>
</template>
<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;
export default {
  props: {
    ratings: {
      type: Array,
      default() {
        return [];
      }
    },
    selectType: {
      type: Number,
      default: 2
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default() {
        return {
          all: "全部",
          positive: "满意",
          negative: "不满意"
        };
      }
    }
  },
  computed: {
    positive() {
      return this.ratings.filter(rating => {
        return rating.rateType === POSITIVE;
      });
    },
    negative() {
      return this.ratings.filter(rating => {
        return rating.rateType === NEGATIVE;
      });
    }
  },
  methods: {
    select(type, event) {
      this.$emit("select", type);
    },
    toggleContent(event) {
      this.$emit("toggle");
    }
  }
};
</script>

<style lang="scss">
@import "../../common/scss/mixin.scss";
.ratingselect {
  .rating-type {
    padding: 18px 0;
    margin: 0 18px;
    @include border-1px(rgba(7, 17, 27, 0.1));
    font-size: 0;
    .block {
      display: inline-block;
      padding: 8px 12px;
      margin-right: 8px;
      border-radius: 1px;
      font-size: 12px;
      color: rgb(77, 85, 93);
      line-height: 16px;
      font-weight: 700;
      // text-align: center;
      &.active {
        color: #fff;
        background: rgb(0, 160, 220);
      }
      .count {
        margin-left: 2px;
        font-size: 8px;
      }
      &.all {
        background: rgba(0, 160, 220, 0.2);
        &.active {
          background: rgb(0, 160, 220);
        }
      }
      &.positive {
        background: rgba(0, 160, 220, 0.2);
        &.active {
          background: rgb(0, 160, 220);
        }
      }
      &.negative {
        background: rgba(77, 85, 93, 0.2);
        &.active {
          background: rgb(77, 85, 93);
        }
      }
    }
  }
  .switch {
    padding: 12px 18px;
    line-height: 24px;
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
    color: rgb(147, 153, 159);
    font-size: 0;
    &.on {
      .iconfont {
        color: #00c850;
      }
      .text{
        color: rgb(7, 17, 27);        
      }
    }
    .iconfont {
      display: inline-block;
      vertical-align: top;
      margin-right: 4px;
      font-size: 24px;
    }
    .text {
      display: inline-block;
      vertical-align: top;
      font-size: 12px;
    }
  }
}
</style>
