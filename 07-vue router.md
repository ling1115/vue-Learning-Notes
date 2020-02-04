[TOC]
Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：
  - 嵌套的路由/视图表
  - 模块化的、基于组件的路由配置
  - 路由参数、查询、通配符
  - 基于 Vue.js 过渡系统的视图过渡效果
  - 细粒度的导航控制
  - 带有自动激活的 CSS class 的链接
  - HTML5 历史模式或 hash 模式，在 IE9 中自动降级
  - 自定义的滚动条行为

#### vue-router插件使用---路由基础
```
1. router.js
    import Vue from 'vue'
    import Router from 'vue-router'

    import Foo from './pages/Foo'
    import Bar from './pages/Bar'

    <!-- 让vue项目可以使用路由的功能 -->
    Vue.use(Router)
    
    <!-- 配置路由匹配切换组件的规则 -->
    const ruotes = [
        {
            path:'/foo',
            component: Foo
        },
        {
            path:'/bar',
            component: Bar
        }
    ]

    const router = new  Router({
        routes
    })

    export default router
2. main.js:
    import router from './router.js'

    new Vue({
        <!-- 配置路由 -->
        router,
        render: h => h(App)
    }).$mount('#app')
3. App.vue:
    <div id="app">
        <!-- 匹配到的路由 装载在这里 -->
        <router-view/>
    </div>
```
#### router-link使用
```
1. App.vue:
    1. html:
        <div id="app">
            <a href="#/bar">bar</a>
            <a href="#/foo">foo</a>
            <br/>
            <router-link to="/bar">bar</router-link>
            <router-link to="/foo">foo</router-link>
            <!-- 路由容器 -->
            <router-view/>
        </div>
    2. css:
        .router-link-active{
            background: yellow;
        }
to属性值为匹配路径
与普通a标签相比，在选中的标签中 自动添加两个样式：router-link-exact-active  router-link-active=>一般选用这个来添加active样式，因为在子路由中，共同存在这个样式
```
#### 嵌套路由--子路由配置
```
1. App.vue：
    1. html:
        <div id="app">
            <!-- 页面 -->
            <router-view/>
            <!-- tab切换 -->
            <nav class="tabs">
                <router-link class="tab" to="/bar">首页</router-link>
                <router-link class="tab" to="/foo">发现</router-link>
                <router-link class="tab" to="/fun">设置</router-link>
            </nav>
        </div>
    2. css
        .tabs{
            width: 100%;
            height: 49px;
            position: absolute;
            left: 0;
            bottom: 0;
            display: flex;
            background: #eee;
        }
        .tabs .tab{
            flex: 1;
            text-align: center;
            line-height: 49px;
            color: #333;
        }
        .tabs .tab.router-link-active{
            color: powderblue;
            font-weight: bold;
            color: brown;
        }
        .page{
            width: 100%;
            position: absolute;
            left: 0;
            top: 0;
            bottom: 49px;
        }
        <!-- 让子页面覆盖底部Tabs,并且提高权重 -->
        .page.subpage{
            bottom: 0;
            z-index: 5;
        }
```
```
2. router.js:
    import Vue from 'vue'
    import Router from 'vue-router'

    import Bar from './pages/Bar'
    import Foo from './pages/Foo'
    import Fun from './pages/Fun'

    import Location from './pages/subpage/Location'
    import Search from './pages/subpage/Search'
    import Detail from './pages/subpage/Detail'

    // 为了让vue项目能够使用路由的功能
    Vue.use(Router);
    const routes = [
    //配置路由切换组件的规则
    {
        path: '/bar',
        component: Bar,
        <!-- 配置子路由:不需要/，因为在路由当中/代表根路径，子路由是依赖前面的一个路径来定位的 -->
        children: [
        {
            path: 'location',
            component: Location
        },
        {
            path: 'search',
            component: Search
        },
        {
            path: 'detail',
            component: Detail
        }
        ]
    },
    {
        path: '/foo',
        component: Foo
    },
    {
        path: '/fun',
        component: Fun
    }
    ];

    const router = new Router({
    routes
    });

    export default router;
```
```
3. reset.css:
    *{margin: 0;padding: 0;}
    li{list-style: none;}
    a{text-decoration: none;color: #000;}
    img{display: block;}
4. style.css:
    html, body, #app{width: 100%;height: 100%;}
```
```
5. Bar.vue:
    1. html:
        <div>
            <div class="bar page">

                <header class="header">
                    <router-link class="header-btn btn-left" to="/bar/location">定位</router-link>
                    <h1 class="title">首页</h1>
                    <router-link class="header-btn btn-right" to="/bar/search">搜索</router-link>
                </header>

                <div class="content">

                    <ul class="list">
                        <li class="item" v-for="item in goodsList" :key="item.id">
                            <router-link to="/bar/detail/">
                            {{item.name}}
                            </router-link>
                        </li>
                    </ul>

                </div>
            </div>
            <!-- 装载子路由页面：每个子页面都需要动画进入 弹出，所以在装载它们的router-view  用transition包裹起来 -->
            <transition enter-active-class="slideInRight" leave-active-class="slideOutRight">
                <router-view/>
            </transition>

        </div>
    2. js:
        export default {
            data(){
                return {
                    goodsList: [
                        {id: 1, name: '上衣'},
                        {id: 2, name: '外套'},
                        {id: 3, name: '鞋子'},
                        {id: 4, name: '袜子'},
                        {id: 5, name: '牛仔裤'},
                        {id: 6, name: '羽绒服'},
                        {id: 7, name: '上衣1'},
                        {id: 8, name: '外套2'},
                        {id: 9, name: '鞋子1'},
                        {id: 10, name: '袜子2'},
                        {id: 11, name: '牛仔裤2'},
                        {id: 12, name: '羽绒服1'}
                    ]
                }
            }
        }
    3. css
        .bar{background: pink;}
        .slideInRight, .slideOutRight{animation-duration: 300ms;}

        .header{width: 100%;height: 44px;background: #eee;}

        .header .title{width: 100%; height: 100%; text-align: center; line-height: 44px; font-size: 16px;
            font-weight: bold; color: salmon;}

        .header .header-btn{ line-height: 14px; font-size: 14px; padding: 10px;  border: 1px solid salmon;
            position: absolute;  top: 5px;  border-radius: 4px; color: salmon; }

        .header-btn.btn-left{left: 5px;}

        .header-btn.btn-right{right: 5px;}

        .content{width: 100%; position: absolute; top: 44px; bottom: 0; overflow: auto; }

        .content .list .item{ line-height: 60px; padding-left: 10px; box-sizing: border-box;
            border-bottom: 1px solid #ddd;  }
```
```
6. Loaction.vue: 其他子页面一样
    <div class="location page subpage">
        <h1>定位页面</h1>
    </div>

    .location{
        background: darkgreen;
    }
```
#### 动态路由 传值 
> 在这里是非父子组件，属于父页面和子页面，但在这里不可以使用非父子传值方式进行传值，因为这种传值方式的要点就是先监听后触发
子页面监听，父页面触发，但是子页面只有在点击实现路由跳转之后才能创建并且监听，所以不能实现先监听后触发
```
1. Bar.vue:
    <li class="item" v-for="item in goodsList" :key="item.id">
        <!-- <router-link :to="'/bar/detail/'+item.id+'/'+item.name"> -->
        <router-link :to="'/bar/detail?id='+item.id+'&name='+item.name">
        {{item.name}}
        </router-link>
    </li>
```
```
第一种正向路由方向传值方法：父页面传子页面
    配置子路由为动态路由：path: 'detail/:id/:title', 动态路由用于父页面传值给子页面的
    详情页获取路由传值：this.$route.param.参数
    子页面传值给父页面可以使用 $on  $emit方法
第二种：search方式传值
    子路由：path:'detail',
    Bar.vue:中的路由跳转：<router-link :to="'/bar/detail?id='+item.id+'&name='+item.name">
    
    详情页获取路由传值：this.$route.query.参数
```
#### 命名路由  name=''
```
1. router.js:
    const routes = [
        //配置路由切换组件的规则
        {
            //命名路由
            name: 'home',
            path: '/bar',
            component: Bar,
            //配置子路由
            children: [
            {
                name: 'subpage1',
                path: 'location',
                component: Location
            },
            {
                name: 'subpage2',
                path: 'search',
                component: Search
            },
            {
                name: 'subpage3',
                // path: 'detail/:id/:title',
                path: 'detail',
                component: Detail
            }
            ]
        },
        {
            name: 'discover',
            path: '/foo',
            component: Foo
        },
        {
            name: 'setting',
            path: '/fun',
            component: Fun
        }
    ];
```
```
2. Bar.vue:
    <header class="header">
        <!-- 两种写法等价 -->
        <!-- <router-link class="header-btn btn-left" to="/bar/location">定位</router-link> -->
        <!-- <router-link class="header-btn btn-left" :to="{path: '/bar/location'}">定位</router-link> -->
        <router-link class="header-btn btn-left" :to="{name: 'subpage1'}">定位</router-link>
        <h1 class="title">首页</h1>
        <!-- <router-link class="header-btn btn-right" to="/bar/search">搜索</router-link> -->
        <!-- <router-link class="header-btn btn-right" :to="{path: '/bar/search'}">搜索</router-link> -->
        <router-link class="header-btn btn-right" :to="{name: 'subpage2'}">搜索</router-link>
    </header>

    <div class="content">
        <ul class="list">
            <li class="item" v-for="item in goodsList" :key="item.id">
                <!-- <router-link :to="'/bar/detail/'+item.id+'/'+item.name"> -->

                <!-- <router-link :to="'/bar/detail?id='+item.id+'&name='+item.name"> -->

                <!-- 命名路由的方式切换页面 -->
                <!-- <router-link :to="{name: 'subpage3', params: {id: item.id, title: item.name}}"> -->
                <router-link :to="{name: 'subpage3', query: {id: item.id, title: item.name}}">

                {{item.name}}
                </router-link>
            </li>
        </ul>
    </div>
```
#### 编程式导航：使用js切换路由
> 在router-link切换页面时，是vue-router插件内部将router-link转为a标签
使用js切换路由，js操作路由叫做编程式导航

> 在编程式导航中，对象给我们提供了以下方法操作路由对象
  - router.push()     进入新的路径
  - router.replace()  使用新的路径替换当前路径
  - router.go(n)      跳转到第几页，n的值为正整数（前进），n的值为负整数（后退） 
  - router.back()     等价于router.go(-1)
  - router.forword()  等价于router.go(1)

```
1. App.vue:
    1. html:
        <!-- 页面 -->
        <router-view/>
        <!-- tab切换 -->
        <nav class="tabs">
            <!-- <router-link class="tab" :to="{name: 'home'}">首页</router-link>
            <router-link class="tab" :to="{name: 'discover'}">发现</router-link>
            <router-link class="tab" :to="{name: 'setting'}">设置</router-link> -->
            <li class="tab" v-for="(item, index) in tabList" :key="item.id" @click="tabAction(index)">
                {{item.title}}
            </li>
        </nav>
    2. js:
        export default {
            data(){
                return {
                    tabList: [
                        {id: 1, title: '首页', path: '/bar', routeName: 'home'},
                        {id: 2, title: '发现', path: '/foo', routeName: 'discover'},
                        {id: 3, title: '设置', path: '/fun', routeName: 'setting'}
                    ]
                }
            },
            methods: {
                tabAction(index){
                    console.log('点击了：', this.tabList[index].title);
                    // 路由对象
                    console.log(this.$router);

                    // 使用路由对象切换页面
                    let pathStr = this.tabList[index].path;

                    // this.$router.push(pathStr);
                    this.$router.replace({path: pathStr});

                    let routeName = this.tabList[index].routeName;
                    // this.$router.push({name: routeName});

                }
            }
        }
```
> this.$route => 路由信息对象

> this.$router => 路由对象

#### router 重定向 别名 
```
path:'/',
<!-- 重定向 -->
redirect:'/bar',
<!-- 别名 -->
alias:'/b',
<!-- 将动态路由中的参数 作组件的props属性传值给组件 -->
<!-- 布尔模式传参 -->
props:true,
<!-- 对象传参，适合设置静态属性 -->
props:{
    value:'hello'
}
<!-- 函数模式传参：通过函数运算，return结果 -->
props(){
    return {
        list:[1,2,3]
    }
}
在子组件中使用props接受路由参数：props:['id','title']  props:['value']  props:['list']

####router history模式
router.js:
    const router = new Router({
        <!-- history需要后台配置,因为history模式下在刷新时会返回can not get的错误 -->
        mode:'history',
        routes
    })
```

> 使用node搭建后台服务器---->处理history模式下的刷新问题,
新建文件夹：web-server/server.js
在web-server文件夹下：npm init => npm i express -S
```
1. web-server/server.js:
    const express = require('express');

    const server = express();

    server.use('/js', express.static('./www/js'));
    server.use('/css', express.static('./www/css'));

    server.get('/', (req, res)=>{
        res.sendFile(__dirname+'/www/index.html');
    })
    <!-- 刷新时，即发生任何请求时都返回这个index.html -->
    server.get('*', (req, res)=>{
        res.sendFile(__dirname+'/www/index.html');
    })

    server.listen('8000', 'localhost', (error)=>{
        if(error){
            console.log('服务器启动失败');
        }
        else{
            console.log('服务器启动成功');
        }
    })
```
#### 命名视图


> 优化：
路由切换方式与v-if的一致，会不断的有创建销毁，可以使用keep-alive包裹router-view,进行页面缓存
并且在路由文件中直接引入组件文件，也有很大初始化开销，所以可以修改为异步组件，component:()=>import('./pages/Bar')

> 路由懒加载：就是还是要异步组件的方式去加载组件
