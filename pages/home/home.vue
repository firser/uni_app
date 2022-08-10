<template>
  <view>
    <!-- 轮播图区域   输入uSwiper会出来轮播图的框架-->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">

      <swiper-item v-for="(item,i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 导航区域-->
    <view class="nav-list">
      <view class="nav-item" v-for="(item,i) in navList" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>
    <!-- 楼层区域-->
    <view class="floor-list">
      <!-- 楼层item项-->
      <view class="floor-item" v-for="(item,i) in floorList" :key="i">
        <!-- 楼层标题-->
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!--  楼层的图片区域-->
        <view class="floor-img-box">
          <!-- 左侧大图片的盒子-->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width+'rpx'}"
              mode="widthFix"></image>

          </navigator>
          <!-- 右侧四个小图片的盒子-->  
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2,i2) in item.product_list" :key="i2" v-if="i2!==0" :url="item2.url">
              <image :src="item2.image_src" :style="{width: item2.image_width + 'rpx'}" mode="widthFix"></image>
              
            </navigator>

          </view>
        </view>
      </view>
    </view>


  </view>
</template>

<script>
  export default {
    data() {
      return {
        //这是轮播图的数据列表
        swiperList: [],
        //分类导航数据列表
        navList: [],
        //楼层的数据
        floorList: [],

      };
    },
    onLoad() {
      //调用方法获取轮播图的数据
      this.getSwiperList()
      //调用获取导航数据的方法
      this.getNavList(),
        //调用获取楼层数据的方法
        this.getFloorList()
    },
    methods: {
      //定义获取轮播图数据的方法
      async getSwiperList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/swiperdata')
        //请求失败
        if (res.meta.status !== 200) return uni.$showMsg()
        this.swiperList = res.message
        //uni.$showMsg('数据请求成功')

      },
      //定义获取导航数据的方法
      async getNavList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/catitems')
        //请求失败  
        if (res.meta.status !== 200) return uni.$showMsg()
        //请求成功
        this.navList = res.message
        //uni.$showMsg('导航数据请求成功')
      },
      // 定义nav-item 项被点击时候的事件处理函数
      navClickHandler(item) {
        //切换到分类页面
        if (item.name === '分类') { //分类属于tabBar页面
          uni.switchTab({
            url: '/pages/cate/cate'
          })

        }

      },
      //定义获取楼层列表数据的方法
      async getFloorList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/home/floordata')
        //请求失败
        if (res.meta.status !== 200) return uni.$showMsg()
        //对数据进行处理  要处理的原因就是商品信息的url和包的路径不一样 所以要和goods_list路径拼接起来
        res.message.forEach(floor =>{//这一个是拿到楼层
          floor.product_list.forEach(prod =>{//prod就是商品的信息 prod指向的就是每个图片的信息对象
          prod.url='/subpkg/goods_list/goods_list?'+prod.navigator_url.split('?')[1]//以?进行分割 所以为1的就是要拼接上去的数据项
          })
        })
        this.floorList = res.message
        //uni.$showMsg('数据请求成功')

      },

    }

  }
</script>

<style lang="scss">
  /* 轮播图样式*/
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  /* 导航样式*/
  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15px 0;

    .nav-img {
      width: 128rpx;
      height: 140rpx;
    }
  }

  /* 美化楼层标题的样式*/
  .floor-title {
    height: 60rpx;
    width: 100%;
    display: flex;
  }
  /* 楼层内图片样式*/
  .right-img-box {
    display: flex;
    flex-wrap: wrap;/* 横向对齐 */
    justify-content: space-around;/* 分散对齐 */
  }
  
  .floor-img-box {
    display: flex;
    padding-left: 10rpx;
  }
</style>
