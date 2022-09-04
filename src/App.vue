<template>
  <div class="app-container">
    <!-- header头部区域 -->
    <!-- <p>{{fullState}}</p>计算属性在声明的时候是函数，在调用的时候是属性 -->
    <Header></Header>
    <!-- <h1>App 根组件</h1> -->
    <!-- 循环渲染每一个商品的信息 -->
    <!-- 注意这里title前面如果不加v-bind传过去的就是个写死的字符串 -->
    <Goods 
    v-for="item in list" :key="item.id" 
    :id="item.id"
    :title="item.goods_name" 
    :pic="item.goods_img" 
    :price="item.goods_price" 
    :state="item.goods_state"
    :count="item.goods_count"
    @state-change="getNewState"
    ></Goods>
    <!-- Footer区 -->
    <!-- 计算属性在声明的时候是函数，在调用的时候是属性 -->
    <Footer :isFull="fullState" :amount="amt" :all="total" @full-change="getFullState"></Footer>
  </div>
</template>

<script>
  //导入axios请求库
  import axios from 'axios'

  // 导入需要的组件
  import Header from '@/components/Header/Header.vue'
  import Goods from '@/components/Goods/Goods.vue'
  import Footer from '@/components/Footer/Footer.vue'
  import bus from '@/components/eventBus.js'
  



  export default {
    data() {
      return {
        // 用来存储购物车的列表数据，默认为空数组
        list:[]
      }
    },

    // 计算属性,动态计算一个布尔值给Footer里面的复选框用
    computed: {
      fullState() {
        return this.list.every(item=>item.goods_state === true)
      },

      amt() {
      
      //先filter过滤，再reduce累加
      return this.list.
      filter(item => item.goods_state===true).
      reduce((total,item)=>(total += item.goods_price * item.goods_count),0)
      
      },
      //已勾选商品的总数量
      total() {
        return this.list.filter(item => item.goods_state).reduce((t,item) => (t +=item.goods_count),0)
      }
    },
    components: {
      Header,
      Goods,
      Footer
    },

    created() {
      //调用请求数据的方法
      // console.log(this)通过打印我们可以发现这里面有刚刚定义的方法
      this.initCartList()
      bus.$on('share',(val)=>{
        this.list.some(item=>{
          if(item.id===val.id){
            item.goods_count = val.value
            return true
          }
        })
      })
    },

    methods: {
      // 想要获取数据需要调用一个方法
      //方法用到了await，方法名前面就要用到async
      async initCartList() {
        //调用axios的get方法请求列表数据
        // data: res就是将data重命名为res
       const {data: res} = await axios.get('https://www.escook.cn/api/cart')
       // 只要是请求回来的数据，在页面渲染的时候要用到，就必须转存到data中
       if(res.status === 200) {
         this.list = res.list
       }
       console.log(res)
      },
     

      //接收父组件传递过来的数据
      getNewState(e) {
       
        // console.log(e)
        this.list.some(item => {
          if(item.id === e.id){
            item.goods_state = e.value
            // 终止后续的循环
            return true
          }
        })
      },
      // 接收Footer子组件传递过来的全选按钮的状态
      getFullState(e) {
        //这里的e是形参，写什么都可以，值为布尔值
        //接收到了全选的状态，对其他复选框进行操作
        this.list.forEach(item=>item.goods_state=e)
      }

    }

  }
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
