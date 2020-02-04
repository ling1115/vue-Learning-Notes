[TOC]

组件：
正常情况下 项目页面有很多部分组成，逻辑非常多，维护相对来说较困难
组件系统是vue的另一个重要概念，可以使用小型，独立和通常可复用的组件构建大型应用，提高代码的可维护性和复用性

1. 组件传值：v-bind,快捷：@ 
    
>   内部用props接收，组件内部使用$emit触发外部绑定事件并传值
```
    css:
        .todo-item{width:100px;height:100px;border:1px solid #ccc}
        .todo-item p{color:cyan}
    1. html:
        <div id='#app'>
            <todo-item></todo-item>
        </div>
    2. js:
        <script src='vue.js'></script>
        <script>
            Vue.component('todo-item',{
                template:'<div class='todo-item'><p>{{message}}</p><button @click='btnAction'>点击</button></div>',
                data:function(){
                    return {
                        message:'hello',
                    }
                },
                methods:{
                    btnAction:function(){
                        alert(this.message)
                    }
                }
            })
        </script>
```
2. 单文件组件：以上的组件创建可读性不高，维护起来比较费劲，创建.vue文件，将html css js分为三个模块
```
    test.vue:
        <template>
            <div class='todo-item'>
                <p>{{message}}</p><button @click='btnAction'>点击</button>
            </div>
        </template>
        <script>
            export default {
                data:function(){
                    return {
                        message:'hello',
                    }
                },
                methods:{
                    btnAction:function(){
                        alert(this.message)
                    }
                }
            }
        </script>
        <style>
            .todo-item{width:100px;height:100px;border:1px solid #ccc}
            .todo-item p{color:cyan}
        </style>
```
>   在这只需要将这个.vue文件引入，然后再声明这个组件即可：Vue.component('todo-item')
    但是浏览器无法识别.vue格式的文件(html,css,js,图片)，无法直接引入，
    所以需要使用(官网)vue loader这个工具对.vue进行编译 成.js文件

3. vue cli3: vue脚手架工具
    -   安装：npm install @vue/cli -g
    -   创建项目：vue create 项目名称
    -   选择哪种工具：default(ababel,eslint)  Manually select -features自定义选择  上下箭头移动 enter选择
        -   空格选中：Babel(将es6语法编译成浏览器可以正常编译的语法) Router Vuex css预编译工具
                Linter(语法检查)  单元测试 端对端测试
        -   enter => 
            将以上的工具配置到独立文件当中还是配置在package.json文件中，一般选择配置在package.json
        -    enter => 
            是否将以上配置保存为将来可以用的备用预配置 => no,选择yes，需要给这个配置设置一个名字
        -   enter => 进行安装配置
    进入项目：cd 项目名称
    运行项目：npm run serve

4. 创建项目的main.js文件：
>   main.js是vue脚手架工具创建好项目的入口文件，创建vue实例对象 管理html页面中id为app的div结构，
    vue实例的dom结构由组件App提供
```
    1. 解读main.js:
        <!-- 等价于 var Vue = require('vue') 这是Common.js规范模块开发，下面是ES6 -->
        import Vue from 'vue'  
        import App from './App.vue'
        <!-- 设置生产模式的提示为false，即为开发模式，无压缩，包含警告之类的，设置为true则为生产模式，压缩版，删除警告 -->
        Vue.config.productionTip = false

        new Vue({
        render: h => h(App),
        }).$mount('#app')

        <!-- 上面的new Vue部分等价于下面 -->
        const vm = new Vue({
            <!-- 引入App,然后component声明局部组件App,然后template可以使用这个组件 -->
            <!-- template:'</App>',
            component:{
                App:App
            }, -->
            <!-- render函数是替换template的方式：等价于上面的template和component
                h参数是一个形参，相当于createElement方法，能够读出App中template中的dom结构
                然后将读取出的dom返回
            -->
            render:function(createElement){
                let dom = createElement(App)
                return dom
            }
        })
        <!-- 以上在生命周期中的顺序会走到判断是否有el，没有，要都明天vm.$mount('#app') 
            动态将vm实例挂载到dom结构上,再执行render()
        -->
        vm.$mount('#app')
```
5. 单文件组件
  - 1. data属性对应的是函数  this指向  
```
        <template>
            <!-- template声明组件结构，只能有一个根标签：由一个div包含所有标签， -->
            <div id="app">
                <h2>{{message}</h2>
                <button @click='btnAction'>点击</button>
            </div>
        </template>
        <script>
            <!-- script提供组件的数据和逻辑处理 -->
            <!-- export default{}等价于commonjs规范中module.exports={} -->
            export defaule {
                <!-- data是组件的属性，是vue实例的data属性一样，但是配置项中对应的是函数而不是对象-->
                data:function(){
                    return {
                        message:'hello vue'
                    }
                },
                methods:{
                    btnAction(){
                        <!-- this指向组件对象 -->
                        alert(this.message)
                    }
                }
            }
        </script>
        <style>
            #app{background:cyan}
            #app h2{color:red}
        </style>
```
  - 2. vue工具Vetur 快捷方式快速创建vue文件：scf+enter

  - 3. 声明全局组件：
```
        1. Wrap.vue: 
            <template>
                <div class='wrap'>
                    <h2>Wrap组件</h2>
                </div>
            </template>
            <script>
                export defaule {
                    data:function(){
                        return {
                            message:'hello vue'
                        }
                    },
                    methods:{
                        btnAction(){
                            <!-- this指向组件对象 -->
                            alert(this.message)
                        }
                    }
                }
            </script>
            <style>
                .wrap{
                    color:bule
                }
            </style>
        2. 在main.js中引入Wrap并且声明全局组件
            import Wrap from './Wrap.vue'
            Vue.component('wrap',Wrap)
        3. App.vue: 使用Wrap
            <template>
                <!-- 单标签，也可以使用双标签 -->
                <wrap />
            </template>
        
        4. wrap中的h2样式会被App中的样式同化，可以给style标签添加一个属性 scoped，表示样式仅作用于当前组件
            <style scoped>
            </style>
```
  - 4. 声明局部组件
    - 1. App.vue：在App组件中使用wrap组件，可以将wrap声明局部组件
```
  <script>
      import Wrap from './Wrap.vue'
      export default {
          ...,
          <!-- 声明局部组件 -->
          componnets:{
              wrap:Wrap
          }
      }
  </script>
```
6. 单文件组件方式进行父子组件通信: 
>   父=>子：v-bind:值名称，子组件中用props接受这个值名称，然后可以使用这个值显示在子组件上

>   子=>父：子使用$emit触发事件，并传值，这个事件需要在父组件中 使用的子组件标签中接收=> 
        父组件中的子组件标签：@事件名称='事件处理函数',然后父组件methods执行这个事件处理函数，可以接收子组件传来的值

  - 1. App.vue: 先在App组件中引入父组件
    ```
        1. html:
            <div id='app'>
                <Father>
            </div>
        2. js:
            import Father from './Father'
            export default {
                components:{
                    Father
                }
            }
    ``` 
  - 2. Father.vue:创建父组件
    ```
        1. html:
            <div class='father'>
                <p>父组件</p>
                <p>接受到子组件的信息：{{Sonmessage}}</p>
                <input type="text" v-model="message">
                <button @click="FatherSend">发送</button>
                <Son v-bind:Fathermessage='Fathermessage' @send='hanldeSend' />
            </div>
        2. js:
            import Son from './Son'
            export default {
                components:{
                    Son
                },
                data(){
                    message:'',
                    Fathermessage:''
                    Sonmessage:''
                },
                methods:{
                    FatherSend(){
                        this.Fathermessage = this.message
                    },
                    hanldeSend(Sonmessage){
                        this.Sonmessage = Sonmessage
                    }
                }
            }
        3. css:
            .father{width:300px;height:300px;background:cyan}
    ```
  - 3. Son.vue: 创建子组件
    ```
        1. html:
            <div class='son'>
                <p>子组件</p>
                <p>接受到父组件的信息：{{Fathermessage}}</p>
                <input type='text' v-model='Sonmessage' />
                <button @click='sendAction'>发送</button>
            </div>
        2. js:
            import Son from './Son'
            export default {
                <!-- 接收父组件传入的属性，v-bind绑定的是什么值，这里就接受什么值 -->
                props:['Fathermessage'],
                data(){
                    Sonmessage:''
                },
                methods:{
                    sendAction(){
                        this.$emit('send',this.Sonmessage)
                    }
                }
            }
        3. css:
            .son{background:blue}
    ```

7. 非父子组件传值：创建一个空实例
>   作为非关系组件中的中转：采用$on $emit => 多对多的关系，可以有多个监听 多个触发

```
    1. pubsub.js: 创建空实例
    
        import Vue from 'vue'
        const pubsub = new Vue()
        <!-- 
            先监听后触发！！！
            监听事件 等价于标签中的@send绑定事件：@send='事件函数' 
            参数一：监听事件名称，参数二：监听到事件的回调函数
        -->
        pubsub.$on('send',()=>{
            console.log('监听事件执行')
        })
        <!-- 触发事件：只有$emit触发事件了，才可以执行监听中的事件函数 -->
        pubsub.$emit('send')

        export default pubsub

    2. main.js：引入pubsub.js
        import './pubsub'

    3. One.vue: One组件传值给Two组件：在one组件中触发事件，执行pubsub.$emit(),传输数据
        1. html:
            <div class='One'>
                <h1>One组件</h1>
                <input type='text' v-model='value' />
                <button @click='btnAction'>发送</button>
            </div>
        2. js:
            import pubsub from './pubsub'
            export defualt {
                data(){
                    return {
                        value:''
                    }
                },
                methods:{
                    btnAction(){
                        pubsub.$emit('send',this.value)
                    }
                }
            }
        3. css:
            .One{background:cyan}

    4. Two.vue: One组件传值给Two组件：在two组件中监听事件，，执行pubsub.$on(),接收数据
        1. html:
            <div class='Two'>
                <h1>Two组件</h1>
                <p>接收到One组件传来的数据：{{OneDate}}</p>
            </div>
        2. js:
            import pubsub from './pubsub'
            export default {
                data(){
                    return {
                        OneDate:''
                    }
                },
                create(){
                    <!-- 这里的回调函数必须使用箭头函数，因为要使用this,如果不使用箭头函数，this将指向pubsub -->
                    pubsub.$on('send',(value)=>{
                        this.OneData = value
                    })
                }
            }
        3. css:
            .Two{background:red}

    5. App.vue: 声明两个局部组件
        1. html：
            <One />
            <hr />
            <Two />
        2. js:
            import One from './One'
            import Two from './Two'
            export default {
                components:{
                    One,
                    Two
                }
            }
```
为什么 组件中 data属性对应的是函数，而实例中的data是对象？
原因是：组件是可以复用的，每次调用组件都是获取一个新的data属性，如果data是一个对象，在复用时所有组件都会共用一个对象，会干扰其他组件的data，data是函数，在被复用这个组件时，不会干扰到这自身组件中的data
