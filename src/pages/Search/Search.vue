<template>
  <section class="search">

    <!--HeaderTop-->
    <v-HeaderTop title="搜索"></v-HeaderTop>

    <!--搜索的表单-->
    <form class="search_form" @submit.prevent="search">
      <input type="search" name="search" placeholder="请输入商家或美食名称" class="search_input" v-model="keyword">
      <input type="submit" name="submit" class="search_submit" value="搜索">
    </form>

    <!--搜索的列表-->
    <section class="list" >
      <!--<ul class="list_container">-->
      <!--<router-link :to="{path:'/shop', query:{id:item.id}}" tag="li"-->
      <!--v-for="item in searchShops" :key="item.id" class="list_li">-->
      <!--<section class="item_left">-->
      <!--<img :src="imgBaseUrl + item.image_path" class="restaurant_img">-->
      <!--</section>-->
      <!--<section class="item_right">-->
      <!--<div class="item_right_text">-->
      <!--<p>-->
      <!--<span>{{item.name}}</span>-->
      <!--</p>-->
      <!--<p>月售 {{item.month_sales||item.recent_order_num}} 单</p>-->
      <!--<p>{{item.delivery_fee||item.float_minimum_order_amount}} 元起送 / 距离{{item.distance}}</p>-->
      <!--</div>-->
      <!--</section>-->
      <!--</router-link>-->
      <!--</ul>-->
    </section>

    <!--<div class="search_none">很抱歉！无搜索结果</div>-->
  </section>

</template>

<script>
  import HeaderTop from '../../components/HeaderTop/HeaderTop.vue'
  import {mapState} from 'vuex'

    export default {
        data() {
            return {
              keyword:'',
              imgBaseUrl: 'http://cangdu.org:8001/img/',
            }
        },
      computed:{
        ...mapState(['searchShops'])
      },
      mounted(){

      },
      methods:{
        search(){
          const keyword = this.keyword.trim()
          if(keyword){
            this.$store.dispatch('searchShops', keyword)
          }
        }
      },
      components:{
          'v-HeaderTop':HeaderTop
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">

  @import "../../assets/stylus/mixins.styl"

  .search  //搜索
    width 100%
    overflow hidden
    .header
      background-color #02a774
      position fixed
      z-index 100
      left 0
      top 0
      width 100%
      height 45px
      .header_search
        position absolute
        left 15px
        top 50%
        transform translateY(-50%)
        width 10%
        height 50%
        .icon-sousuo
          font-size 25px
          color #fff
      .header_title
        position absolute
        top 50%
        left 50%
        transform translate(-50%, -50%)
        width 50%
        color #fff
        text-align center
        .header_title_text
          font-size 20px
          color #fff
          display block
      .header_login
        font-size 14px
        color #fff
        position absolute
        right 15px
        top 50%
        transform translateY(-50%)
        .header_login_text
          color #fff
    .search_form
      clearFix()
      margin-top 45px
      background-color #fff
      padding 12px 8px
      input
        height 35px
        padding 0 4px
        border-radius 2px
        font-weight bold
        outline none
        &.search_input
          float left
          width 79%
          border 4px solid #f2f2f2
          font-size 14px
          color #333
          background-color #f2f2f2
        &.search_submit
          float right
          width 18%
          border 4px solid #02a774
          font-size 16px
          color #fff
          background-color #02a774
</style>
