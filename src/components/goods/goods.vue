<template>
<div class="goods">
    
    <div class="menu-wrapper" ref="menu-wrapper">
        <ul>
            <li v-for="(item,index) in goods" class="menu-item" :class="{'current':(currentIndex===index)}" @click="selectMenu(index,$event)">
                <span class="text  border-1px">
                    <!-- <div>{{(index)}}</div> -->
                    <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
                    {{item.name}}
                </span>
            </li>
        </ul>
    </div>

    <div class="food-wrapper" ref="food-wrapper">
        <ul>
            <li v-for="item in goods" class="food-list food-list-hook">
                <h1 class="title">{{item.name}}</h1>
                <ul>
                    <li v-for="food in item.foods" class="food-item border-1px" @click="selectedFood(food,$event)" >
                        <div class="icon">
                            <img :src="food.icon" width="57" height="57">
                        </div>
                        <div class="content">
                            <h2 class="name">{{food.name}}</h2>
                            <p class="desc">{{food.description}}</p>
                            <div class="extra">
                                <span>月售{{food.sellCount}}</span>
                                <span class="rating">好评率{{food.rating}}%</span>
                            </div>
                            <div class="price">
                                <span class="now">￥{{food.price}}</span>
                                <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                            </div>
                            <div class="cartcontrol-wrapper">
                              <cartcontrol :food="food" @getTarget = "getTargetEle"></cartcontrol>
                            </div>   
                        </div>
                    </li>
                </ul>
            </li>
        </ul>
    </div>
    <shopcart :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice" ref="shopcart"></shopcart>
    <food :food="selectFood" ref="food" @getTarget = "getTargetEle"></food>
</div>
</template>

<script>
// import IScroll from 'iscroll';
import BScroll from "better-scroll";
import shopcart from "../shopcart/shopcart";
import cartcontrol from "../cartcontrol/cartcontrol";
import food from "../food/food";
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0,
      selectFood:{}
    };
  },
  created() {
    this.$http.get("./api/goods").then(response => {
      response = response.body;
      if (response.errno === 0) {
        this.goods = response.data;
      // console.log(this.goods instanceof Array)
        
        this.$nextTick(() => {
          this._initScroll();
          this._computedHeight();
        });
      }
    });
    this.classMap = ["decrease", "discount", "special", "invoice", "guarantee"];
  },
  // updated(){
  //     this.currentIndex();
  // },
  methods: {
    _initScroll() {
      //   console.log(this.$refs['menu-wrapper'])
      this.menuScroll = new BScroll(this.$refs["menu-wrapper"], {
        click: true
      });
      this.foodScroll = new BScroll(this.$refs["food-wrapper"], {
        click:true,
        probeType: 3
      });
      this.foodScroll.on("scroll", pos => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _computedHeight() {
      let foodList = this.$refs["food-wrapper"].getElementsByClassName(
        "food-list-hook"
      );
      let height = 0;
      this.listHeight = [];
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      }
      //   console.log(this.scrollY);
    },
    selectMenu(index,e) {
      let foodList = this.$refs["food-wrapper"].getElementsByClassName(
        "food-list-hook"
      );

      let target = foodList[index];
      this.foodScroll.scrollToElement(target, 300);
      console.log(e.target)
    },
    getTargetEle(that){
      this._drop(that)
    },
    _drop(target){
      this.$nextTick(()=>{
        this.$refs.shopcart.drop(target)
      })
    },
    selectedFood(food,event){
      this.selectFood = food;
      this.$refs.food.show();
    }
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        //   console.log(index)
        if (
          !this.listHeight[i + 1] ||
          (this.scrollY >= this.listHeight[i] &&
            this.scrollY < this.listHeight[i + 1])
        ) {
          //   console.log(this.currentIndex);
          return i;
        }
      }
      //   console.log(this.scrollY);
    },
    selectFoods(){
      let foods=[];
      // console.log(this.goods instanceof Array)
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if(food.count){
            foods.push(food);
          }
        })
      });
      return foods;
    },

  },
  components: {
    shopcart,
    cartcontrol,
    food
  }
};
</script>

<style lang="scss">
@import "../../common/scss/mixin.scss";
.goods {
  display: flex;
  position: absolute;
  width: 100%;
  top: 174px;
  bottom: 50px;
  overflow: hidden;
  .menu-wrapper {
    // 等分 类容缩放 占位
    flex: 0 0 80px;
    width: 80px;
    background: #f3f5f7;
    .menu-item {
      display: table;
      height: 54px;
      width: 56px;
      padding: 0 12px;
      line-height: 14px;
      &.current {
        position: relative;
        margin-top: -1px;
        z-index: 10;
        font-size: 700;
        background: #fff;
        .text {
          @include border-none();
        }
      }
      .icon {
        display: inline-block;
        vertical-align: top;
        width: 12px;
        height: 12px;
        margin-right: 2px;
        background-size: 12px 12px;
        background-repeat: no-repeat;

        &.decrease {
          @include bg-image("decrease_3");
        }

        &.discount {
          @include bg-image("discount_3");
        }

        &.guarantee {
          @include bg-image("guarantee_3");
        }

        &.invoice {
          @include bg-image("invoice_3");
        }

        &.special {
          @include bg-image("special_3");
        }
      }
      .text {
        display: table-cell;
        width: 56px;
        vertical-align: middle;
        font-size: 12px;
        @include border-1px(rgba(7, 17, 27, 0.1));
      }
    }
  }
  .food-wrapper {
    flex: 1;
    .title {
      padding-left: 14px;
      height: 26px;
      line-height: 26px;
      font-size: 12px;
      border-left: 2px solid #d9dde1;
      color: rgb(147, 153, 159);
      background: #f3f5f7;
    }
    .food-item {
      display: flex;
      margin: 18px;
      padding-bottom: 18px;
      // position: relative;
      @include border-1px(rgba(7, 17, 27, 0.1));
      &:last-child {
        @include border-none();
        margin-bottom: 0;
      }
      .icon {
        flex: 0 0 57px;
        margin-right: 10px;
      }
      .content {
        flex: 1;
        .name {
          margin: 2px 0 8px 0;
          height: 14px;
          line-height: 14px;
          font-size: 14px;
          color: rgb(7, 17, 27);
        }
        .extra {
          line-height: 10px;
          font-size: 10px;
          color: rgb(147, 153, 159);
          .rating {
            margin-left: 12px;
          }
        }
        .desc {
          @extend .extra;
          margin-bottom: 8px;
          line-height: 14px;
        }

        .price {
          font-weight: 700;
          line-height: 24px;
          .now {
            margin-right: 8px;
            color: rgb(240, 20, 20);
            font-size: 14px;
          }
          .old {
            text-decoration: line-through;
            color: rgb(147, 153, 159);
          }
        }
        .cartcontrol-wrapper{
          position: absolute;
          right: 0;
          bottom: 12px;
        }
      }
    }
  }
}
</style>
