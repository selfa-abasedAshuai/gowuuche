<template>
  <div class="number-container d-flex justify-content-center align-items-center">
    <!-- 减 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="sub">-</button>
    <!-- 购买的数量 -->
    <span class="number-box">{{num}}</span>
    <!-- 加 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="add">+</button>
  </div>
</template>

<script>
  import bus from '@/components/eventBus.js'
export default {
  props:{
    //接收商品的id值，将来使用EventBus方案，
    //把数量传递到APP.vue的时候，需要通知APP组件更新哪个商品的数量
    id:{
      type:Number,
      required:true
    },
    // 接收到的num值
    num: {
      type:Number,
      default:1
    }
  },

  methods:{
    //点击按钮，数量+1
    add(){
      //要发送给APP的数据格式为{id,value},其中value是商品数量
      const obj = {id: this.id, value: this.num + 1}
      //接下来要通过EventBus把obj对象发给APP.vue组件
      bus.$emit('share',obj)
    },
    sub(){
      if(this.num-1===0){
        return
      }
      //要发送给APP的数据格式为{id,value},其中value是商品数量
      const obj = {id: this.id, value: this.num - 1}
      //接下来要通过EventBus把obj对象发给APP.vue组件
      bus.$emit('share',obj)
    }

  }
}
</script>

<style lang="less" scoped>
.number-box {
  min-width: 30px;
  text-align: center;
  margin: 0 5px;
  font-size: 12px;
}

.btn-sm {
  width: 30px;
}
</style>
