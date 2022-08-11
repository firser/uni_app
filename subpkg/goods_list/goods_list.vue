<template>
  <view>
    <view class="goods-list">
      <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
        <my-goods :goods="goods"></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //请求参数对象
        queryObj: {
          query: '',
          cid: '',
          pagenum: 1, //默认请求第1页数据
          pagesize: 10 //默认一次请求10条数据
        },
        //商品列表的数据
        goodsList: [],
        total: 0,
        //通过节流阀防止发起额外的请求
        //是否正在请求数据
        isloading:false
        
      }
    },
    onLoad(options) { //options是页面跳转时所携带的参数
      //对页面跳转时所携带的参数进行接收
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      //console.log(this.queryObj)
      //调用获取商品数据列表的方法
      this.getGoodsList()
    },
    methods: {
      //定义获取商品列表数据的方法
      async getGoodsList(cb) {
        //打开节流阀
        this.isloading=true
        //发起请求
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        if (res.meta.status !== 200) return uni.$showMsg()
        //关闭节流阀
        this.isloading=false
         // 只要数据请求完毕，就立即按需调用 cb 回调函数
         cb&&cb()//外界可能会传cb也可能不会传  利用短路运算符来判断 如果cb这个值存在就进行调用  调用了就会关闭下拉刷新的效果 
        // 也就是把uni.stopPullDownRefresh() 这个回调函数调用了一下
         // 为数据赋值：通过展开运算符的形式，进行新旧数据的拼接
         this.goodsList=[...this.goodsList,...res.message.goods]
         this.total=res.message.total
         
      },
      //点击item项(这里是goods)跳转到商品的详情页
      gotoDetail(goods){
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id='+goods.goods_id
        })
      },
    },
    //用来监听页面的上拉触底行为
    onReachBottom() {
      // 判断是否还有下一页数据
      if(this.queryObj.pagenum * this.queryObj.pagesize>=this.total) return uni.$showMsg('数据加载完毕!')
      // 判断是否正在请求其它数据，如果是，则不发起额外的请求
      if(this.isloading) return
      //让页码值自增+1
      this.queryObj.pagenum+=1
      //页码值自增加1后 重新获取列表数据
      this.getGoodsList()
    },
    // 下拉刷新的事件
    onPullDownRefresh() {
      // 1. 重置关键数据
      this.queryObj.pagenum = 1
      this.total = 0
      this.isloading = false
      this.goodsList = []
      // 2. 重新发起请求
      this.getGoodsList(() => uni.stopPullDownRefresh())
    }
   
  }
</script>

<style lang="scss">
  
</style>
