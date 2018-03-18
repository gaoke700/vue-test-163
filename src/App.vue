<template>
  <div id="app">
    <view-box>
      <!--头部-->
      <x-header
        slot="header"
        class="header"
        :left-options="{showBack:false}"
      >
        <div slot="left">直播</div>
        <div>网易</div>
        <div slot="right">搜索</div>
      </x-header>
      <!--tab-->
      <!--
      scroller：滚动回弹效果
      面必须包含一个div
      默认x、y轴都可以
      -->
      <sc
        :lock-y="true"
      >
        <div
          class="tab"
        >
          <tab>
            <tab-item selected>推荐</tab-item>
            <tab-item>要闻</tab-item>
            <tab-item>游戏</tab-item>
            <tab-item>科技</tab-item>
            <tab-item>娱乐</tab-item>
          </tab>
        </div>
      </sc>
      <scroller
        class="scroller"
        :on-refresh="pullDownFresh"
        refresh-text="拉一拉更健康"
        :on-infinite="pullupFresh"
        ref="myRef"
      >
        <!--swiper-->
        <swiper
          :list="swiperList"
          height="180px"
          :auto="true"
          :loop="true"
          :interval="3000"
          :duration="500"
          :threshold="100"
        ></swiper>
        <!--Marquee-->
        <marquee
          :interval="3000"
        >
          <!--模拟数据-->
          <!--
          <marquee-item>
            111111111
          </marquee-item>
          <marquee-item>
            222222222
          </marquee-item>
          <marquee-item>
            3333333333
          </marquee-item>
          -->
          <marquee-item
            class="marquee-item"
            v-for="item in marqueeList"
          >
            {{item.title}}
          </marquee-item>
        </marquee>
        <!--list-->
        <panel
          :list="dataList2"
          style="margin: 0px"
        ></panel>
        <!--tabbar-->
      </scroller>
      <tabbar slot="bottom">
        <tabbar-item>
          <img src="./assets/icon-nav-index.png" slot="icon">
          <span slot="label">首页</span>
        </tabbar-item>
        <tabbar-item>
          <img src="./assets/icon-nav-index.png" slot="icon">
          <span slot="label">推荐</span>
        </tabbar-item>
        <tabbar-item>
          <img src="./assets/icon-nav-index.png" slot="icon">
          <span slot="label">我的</span>
        </tabbar-item>

      </tabbar>
    </view-box>
    <router-view/>
  </div>
</template>

<script>
import { ViewBox,XHeader,Tabbar,TabbarItem,Tab,TabItem,Scroller as sc,
  Swiper,Marquee, MarqueeItem,Panel} from 'vux';
/*
//轮播模拟数据
const swiperList = [{
  url: 'javascript:',
  img: 'https://static.vux.li/demo/1.jpg',
  title: '送你一朵fua'
}, {
  url: 'javascript:',
  img: 'https://static.vux.li/demo/2.jpg',
  title: '送你一辆车'
}, {
  url: 'javascript:',
  img: 'https://static.vux.li/demo/5.jpg',
  title: '送你一次旅行',
  fallbackImg: 'https://static.vux.li/demo/3.jpg'
}];
*/
/*
//panel内容模拟数据
const dataList1 = [{
  src: 'http://somedomain.somdomain/x.jpg',
  fallbackSrc: 'http://placeholder.qiniudn.com/60x60/3cc51f/ffffff',
  title: '标题一',
  desc: '由各种物质组成的巨型球状天体，叫做星球。星球有一定的形状，有自己的运行轨道。',
  url: '/component/cell'
  }, {
  src: 'http://placeholder.qiniudn.com/60x60/3cc51f/ffffff',
  title: '标题二',
  desc: '由各种物质组成的巨型球状天体，叫做星球。星球有一定的形状，有自己的运行轨道。',
  url: {
    path: '/component/radio',
    replace: false
  },
  meta: {
    source: '来源信息',
    date: '时间',
    other: '其他信息'
  }
}];
*/

const loadParams = ['A','B01','B02','B03','B04','B05','B06',
  'B07','B08','B09','B010'];
let loadParamsIndex = 0;
let keyValue = 'A';

let star = 0,
    end = star + 9;
let initDataFinished = false;

export default {
  name: 'App',
  components: {
    ViewBox,
    XHeader,
    Tabbar,
    TabbarItem,
    Tab,
    TabItem,
    sc, //和第三方的插件重名，故重命名下
    Swiper,
    Marquee,
    MarqueeItem,
    Panel
  },
  data(){
    // 模拟数据
    // var dataList2 = [];
    // for (let i=0;i<5;i++){
    //   dataList2.push(...dataList1)
    // }

    return {
      // swiperList,
      // dataList2
      swiperList : [],
      dataList2 : [],
      marqueeList: []
    }
  },
  created() {
    //163提供的数据地址 http://3g.163.com/touch/jsonp/sy/recommend/0-9.html
    //swiper
    this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html').then(
      data => {
        // console.log(data)
        this.swiperList = data.focus.filter(item => {
          return item.addata === null;  //筛选广告为空的数据
        }).map( item => {
          return {
            url: item.link,
            img: item.picInfo[0].url,
            title: item.title
          }
        })
      }
    )
    //panel
    this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html').then(
      data => {
        // console.log(data)
        this.dataList2 = data.list.filter(item => {
          return item.addata === null && item.picInfo.length > 0;
          //筛选广告为空的数据 和 有图片信息的数据
        }).map( item => {
          return {
            url: item.link,
            src: item.picInfo[0].url,
            title: item.title,
            desc: item.digest
          }
        })
      }
    )

    //marquee
    this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html').then(
      data => {
        // console.log(data)
        this.marqueeList = data.live.filter(item => {
          return item.addata === null && item.picInfo.length > 0;
          //筛选广告为空的数据 和 有图片信息的数据
        }).map( item => {
          return {
            title: item.title
          }
        })
        initDataFinished = true;
      }
    )
  },
  methods: {
    pullDownFresh(done){
      // console.log(1)
      this.getRefreshKey();
      this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html',{
        miss:'00',
        refresh: keyValue
      }).then(data => {
        // console.log(data)
        this.dataList2 = data.list.filter(item => {
          return item.addata === null && item.picInfo[0];
          //筛选广告为空的数据 和 有图片信息的数据
        }).map( item => {
          return {
            url: item.link,
            src: item.picInfo[0].url,
            title: item.title,
            desc: item.digest
          }
        })

        // console.log(this.$refs.myRef)  //通过ref属性引用插件对象实例
        // this.$refs.myRef.finishPullToRefresh(); //告诉插件我刷新完了
        //上面这句可以用传入的done函数代替
        done();
        this.$vux.toast.text(`刷新了${this.dataList2.length}条数据`,'top')
      })
    },
    pullupFresh(){

      if(!initDataFinished) {
        this.$refs.myRef.finishInfinite();
        //页面加载会触发上拉动作,需要开关控制 initDataFinished为false 数据初始化完成后改为true
        return;
      }
      console.log(2)

      this.$jsonp(`http://3g.163.com/touch/jsonp/sy/recommend/${star}-${end}.html`,{
        miss:'00',
        refresh: keyValue
      }).then(data => {
        // console.log(data)
        var moreData = (data.list.filter(item => {
          return item.addata === null && item.picInfo[0];
          //筛选广告为空的数据 和 有图片信息的数据
        }).map( item => {
          return {
            url: item.link,
            src: item.picInfo[0].url,
            title: item.title,
            desc: item.digest
          }
        }))

        this.dataList2.push(...moreData)
        //更新参数
        star += 10;
        end = star + 9;
        this.$refs.myRef.finishInfinite(); //告诉插件我刷新完了
        this.$vux.toast.text(`加载了${moreData.length}条数据`,'bottom')
      })
    },
    getRefreshKey(){  //更改刷新参数
      loadParamsIndex ++;
      if(loadParamsIndex == loadParams.length){
        loadParamsIndex = 0;
      }
      keyValue = loadParams[loadParamsIndex]
    }
  }
}
</script>

<style lang="less">
  @import "~vux/src/styles/reset.less";
  html,body{
    margin: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
  }
  #app {
    height: 100%;

    .header {
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      z-index: 9;
    }

    .tab {
      margin: 46px 0 0 0 ;
      width: 960px;
    }

    .marquee-item {
      padding: 4px;
      box-sizing: border-box;
    }

    .scroller{
      top: 90px;
    }

    /*更改默认组件样式，直接进行覆盖*/
    .weui-media-box__hd,.weui-media-box__hd img {
      width: 102px;
      height: 78px;
    }
    .weui-media-box__bd {
      height: 78px;
    }
    .loading-layer{
      padding-bottom: 64px;
    }
  }
</style>
