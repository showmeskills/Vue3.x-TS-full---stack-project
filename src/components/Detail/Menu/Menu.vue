<template>
  <div class="menu"  v-if="recommend">
    <!-- 推荐 -->
    <div class="menu_recommend" 
      v-for="recom in recommend" :key="recom.id"
    >
      <h3 class="recommend_title">{{recom.name}}</h3>
      <div class="recommend_view">
        <ul class="view_slide flex">
          <li class="slide_item" v-for="item in recom.items" :key="item.id">
            <img class="item_img" :src="$formatImgSrc(item.image_path)" />
            <p class="item_name t_ellipsis">{{item.name}}</p>
            <p class="item_rating">
              <span>月售{{item.month_sales}}份</span>-
              <span v-if="item.satisfy_rate">好评{{item.satisfy_rate}}%</span>
            </p>
            <div class="item_count flex" v-for="food in item.specfoods" :key="food.item_id">
              <span class="count_price">{{count >0? (food.price * count).toFixed(2):(food.price.toFixed(2))}}</span>
              <!-- 加减购物车按钮 -->
              <Button :count="count(item)" @changeNum="changeNum($event,{item:item,cid:'recommend'})"></Button>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <!-- 菜单列表 -->
    <div class="menu_main flex" :style="{height:con_height}" ref="menu_main" v-if="menu" >
      <ul class="main_class">
        <li class="class_item" ref="menu_con" v-for="(item,i) in menu" :key="item.id"
        :class="menu_index === i? 'class_active' : ''"
        @click="menuSelector(i)"
        >
          <p>
            <img :src="$formatImgSrc(item.icon_url)" v-if="item.icon_url" alt="" />
            <span>{{item.name}}</span>
          </p>
          <!-- 分类的加购数量 -->
          <span class="item_countClass" v-if="cateCount(rst.id,item.id)">{{cateCount(rst.id,item.id)}}</span>
        </li>
      </ul>
      <!-- 菜单主体 -->
      <div class="main_con grow_shrink" ref="main_con">
        <div class="slideWrap">
          <dl class="con_item" v-for="item in menu" :key="item.id">
            <!-- 分类标题 -->
            <dt class="item_title">
              <span class="title_main">{{item.name}}</span>
              <span class="title_desc">{{item.description}}</span>
            </dt>
            <!-- 主体内容 -->
            <dd class="item_desc flex" v-for="food in item.foods" :key="food">
              <img class="desc_img" :src="$formatImgSrc(food.image_path)" alt="" />
              <div class="desc_main">
                <h3 class="main_name t_ellipsis">{{food.name}}</h3>
                <p class="main_rating t_ellipsis">
                  <span>月售{{food.month_sales}}份</span>
                  <span>好评率{{food.satisfy_rate}}%</span>
                </p>
                <p class="main_discount" v-if="food.activity">
                  <span class="discount_icon">{{food.activity.benefit_text}}</span>
                  <span>单元定价</span>
                </p>
                <div class="main_btn flex">
                  <div class="btn_price">
                    <span class="price">{{count>0?(food.specfoods[0].price * count).toFixed(2):food.specfoods[0].price}}</span>
                    <span class="originPrice" v-if="food.specfoods[0].origin_price">{{food.specfoods[0].origin_price}}</span>
                  </div>
                    <!-- 加减购物车按钮 -->
                    <Button :count="count(food)" @changeNum="changeNum($event,{item:food,cid:item.id})"></Button>
                </div>
              </div>
            </dd>
          </dl>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang='ts'>
import { defineComponent } from "vue";
import useMenu from "@/hooks/useMenu";
import Button from "@/components/Button/Button.vue";
import useChangeNum from "@/hooks/useMenu/changeNum";
export default defineComponent({
  name: "Menu",
  components:{
    Button
  },
  setup() {
     const {
      recommend,
      menu,
      con_height,
      menuSelector,
      menu_index,
      cateCount,
      rst
    } = useMenu();
    const {count,changeNum} = useChangeNum();
    return {
      recommend,
      menu,
      con_height,
      menuSelector,
      menu_index,
      changeNum,
      count,
      cateCount,
      rst
    };
  },
});
</script>
<style lang="scss">
.menu {
  .menu_recommend {
    margin-top: 0.533333rem; //20
    .recommend_title {
      margin-left: 0.4rem; //15
      padding: 0.266666rem 0; //10
      font-size: 0.586666rem; //22
      font-weight: 700;
    }
    .recommend_view {
      overflow-x: auto;
      overflow-y: hidden;
      &::-webkit-scrollbar {
        display: none;
      }
      &::-moz-scrollbar {
        display: none;
      }
      .view_slide {
        .slide_item {
          width: 3.2rem; //120
          margin-left: 0.4rem; //15
          .item_img {
            display: block;
            width: 100%;
            border-radius: 0.16rem 0.16rem 0 0; //6
          }
          .item_name {
            margin-top: 0.213333rem;
            font-size: 0.373333rem; //14
          }
          .item_rating {
            margin-top: 0.213333rem; //8
            color: #777;
          }
          .item_count {
            justify-content: space-between;
            .count_price {
              font-size: 0.426666rem; //16
              line-height: 0.8rem; //30
              color: #fb5050;
              &::before {
                content: "￥";
              }
            }
          }
        }
      }
    }
  }
  .menu_main {
    margin-top: 0.8rem; //30
    // 菜单列表
    .main_class {
      height: 100%;
      overflow: auto;
      .class_item {
        position: relative;
        width: 2.266666rem; //85
        height: 1.333333rem; //50
        display: table;
        overflow: hidden;
        p {
          display: table-cell;
          padding: 0 0.266666rem; //10
          vertical-align: middle;
        }
        img,
        span {
          display: inline-block;
          vertical-align: middle;
        }
        img {
          width: 0.666666rem; //25
          height: 0.666666rem;
        }
        span {
          font-size: 0.373333rem; //14
          margin-left: 0.16rem; //6
        }
        .item_countClass {
          position: absolute;
          right: 0;
          top: 0;
          padding: 0.053333rem 0.106666rem; //2 4
          color: #fff;
          border-radius: 0.8rem; //30
          background-color: #f34545;
          font-size: 0.32rem; //12
        }
      }
      .class_active {
        font-weight: 700;
        background-color: #dfdfdf;
      }
    }
    // 菜单主体内容
    .main_con {
      position: relative;
      height: 100%;
      overflow: auto;
      .con_item {
        // 分类标题
        padding: 0 0.266666rem; //10
        .item_title {
          line-height: 0.64rem; //24
          .title_main {
            font-size: 0.373333rem; //14
            font-weight: 600;
            margin-right: 0.133333rem; //5
          }
          .title_desc {
            color: #999;
          }
        }
        // 商品主要描述
        .item_desc {
          margin-top: 0.533333rem; //20
          .desc_img {
            width: 2.533333rem; //95
            height: 2.533333rem;
            border-radius: 0.106666rem; //4
          }
          .desc_main {
            margin-left: 0.266666rem; //10
            overflow: hidden;
            flex-grow: 1;
            flex-shrink: 1;
            & > * {
              margin-top: 0.266666rem;
            }
            .main_name {
              font-size: 0.373333rem; //14
              font-weight: 700;
            }
            .main_rating {
              color: #aaa;
            }
            .main_discount {
              color: #f07373;
              .discount_icon {
                padding: 0 0.053333rem; //2
                border: 1px solid #f1f1f1;
                border-radius: 0.08rem; //3
              }
            }
            .main_btn {
              line-height: 0.533333rem; //20
              justify-content: space-between;
              .btn_price {
                color: #f07373;
                .price {
                  font-size: 0.426666rem; //16
                  // line-height:26px;
                  &::before {
                    content: "￥";
                  }
                }
                .originPrice {
                  &::before {
                    content: "￥";
                  }
                  text-decoration: line-through;
                  color: #ccc;
                }
              }
            }
          }
        }
      }
    }
  }
}
</style>