<template>
  <div class="goods-container">
    <!-- 左侧图片 -->
    <div class="thumb">
      <div class="custom-control custom-checkbox">
        <!-- 复选框 -->
        <!-- change事件是复选框自己本身就有的 -->
        <input type="checkbox" class="custom-control-input" :id="'cb' + id" :checked="state" 
        @change="stateChange" />
        <label class="custom-control-label" :for="'cb' + id">
          <!-- 商品的缩略图 -->
          <!-- 要给属性加动态绑定值需要v-bind，属性中不能用插值表达式 -->
          <img :src="pic" alt="" />
        </label>
      </div>
    </div>
    <!-- 右侧信息区域 -->
    <div class="goods-info">
      <!-- 商品标题 ,这里我们需要从外界传过来-->
      <h6 class="goods-title">{{ title }}</h6>
      <div class="goods-info-bottom">
        <!-- 商品价格 -->
        <span class="goods-price">{{price}}</span>
        <!-- 商品的数量 -->
        <Counter :num="count" :id="id"></Counter>
      </div>
    </div>
  </div>
</template>

<script>
import { ok } from 'assert';
import Counter from '@/components/Counter/Counter.vue'
export default {
  //父向子传值
  props: {
    // 子向父传商品id
    // 再这里封装一个id属性是为了将来子组件中商品的勾选状态发生变化后，
    //  需要通过子传父的形式通知父组件修改勾选状态。
    id: {
      required: true,
      type: Number
    },

    // 要渲染的商品的标题
    title: {
      //设置默认值
      default: '',
      //规定标题的类型
      type: String
    },

    //要渲染的商品图片
    pic: {
      default: '',
      //图片路径是个字符串
      type: String
    },
    price: {
      default: 0,
      type:Number
    },
    // 商品的勾选状态
    state: {
      default:true,
      type: Boolean
    },
    //商品的购买数量
    count: {
      type:Number,
      default:1
    }
  },
  methods: {
    //只要复选框的选中状态发生变化，就会调用这个处理函数
    stateChange(e) {
    //   console.log(e) 可以看到target中有checked属性
    const newState = e.target.checked
    // 在组件中，this是组件的实例对象，所以props接收的数据在methods中想调用就用this
    // 触发自定义事件
    this.$emit('state-change',{id: this.id,value: newState})
    }
  },
  components: {
    Counter
  }
}
</script>

<style lang="less" scoped>
.goods-container {
  + .goods-container {
    border-top: 1px solid #efefef;
  }
  padding: 10px;
  display: flex;
  .thumb {
    display: flex;
    align-items: center;
    img {
      width: 100px;
      height: 100px;
      margin: 0 10px;
    }
  }

  .goods-info {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    flex: 1;
    .goods-title {
      font-weight: bold;
      font-size: 12px;
    }
    .goods-info-bottom {
      display: flex;
      justify-content: space-between;
      .goods-price {
        font-weight: bold;
        color: red;
        font-size: 13px;
      }
    }
  }
}
</style>
