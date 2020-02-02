[TOC]
- vue是MVVM js框架，是有国人尤雨溪开发，
    与react angular对比：中文文档比较完善  代码轻量 上手简单 容易使用
- vue: vue是一套英语构建用户界面的渐进式框架，与其他大型框架不同的是，vue被设计为可以自底向上逐层应用。vue的核心只关注视图层，不仅易于上手，还便于与第三方库和既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，vue页完全能够为复杂的单页应用提供驱动。

- vue无法兼容IE8以下版本的浏览器，因为vue使用了IE8无法模拟的ECMAScript 5的特性。

下载vue.js  || vue.min.js,script引入
```
1. html
    <!-- <div id="app">hello word</div> -->
    <div id="app">{{message}}</div>

2. js:
    <script src='vue.js'></script>
    <script>
        // 原生js方法
        // var dom = document.querySlector('#app')
        // dom.innerText = 'hello vue'

        // setTimeout(()=>{
        //    dom.innerText = 'bye bye'
        // },1000)

        // 创建vue实例
        var vm = new Vue(
            // 配置项
            {
                // 让vue实例接管id为app的dom结构
                // 如果上面的dom结构为class='app',则改为el:'.app'
                // 因为id是唯一的，所有选择结构id为app的dom结构
                el:"#app",
                // 申明好vue结构dom结构之后，给dom结构设置属性
                data:{
                    message:'hello vue'
                }
            }
        )
        // 不需要操作dom
        setTimeout(()=>{
            vm.message = 'bye bye'
            // vm.$data.message = 'bye bye'
        },1000)

    </script>
```
+ webpack: 包管理工具
+ npm: node内置包管理工具

### vue指令：
1. v-if: 通过show来控制box的显示和隐藏
```
    css:
        .box{width:100px;height:100px;background:red;}
    html:
        <div id='app'>
            <div class='box' v-if='show'></div>
        </div>

    js:
        <script src='vue.js'></script>
        <script>
            var vm = new Vue({
                el:"#app",
                data:{
                    show:true
                }
            })
        </script>
```
2. v-for: 可以绑定数组的数据来渲染一个项目列表
```
    html:
        <div id='app'>
            <ul>
                <li v-for='{item,index} in list' v-key='index'>{{item}}---{{index}}</li>
            </ul>
        </div>

    js:
        <script src='vue.js'></script>
        <script>
            var vm = new Vue({
                el:"#app",
                data:{
                    list:['aa','ss','ad','dfd']
                }
            })
        </script>
```
3. v-on: 事件绑定  简写 ： @事件名称='事件触发函数($event,a,b,c)' 
    - 例如：@mousemove='handleMousemove'
    - 传参：
```
     html:
        <div id='app'>
           <button v-on:click='handleClick'>点击</button>
        </div>

    js:
        <script src='vue.js'></script>
        <script>
            var vm = new Vue({
                el:"#app",
                data:{
                    list:['aa','ss','ad','dfd']
                },
                methods:{
                    handleClick(ev,a,b,c){
                        console.log(ev)
                        console.log('点击事件触发了')
                    }
                }
            })
        </script>
```
4. v-model: 数据双向绑定的指令 在表单控件或组件上创建双向绑定
```
    html:
        <div id='app'>
           <input type='text' v-model='value' />
        </div>

    js:
        <script src='vue.js'></script>
        <script>
            var vm = new Vue({
                el:"#app",
                data:{
                    list:['aa','ss','ad','dfd'],
                    value:'abc'
                },
                methods:{
                    handleClick(ev,a,b,c){
                        console.log(ev)
                        console.log('点击事件触发了')
                    }
                }
            })
        </script>
```
5. todolist
```
    html:
        <div id='app'>
           <input type='text' v-model='value' /><button @click='addAction()'>添加</button>
           <ul>
                <li v-for='(item,index) in arr'>
                    {{item}}<span @click='removeAction(index)'>删除</span>
                </li>
           </ul>
        </div>

    js:
        <script src='vue.js'></script>
        <script>
            var vm = new Vue({
                el:"#app",
                data:{
                    arr:['aa','ss','ad','dfd'],
                    value:''
                },
                methods:{
                    addAction(){
                        this.arr.push(this.value)
                        this.value = ''
                    },
                    removeAction(index){
                        this.arr.splice(index,1)
                    }
                }
            })
        </script>
```
### MVVM模型
- M => model 数据模型
- V => view 视图(dom元素)
- VM => ViewModel(视图模型) - 管理数据模型的工具  vue
        数据变化 
        
    - =>通过ViewModel模型中的数据绑定来进行 视图更新 重新渲染 , 
        视图操作，通过ViewModel管理模型中的事件监听来改变数据 ,
        这些就是有vue来管理操作的

    - 在视图操作中，进行事件监听，并且引入了虚拟dom的概念，在vue管理的dom结构当中，会在js中准备一个虚拟dom结构，这个虚拟dom结构就是一个js对象，去映射所管理的dom结构

### 组件
正常情况下 项目页面有很多部组成，逻辑非常多，维护相对来说较困难
组件系统是vue的另一个重要概念，可以使用小型，独立和通常可复用的组件构建大型应用，提高代码的可维护性和复用性

1. 用组件完成todolist
```
    html:
        <div id='app'>
        <input type='text' v-model='value' /><button @click='addAction()'>添加</button>
        <ul>
            <!-- 调用组件 -->
            <!-- v-bind:item='item' 将arr遍历出来的item传给todo-item组件中的item值 -->
            <!-- 在组件中使用props属性进行接收 item -->
            <todo-item v-for='(item,index) in arr'
                v-bind:item='item' 
                v-bind:index='index' 
                v-key='index'
                v-bind:delete='removeAction'
                />
        </ul>
        </div>

    js:
        <script src='vue.js'></script>
        <script>

            // 声明组件:  参数1:组件名字  参数2: 组件内容
            Vue.component("TodoItem",{
                props:['item','index'],
                template: `<li>
                                {{item}}<button @click='deleAction'>删除</button>
                            </li>`,
                method:{
                    deleAction(){
                        this.$emit('delete',this.index)
                    }
                }
            })
            var vm = new Vue({
                el:"#app",
                data:{
                    arr:['aa','ss','ad','dfd'],
                    value:''
                },
                methods:{
                    addAction(){
                        this.arr.push(this.value)
                        this.value = ''
                    },
                    removeAction(index){
                        this.arr.splice(index,1)
                    }
                }
            })
        </script>
```


