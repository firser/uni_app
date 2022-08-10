<template>
  <view>
    <view class="scroll-view-container">
      <!-- 左侧的滑动区-->
      <scroll-view scroll-y="true" class="left-scroll-view" :style="{height:wh+'px'}">
        <block v-for="(item,i) in cateList" :key="i">
          <view :class="['left-scrool-view-item',i===active ? 'active' : '']" @click="activeChanged(i)">
            {{item.cat_name}}
          </view><!-- 如果i(索引)等于active的值就给它绑定active样式的类 -->

        </block>


      </scroll-view>
      <!-- 右侧的滑动区-->
      <scroll-view scroll-y="true" class="right-scroll-view" :style="{height:wh+'px'}" :scroll-top="scrollTop">
        <view class="cate-lv2" v-for="(item2,i2) in cateLevel2" :key="i2">
          <!-- 渲染2级分类的标题-->
          <view class="cate-lv2-title">/{{item2.cat_name}}/</view>
          <!-- 当前2级分类下的3级分类列表-->
          <view class="cate-lv3-list">
            <view class="cate-lv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <!-- 三级分类的图片-->
              <image :src="item3.cat_icon"></image>
              <!-- 三级分类的文本-->
              <text>{{item3.cat_name}}</text>

            </view>

          </view>

        </view>


      </scroll-view>

    </view>

  </view>
</template>

<script>
  export default {
    data() {
      return {
        //当前设备可用高度
        wh: 0,
        // 分类列表数据
        cateList: [],
        //设置激活项 也就是小红条
        active: 0,
        // 二级分类列表
        cateLevel2: [],
        scrollTop:0

      };
    },
    onLoad() {
      const sysInfo = uni.getSystemInfoSync() //用来获取手机的参数 同步手机的高度 详情间uni-app官网 API 设备
      this.wh = sysInfo.windowHeight
      // 调用获取分类列表数据的方法
      this.getCateList()
    },
    methods: {
      //定义获取分类列表数据的方法
      async getCateList() {
        // 发起请求
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        // 判断是否获取失败
        if (res.meta.status !== 200) return uni.$showMsg()
        // 转存数据
        this.cateList = res.message
        // 为二级分类赋值    因为默认索引为0 所以先获取第一个的二级分类
        this.cateLevel2 = res.message[0].children
        //uni.$showMsg('数据获取成功')
      },
      //为一级分类的 Item 项(也就是最左侧)绑定点击事件处理函数 activeChanged
      activeChanged(i) {
        this.active = i
        //重新为2级分类赋值
        this.cateLevel2 = this.cateList[i].children
        //给scrollTop动态赋值 若值为0就给它赋1 就这样0和1之间切换赋值 从而达到按下1级列表后2级会在页面最上面显示
        this.scrollTop=this.scrollTop===0 ? 1:0 
      },
      //跳转到商品列表页面
      gotoGoodsList(item3){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?cid='+item3.cat_id
        })
      }
    }
  }
</script>

<style lang="scss">
  .scroll-view-container {
    display: flex;

    .left-scroll-view {
      width: 120px;

      .left-scrool-view-item {
        line-height: 60px;
        background-color: #f7f7f7;
        text-align: center;
        font-size: 12px;

        &.active {
          /* 表示既包含left-scrool-view-item类  和包含cative类 */
          background-color: #ffffff;
          position: relative;

          &::before {
            /* 这是在前面加一个红条 */
            content: ' ';
            display: block;
            width: 3px;
            height: 30px;
            background-color: #C00000;
            position: absolute;
            top: 50%;
            left: 0px;
            transform: translateY(-50%);
            /* 往回撤自己的50% */

          }

        }

      }
    }
  }

  //2级标题的样式
  .cate-lv2-title {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }

  //美化3级分类的样式
  .cate-lv3-list {
    display: flex;
    flex-wrap: wrap; //布满后换行

    .cate-lv3-item {
      width: 33.33%;
      margin-bottom: 10px;
      display: flex;  
      flex-direction: column;
      align-items: center; //横向居中
      justify-content: center; //纵向居中   
    
      image{
        width: 60px;
        height: 60px;
      }
      text {
        font-size: 12px
      }
    }
  }
</style>
