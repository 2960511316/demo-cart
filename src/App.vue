<template>
  <div class="app-container">
    <!-- 头部标题 -->
    <Header title="结算页面"></Header>

    <!-- <p> {{fullState}} </p> -->
    <!-- <p> {{amt}} </p> -->

    <!-- 渲染的每一个商品 -->
    <Goods
      v-for="item in list"
      :key="item.id"
      :title="item.goods_name"
      :pic="item.goods_img"
      :price="item.goods_price"
      :state="item.goods_state"
      :id="item.id"
      :count="item.goods_count"
      @state-change="getNewState"
    ></Goods>

    <!-- Footer部分 -->
    <Footer
      :isfull="fullState"
      :all="toall"
      :amount="amt"
      @full-change="getFullchange"
    ></Footer>

    <h1>App 根组件</h1>
  </div>
</template>

<script>
import axios from "axios";

// import Header from '@/components/Header/Header.vue'   @ 需要配置
import Header from "./components/Header/Header.vue";
import Goods from "./components/Goods/Goods.vue";
import Footer from "./components/Footer/Footer.vue";
import bus from "./components/eventBus.js";

export default {
  components: {
    Header,
    Goods,
    Footer,
  },
  methods: {
    async initCartList() {
      const { data: res } = await axios.get("https://www.escook.cn/api/cart");

      if (res.status === 200) {
        this.list = res.list;
      }
    },
    getNewState(val) {
      // console.log(4);
      // console.log(val);
      this.list.some((item) => {
        if (item.id === val.id) {
          item.goods_state = val.value;

          return true;
        }
      });
    },
    getFullchange(val) {
      // console.log(val);
      // console.log(6);
      this.list.forEach((item) => (item.goods_state = val));
    },
  },
  data() {
    return {
      list: [],
    };
  },
  created() {
    // console.log(this);
    this.initCartList();

    bus.$on("share", (val) => {
      // console.log( "app的", val);
      this.list.some((item) => {
        if (item.id === val.id) {
          item.goods_count = val.value;
          return true;
        }
      });
    });
  },
  // 计算属性
  computed: {
    fullState() {
      return this.list.every((item) => item.goods_state === true);
    },
    amt() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce((totar, item) => {
          return (totar += item.goods_price * item.goods_count);
        }, 0);
    },
    toall() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce((t, item) => {
          return (t += item.goods_count);
        }, 0);
    },
  },
};
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
