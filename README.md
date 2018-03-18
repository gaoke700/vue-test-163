# vue-test-163

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).



项目说明：
1.安装 vue 脚手架
  npm i vue-cli -g
2.创建基于 webpack 的 vue 项目
  vue init webpack vue-test-163
  进入 vue-test-163 目录 安装依赖
  npm i
3.修改项目下 config/index.js 端口为8089 启动项目 
  npm run dev
4.本项目使用vux 所以要先安装
  npm i -S vux
5.vux 的 组件 ViewBox 要求 html,body 和最外面的容器样式
  html,body {
    magin: 0;
    width: 100%;
    height: 100%;
    overflow-x: hidden;
  }
  #app {
    height: 100%;
  }
6.vux 使用 less 写的 所以要安装想换依赖 并声明 style 所使用的语言为 less 还要引入vux 自己的初始化样式文件
  npm i -S less less-loader
  安装完成重启服务
  style文件内导入reset.less为：
  <style lang="less">
    @import '~vux/src/styles/reset.less';
    html,body {
      magin: 0;
      width: 100%;
      height: 100%;
      overflow-x: hidden;
    }
    #app {
      height: 100%;
    }
  </style>
7.vux 要安装自己的语言包并配置
   npm i -D vux-loader
   配置build/webpack.dev.conf.js
   const vuxLoader = require('vux-loader')
   利用vuxLoader提供的merge方法合并扩展baseWebpackConfig (默认baseWebpackConfig为const声明 不允许更改 要修改为let 或 var)
   baseWebpackConfig = vuxLoader.merge(baseWebpackConfig,{plugins:['vux-ui']})
   修改完配置重启服务
8.vux 里的 tab 组件 默认里面的 tab-item 为自动平均分配 tab 总宽度 可自定义 tab 宽度
9.动态数据使用网易提供接口并进行筛选，接口地址为
  http://3g.163.com/touch/jsonp/sy/recommend/0-9.html
10.数据获取使用 vue-jsonp 插件
  npm i -S vue-jsonp
  使用的时候需要在main.js(入口文件)里引入并使用插件
  import VueJsonp from 'vue-jsonp'
  Vue.use(VueJsonp);
11.下拉刷新 和 上拉加载 使用 第三方插件 vue-scroller  vux自带scroller有bug
   同样 安装 
   npm i -S vue-scroller
   在入口文件引入 使用
   import VueScroller from 'vue-scroller'
   Vue.use(VueScroller);
   
   贴出组件内容：
   <scroller
        :on-refresh="pullDownFresh"         //上拉刷新 
        refresh-text="拉一拉更健康"
        :on-infinite="pullupFresh"          //下拉加载
        ref="myRef"
      ></scroller>
   这个鬼东西  数据加载完成 他还不知道  我勒个擦 你得去告诉他完成了 不然那个刷新的菊花标志一直转呀转
   要告诉它 在组件里使用 ref="myRef" 属性引用插件对象实例
   然后使用 this.$refs.myRef.finishPullToRefresh(); //告诉插件我刷新完了
   
   这个下拉加载更坑 因为页面加载初始化会触发上拉动作 所以你还要给他个开关 防止他一上来就加载 影响逻辑
   先给定一个 初始化变量  initDataFinished = false;
   在函数
   pullupFresh(){
    if(!initDataFinished) {  //检测下，如果还没初始化完 就return 
      this.$refs.myRef.finishInfinite();  //进到这里面说明初始化完成了 还得用这句告诉他初始化完成了 我擦 不然它的就不会执行了
      return;
    }
   }
