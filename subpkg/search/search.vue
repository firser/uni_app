<template>
  <view>
    <view class="search-box">
      <!-- 使用 uni-ui 提供的搜索组件 -->
      <uni-search-bar @confirm="search" @input="input" :radius="100" placeholder="请输入搜索内容" cancelButton="none">
      </uni-search-bar>

    </view>
    <!-- 搜索建议列表-->
    <view class="sugg-list" v-if="searchResults.length !== 0">
      <view class="sugg-item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrow-right" size="16"></uni-icons>
      </view>
    </view>
    <!-- 搜索历史-->
    <view class="history-box" v-else>
      <!-- 标题区域-->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="clean"></uni-icons>
      </view>
      <!-- 列表区域-->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item,i) in histories" :key="i" @click="gotoGoodsList(item)"></uni-tag>
      </view>
    </view>

  </view>
</template>

<script>
  export default {
    data() {
      return {
        timer: null,
        kw: '', //定义搜索的关键词
        // 搜索结果列表
        searchResults: [],
        // 搜索关键词的历史记录
        historyList: ['a', 'app', 'apple']

      };
    },
    onLoad() {
      //在页面加载的时候获取到储的值
      this.historyList=JSON.parse(uni.getStorageSync('kw') || '[]')//如果值不存在就获取一个[]
      
    },
    methods: {
      //定义一个input输入事件处理函数
      input(e) {
        //清空之前的延时器
        clearTimeout(this.timer)
        //定义延时器
        this.timer = setTimeout(() => {
          this.kw = e
          this.getSearchList()
        }, 500)
      },
      //根据搜索关键词  定义获取搜索关键字 搜索商品建议列表的方法
      async getSearchList() {
        //判断关键字是否为空
        if (this.kw === '') {
          this.searchResults = []
          return
        }
        //发起请求  获取搜索建议列表
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.kw
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message
        //在获取到的搜索建议后 调用saveSearchHistory()方法
        this.saveSearchHistory()
      },
      //定义点击item项跳转到商品详情页goods_detail的方法
      gotoDetail(item) {
        uni.navigateTo({
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      //定义保存搜索历史记录的方法
      saveSearchHistory(){
        //this.historyList.push(this.kw)
         // 1. 将 Array 数组转化为 Set 对象
         const set =new Set(this.historyList)
          // 2. 调用 Set 对象的 delete 方法，移除对应的元素
          set.delete(this.kw)
          // 3. 调用 Set 对象的 add 方法，向 Set 中添加元素
          set.add(this.kw)
          // 4. 将 Set 对象转化为 Array 数组
          this.historyList=Array.from(set)
          
          //对搜索历史数据 进行持久化的存储
          uni.setStorageSync('kw',JSON.stringify(this.historyList))//'kw'是键  后面是值
         
      },
      //声明点击清空搜索历史方法
      clean(){
        this.historyList=[]
        uni.setStorageSync('kw','[]')
        
      },
      //定义点击搜索历史跳转到商品列表页面的方法
      gotoGoodsList(kw){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?query='+kw
        })
      }
      
    },
   
      //声明一个计算属性  histories
    computed:{
      histories(){
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
  //配置吸顶效果
  .search-box {
    position: sticky;
    top: 0;
    z-index: 999;
  }

  //搜索建议列表样式
  .sugg-list {
    padding: 0 5px;

    .sugg-item {
      display: flex;
      align-items: center; //纵向居中
      justify-content: space-between; //两端贴边 等距
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;

      .goods-name {
        white-space: nowrap; // 文字不允许换行（单行文本）
        overflow: hidden; // 溢出部分隐藏
        text-overflow: ellipsis; // 文本溢出后，使用 ... 代替
        margin-right: 3px;
      }
    }
  }

  //搜索历史区域的样式渲染
  .history-box {
    padding: 0 5px;

    .history-title {
      display: flex;
      justify-content: space-between; //两端贴边排布
      align-items: center;
      height: 40px;
      font-size: 13px;
      border-bottom: 1px solid #efefef; 
    }

    .history-list {
      display: flex;
      flex-wrap: wrap;//允许换行
      .uni-tag {
        margin-top: 5px;
        margin-right: 5px;
      }
    }
  }
</style>
