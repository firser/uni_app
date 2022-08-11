<template>
  <view class="my-search-container" :style="{'background-color':bgcolor}" @click="searchBoxHandler">
    <!-- 使用 view 组件模拟 input 输入框的样式 -->
    <view class="my-search-box" :style="{'border-radius':radius+'px'}">
      <uni-icons type="search" size="17"></uni-icons>
      <text class="placeholder">搜索</text>
    </view>
  </view>
</template>
<script>
  export default {
    //封装自定义属性
    props:{
      //背景颜色
      bgcolor:{
        type:String,
        default:'#C00000'
      },
      //圆角尺寸
      radius:{
        type:Number,
        default:18//默认18px
      }
      
      
    },
    data() {
      return {};
    },
    methods:{
      searchBoxHandler(){
        console.log('1111111')
        this.$emit('click')//通过$emit()来触发cate页面 给组件绑定的click事件
      }
    }
  }
</script>
<style lang="scss">
  .my-search-container {
    //background-color: #c00000;
    height: 50px;
    padding: 0 10px;
    display: flex;
    align-items: center;

    .my-search-box {
      height: 36px;
      background-color: #ffffff;
     // border-radius: 15px;
      width: 100%;
      display: flex;
      align-items: center;//纵向居中
      justify-content: center;

      .placeholder {
        font-size: 15px;
        margin-left: 5px;

      }

    }

  }
</style>
