<template>
  <div>
    <div class="goods">
      <!--左侧分类-->
      <div class="menu-wrapper" ref="menuWrapper">
        <ul>
          <li class="menu-item" v-for="(good,index) in goods"  @click="clickMenu(index)" :key="index">
            <span class="text bottom-border-1px" >
            <img class="icon" :src="good.icon" v-if="good.icon">
              {{good.name}}
            </span>
          </li>
        </ul>
      </div>

      <!--右侧食物列表-->
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <li class="food-list-hook" v-for="(good,index) in goods" >
            <h1 class="title">{{good.name}}</h1>
            <ul>
              <li class="food-item bottom-border-1px" v-for="(food,index) in good.foods" @click="toggleFood(food)">
                <div class="icon">
                  <img width="57" height="57" :src="food.icon">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售 {{food.sellCount}} 份</span>
                    <span>好评率 {{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span>
                    <span class="old" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
                  </div>
                  <div class="cartcontrol-wrapper">
                    <CartControl :food="food"/>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <ShopCart></ShopCart>
    </div>

    <Food v-show="isShowFood" :food="food"></Food>
  </div>
</template>

<script>
  import {mapState} from 'vuex'
  import BScroll from 'better-scroll'
  import CartControl from '../../../components/CartControl/CartControl.vue'
  import Food from '../../../components/Food/Food.vue'
  import ShopCart from '../../../components/ShopCart/ShopCart.vue'

    export default {
        data() {
            return {
              scrollY: 0,
              tops: [],
              isShowFood: false,
              food:{}
            }
        },

        mounted(){
          this.$store.dispatch('getShopGoods',()=>{
              this._initScroll()
              this._initTops()
              this._slidingLogic()
          })
        },

        computed:{
          ...mapState(['goods'])
        },

        methods:{

          // 初始化tops
          _initTops(){
            let top = 0
            this.tops.push(0)
            const foodLis = document.getElementsByClassName('food-list-hook')
            Array.prototype.slice.call(foodLis).forEach(li => {
              top += li.clientHeight
              this.tops.push(top)
            })
            console.log(this.tops)
          },

          // 初始化better-scroll
          _initScroll(){
            // better-scroll的使用
              this.menuScroll = new BScroll(this.$refs.menuWrapper,{
                bounce:true,
                scrollY:true,
                click:true
              })
              this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
                bounce:true,
                scrollY:true,
                click:true,
                probeType: 2
              })

              // 绑定右侧滑动过程中的scroll监听
              this.foodsScroll.on('scroll',({x,y})=>{
                this.scrollY = Math.abs(y)
                this._slidingLogic()
              })

              // 绑定右侧滑动结束的scrollEnd监听
              this.foodsScroll.on('scrollEnd',({x,y})=>{
                this.scrollY = Math.abs(y)
                this._slidingLogic()
              })

            },

          // 滑动的逻辑
          _slidingLogic(){
            // 给右侧的食物列表绑定滑动监听，并实时监视scrollY的变化
              const {scrollY,tops} = this
              for (let i = 0; i < this.tops.length - 1; i++){
                const categorys = document.getElementsByClassName('menu-item')
                categorys[i].classList.remove('current')
                if ( this.tops[i] <= this.scrollY && this.scrollY < tops[i + 1]){
                  categorys[i].classList.add('current')
                }
              }
          },

          // 点击Menu
          clickMenu(index){
            const {scrollY,tops} = this
            const categorys = document.getElementsByClassName('menu-item')
            for (let i = 0; i < this.tops.length - 1; i++){
              categorys[i].classList.remove('current')
            }
            this.foodsScroll.scrollTo(0, -this.tops[index],300)
          },

          toggleFood(food){
            this.food = food
            this.isShowFood = !this.isShowFood
          },

        },

        components:{
          CartControl,
          Food,
          ShopCart
        }

    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../../assets/stylus/mixins2.styl"
  .goods
    display: flex
    position: absolute
    top: 240px
    bottom: 46px
    width: 100%
    background: #fff;
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        padding: 0 12px
        line-height: 14px
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
          background: #fff
          color: $green
          font-weight: 700
          .text
            border-none()
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
        .text
          display: table-cell
          width: 56px
          vertical-align: middle
          bottom-border-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147, 153, 159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        bottom-border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin: 2px 0 8px 0
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .desc
            line-height: 12px
            margin-bottom: 8px
          .extra
            .count
              margin-right: 12px
          .price
            font-weight: 700
            line-height: 24px
            .now
              margin-right: 8px
              font-size: 14px
              color: rgb(240, 20, 20)
            .old
              text-decoration: line-through
              font-size: 10px
              color: rgb(147, 153, 159)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 12px
</style>
