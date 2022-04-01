<template>
  <div class="sc-box">
    <div class="sc-type">
      <ul>
        <li :class="{active:currentIndex === index}" v-for="(item, index) in goodsList" :key="index" data-index="index" @click="toggleType(index)">{{item.type}}</li>
      </ul>
    </div>
    <div class="sc-content">
      <div class="sc-leftMenu" v-if="menu.length > 1">
        <ul>
          <li :class="{active:menuTypeIndex === index}" v-for="(item, index) in menu" :key="index" data-index="index" @click="toggleGoods(index)">{{item.menuType}}</li>
        </ul>
      </div>
      <div class="sc-goods">
        <ul>
          <li v-for="(item, index) in goods" :key="index" data-index="index">
            <img :class="'goods-image'+index" :src="item.imgSrc" alt="" />
            <div class="goods-mes">
              <div class="goods-name">{{item.name}}</div>
              <div class="goods-description">{{item.description}}</div>
              <div class="goods-price"><span>¥</span>{{item.price}}<span class="originalPrice">¥{{item.originalPrice}}</span></div>
              <div class="goods-buttons">
                <span class="button minus" @click="clickMinus(index)" v-if="item.number !== 0"><img src="../../assets/logo.png" alt="" /></span>
                <span class="number" v-if="item.number !== 0">{{item.number}}</span>
                <span class="button add" @click="clickAdd(index)"><img src="../../assets/logo.png" alt="" /></span>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="sc-operation">
      <div class="sc-contact">
        <img src="../../assets/logo.png" alt="" />
        <span>联系商家</span>
      </div>
      <div class="sc-deliveryMes">
        <div class="deliveryNumber"><img src="../../assets/logo.png" alt="" /><span v-if="count!==0">{{count}}</span></div>
        <div class="priceMes">
          <div class="totalPrice">¥{{totalPrice}}<span>¥{{originalTotalPrice}}</span></div>
          <div class="deliveryMes">另需配送费¥{{totalDeliveryCost}}<span v-if="totalDeliveryCost !== 0">¥{{totalOriginalDeliveryCost}}</span> 支持自取</div>
        </div>
      </div>
      <div class="sc-pay" @click="clickPay">去结算</div>
    </div>
  </div>
</template>
<script>
import $ from 'jquery'
export default {
  props: {},
  data() {
    return {
      currentIndex: 0, // 当前类型index
      menuTypeIndex: 0, // 左边菜单index
      goodsList: [{ // 整个商品数据
          type: '全部',
          menu: [{
            menuType: '推荐',
            goods: [{
                name: '排骨饭套餐',
                imgSrc: require('../../assets/logo.png'),
                description: '物美价廉',
                price: 15.50,
                originalPrice: 25.36,
                number: 0,
                deliveryCost: 1,
                originalDeliveryCost: 2,
              },
              {
                name: '宫保鸡丁套餐',
                imgSrc: require('../../assets/logo.png'),
                description: '物美价廉',
                price: 5.50,
                originalPrice: 25.36,
                number: 0,
                deliveryCost: 2,
                originalDeliveryCost: 4,
              }
            ]
          }, {
            menuType: '折扣',
            goods: [{
              name: '排骨饭套餐',
              imgSrc: require('../../assets/logo.png'),
              description: '物美价廉',
              price: 15.50,
              originalPrice: 25.36,
              number: 0,
              deliveryCost: 1,
              originalDeliveryCost: 2,
            }]
          }]
        },
        {
          type: '好评',
          menu: [{
            menuType: '推荐',
            goods: [{
              name: '排骨饭套餐',
              imgSrc: require('../../assets/logo.png'),
              description: '物美价廉',
              price: 15.50,
              originalPrice: 25.36,
              number: 0,
              deliveryCost: 1,
              originalDeliveryCost: 2,
            }]
          }]
        },
        {
          type: '买过',
          menu: [{
            menuType: '推荐',
            goods: [{
                name: '排骨饭套餐',
                imgSrc: require('../../assets/logo.png'),
                description: '物美价廉',
                price: 15.50,
                originalPrice: 25.36,
                number: 0,
                deliveryCost: 1,
                originalDeliveryCost: 2,
              },
              {
                name: '宫保鸡丁套餐',
                imgSrc: require('../../assets/logo.png'),
                description: '物美价廉',
                price: 5.50,
                originalPrice: 25.36,
                number: 0,
                deliveryCost: 2,
                originalDeliveryCost: 4,
              }
            ]
          }]
        }
      ],
      count: 0, // 总数量
      originalTotalPrice: 0, // 原总价
      totalPrice: 0, // 总价
      totalDeliveryCost: 0, // 优惠后总运费
      totalOriginalDeliveryCost: 0, // 总运费
      flag: true // 用来判断是否执行动画
    }
  },
  mounted() {},
  computed: {
    menu() {
      return this.goodsList[this.currentIndex].menu;
    },
    goods() {
      return this.menu[this.menuTypeIndex].goods;
    }
  },
  methods: {
    toggleType(index) {
      this.currentIndex = index;
      this.menuTypeIndex = 0; // 默认menu第一个，解决非第一个切换时报错
    },
    toggleGoods(index) {
      this.menuTypeIndex = index;
    },
    clickMinus(index) {
      this.goods[index].number -= 1;
      this.count -= 1;
      // 价格变化
      this.originalTotalPrice -= parseFloat(this.goods[index].originalPrice); // 原总价增加
      this.totalPrice -= parseFloat(this.goods[index].price); // 优惠总价增加
      this.totalDeliveryCost -= this.goods[index].deliveryCost; // 优惠总配送费增加
      this.totalOriginalDeliveryCost -= this.goods[index].originalDeliveryCost; // 优惠总配送费增加
    },
    clickAdd(index) {
      if (this.flag) {
        this.flag = false;
        this.goods[index].number += 1;
 
        let that = this;
        // 动画跳动效果
        let count = that.count + 1;
        let originalTotalPrice = parseFloat(that.originalTotalPrice) + parseFloat(that.goods[index].originalPrice); // 原总价增加
        let totalPrice = parseFloat(that.totalPrice) + parseFloat(that.goods[index].price); // 优惠总价增加
        let totalDeliveryCost = that.totalDeliveryCost + that.goods[index].deliveryCost; // 优惠总配送费增加
        let totalOriginalDeliveryCost = that.totalOriginalDeliveryCost + that.goods[index].originalDeliveryCost; // 优惠总配送费增加
        let $initImg = $('.goods-image' + index); // 被复制的图片对象
        let $targetLocation = $('.deliveryNumber img'); // 目标购物车的图片对象
        let $moveImg = $initImg.clone().css({ // 生成点击添加行的图片副本，并变成圆形
          'position': 'absolute',
          'z-index': 99,
          'width': $initImg.width() * 0.5,
          'height': $initImg.height() * 0.5,
          'border-radius': '50%'
        }).css($initImg.offset()).appendTo('body'); // 并把位移到图片位置且添加到body
        $moveImg
          .animate({ // 先匀速向左上
            left: $initImg.offset().left - 30,
            top: $initImg.offset().top - 50
          }, 200, 'linear')
          .animate({ // 然后慢慢移到目标位置
            left: $targetLocation.offset().left + $targetLocation.width() - $moveImg.width() * 1.5,
            top: $targetLocation.offset().top + $targetLocation.height() - $moveImg.height() * 1.5
          }, 600, () => {
            $moveImg.fadeOut(100, () => { // 最后慢慢消失
              $moveImg.detach(); // 删除掉移动对象$moveImg
              that.count = count;
              // 价格变化
              that.originalTotalPrice = originalTotalPrice.toFixed(2);
              that.totalPrice = totalPrice.toFixed(2);
              that.totalDeliveryCost = totalDeliveryCost;
              that.totalOriginalDeliveryCost = totalOriginalDeliveryCost;
 
              this.flag = true;
            });
          })
      };
    },
    clickPay() {
      console.log(this.totalPrice, this.totalDeliveryCost);
    }
  }
}
 
</script>
<style lang="less" scoped>
.sc-box {
  position: relative;
  margin: 20px;
  width: 375px;
  height: 667px;
  background-color: #fff;
 
  .sc-type {
    li {
      display: inline-block;
      background-color: #f5f5f5;
      color: #616161;
      margin-right: 10px;
      height: 32px;
      line-height: 32px;
      width: 80px;
      text-align: center;
      border-radius: 8px;
      cursor: pointer;
      transition: all 0.3s;
 
      &.active {
        background-image: linear-gradient(to right, #fedb39, #febb2e);
        color: #000;
        font-weight: bold;
      }
    }
  }
 
  .sc-content {
    margin-top: 30px;
    height: 546px;
    display: flex;
    overflow-y: auto;
 
    .sc-leftMenu {
      height: 100%;
      width: 80px;
      text-align: center;
      background-color: #f5f9fc;
      color: #616161;
 
      li {
        cursor: pointer;
        transition: all 0.3s;
        height: 50px;
        line-height: 50px;
 
        &.active {
          background-color: #fff;
          font-weight: bold;
          color: #000;
        }
      }
    }
 
    .sc-goods {
      margin: 0 10px;
      width: 100%;
 
      li {
        position: relative;
        display: flex;
        margin-bottom: 15px;
 
        img {
          width: 70px;
          height: 70px;
          border-radius: 8px;
        }
 
        .goods-mes {
          margin-left: 8px;
 
          .goods-name {
            font-size: 16px;
            font-weight: bold;
          }
 
          .goods-description {
            margin-top: 6px;
            color: #616161;
            font-size: 12px;
          }
 
          .goods-price {
            margin-top: 10px;
            color: #ff6262;
            font-size: 16px;
 
            span {
              font-size: 12px;
 
              &.originalPrice {
                color: #b4b4b4;
                margin-left: 4px;
                text-decoration: line-through;
              }
            }
          }
 
          .goods-buttons {
            position: absolute;
            bottom: 0;
            right: 0;
 
            .button {
              display: inline-block;
              width: 20px;
              height: 20px;
              line-height: 20px;
              text-align: center;
              border-radius: 50%;
              cursor: pointer;
 
              img {
                width: 10px;
                height: 10px;
              }
            }
 
            .minus {
              border: 1px solid #d0d0d0;
            }
 
            .number {
              display: inline-block;
              margin: 0 5px;
            }
 
            .add {
              background-image: linear-gradient(to right, #fedb39, #febb2e);
            }
          }
        }
      }
    }
  }
 
  .sc-operation {
    position: absolute;
    bottom: 0;
    width: 100%;
    font-size: 12px;
    color: #949494;
    display: flex;
 
    .sc-contact {
      display: flex;
      flex-direction: column;
      padding: 10px 10px 10px 15px;
      background-color: #000;
      border-top-left-radius: 30px;
      border-top-right-radius: 5px;
      border-bottom-right-radius: 5px;
      border-bottom-left-radius: 30px;
 
      img {
        width: 20px;
        height: 20px;
        margin: 0 auto 3px;
      }
    }
 
    .sc-deliveryMes {
      margin-left: 3px;
      padding-right: 15px;
      background-color: #000;
      display: flex;
      border-top-left-radius: 5px;
      border-bottom-left-radius: 5px;
      align-items: center;
 
      .deliveryNumber {
        position: relative;
 
        img {
          position: absolute;
          top: -58px;
          left: -18px;
          width: 100px;
          height: 100px;
        }
 
        span {
          display: inline-block;
          width: 20px;
          height: 20px;
          line-height: 20px;
          text-align: center;
          border-radius: 50%;
          background-color: #ff6262;
          color: #fff;
          position: absolute;
          left: 35px;
          top: 0;
        }
      }
 
      .priceMes {
        margin-left: 66px;
 
        .totalPrice {
          color: #fff;
          font-size: 16px;
          margin-bottom: 3px;
 
          span {
            color: #949494;
            margin-left: 3px;
            font-size: 12px;
            text-decoration: line-through;
          }
        }
 
        .deliveryMes {
          span {
            text-decoration: line-through;
            margin: 0 6px 0 3px;
          }
        }
      }
    }
 
    .sc-pay {
      font-size: 14px;
      font-weight: bold;
      color: #000;
      line-height: 59px;
      flex: 1;
      text-align: center;
      background-image: linear-gradient(to right, #fedb39, #febb2e);
      border-top-right-radius: 30px;
      border-bottom-right-radius: 30px;
      cursor: pointer;
    }
  }
}
 
</style>