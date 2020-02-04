[TOC]

### props
> 组件的属性在js部分以this方式访问，在dom结构当中以 属性名称访问

#### props可以访问不能设置
> props是组件外部属性，可以访问，不能设置 => vue将这个称为单选数据流
    因为还存在其他组件接受这个props传来的值，vue双向绑定的原理，如果在当前组件修改这个props接受的值，原组件的属性值会变化，其他复用到这个组件的props属性值也会变化，数据流向难以管理

> 像props这样的传值属性  可以访问不能设置的外部属性，vue将这个称为单向数据流：数据传输方式是单向下行，自顶向下绑定的数据
    父组件的数据可以流动到子组件。子组件的数据变化流动 不能传给父组件。
    原因：防止数据流向难以理解，使数据方便管理

#### props需要进行设置的两种方式
> 如果传来的props属性 用来传递一个初始值，子组件接下来希望将其作为一个本地的数据来使用，
    可以定义一个本地的data属性来作为初始值，然后在这个初始值进行设置操作

> 如果props属性值 以一种原始的值传入且需要进行切换，可使用computed计算属性

### props 对象形式接收
> props可以数组 和 对象方式接收，key为接收的属性名称，value为接收属性的类型：String Number ...
> 还可以设定移动默认值，如果没有传值就采用默认值：default(){return {...}}
> 以对象方式接收时，可以给接收对象设置一个属性：required:true 表示这个值为必传值

1. props是组件外部属性，可以访问 不能设置，原因数据自顶向下传输
    ```
    1. App.vue
        1. html:
            <h1>{{title}}</h1>
            <button @click='title="hello"'>修改</button>
            <Box :value='title' />
        2. js:
            import Box from './components/Box'
            export default {
                components:{
                    Box
                },
                data(){
                    return {
                        title:'hello vue'
                    }
                }
            }

    2. Box.vue: props是组件外部属性，可以访问，不能设置
        1. html:
            <div class='Box'>
                <p>{{value}}</p>
                <button @click='btnAction'>点击</button>
            </div>
        2. js:
            export default {
                <!-- props是组件外部属性，可以访问，不能设置
                    因为还存在其他组件接受这个props传来的值，vue双向绑定的原理，如果在当前组件修改这个props接受的值，原组件的属性值也会变化，数据流向难以管理
                -->
                props:['value'],
                <!-- data是组件内部属性,在当前组件中可以访问和设置 
                    如果传来的props属性 用来传递一个初始值，子组件接下来希望将其作为一个本地的数据来使用，可以定义一个本地的data属性来作为初始值，然后在这个初始值进行设置操作
                -->
                data(){
                    return {
                        a:1,
                        b:2,
                        boxtitle:this.value
                    }
                },
                <!-- 计算属性 在组件当中可以访问,是否可以设置取决于有没有实现set方法 
                    如果props属性值 以一种原始的值传入且需要进行切换，可使用computed计算属性
                -->
                computed:{
                    count(){
                        return this.a+this.b
                    },
                    length(){
                        return this.value.length
                    }
                },
                methods:{
                    btnAction(){
                        this.value='vue'
                    }
                }
            }
    ```
2. props 对象形式接收，可以进行属性校验：校验属性类型，校验属性必传,还有默认值设置,还可以自定义校验方法
```
    1. App.vue
        1. html:
            <h1>{{title}}</h1>
            <button @click='title="hello"'>修改</button>
            <Box :value='title' :list="['a','b','c']" />
        2. js:
            import Box from './components/Box'
            export default {
                components:{
                    Box
                },
                data(){
                    return {
                        title:'hello vue'
                    }
                }
            }

    2. Box.vue: 以对象形式接收list，
        1. html:
            <div class='Box'>
                <p>{{value}}</p>
                <button @click='btnAction'>点击</button>
            </div>
        2. js:
            export default {
                props:{
                    <!-- key值为需要接收的属性名称，value值为属性类型:
                        String,Array,Number,Object,Boolean,Function,Symbol ...
                    -->
                    value:String,
                    <!-- 这两种方式等价 -->
                    list:{
                        type:Array,
                        required:true,  // required为true 表示属性必传
                        default:'0'  // 如果属性list没有传来，则采用这个默认值
                    },
                    num{
                        validator:function(value){
                            console.log("接收到的数据：",value)
                            if( value > 10 ){
                                return true
                            }else{
                                console.error(new Error('传入的数据必须大于10'))
                                return false
                            }
                        }
                    }
                }
            }
```
3. props：组件的反向传值：子组件传父组件，初学者 不推荐使用
```
    1. App.vue
        1. html:
            <h1>接收到子组件：{{title}}</h1>
            <Box :func='testAction'/>
        2. js:
            import Box from './components/Box'
            export default {
                components:{
                    Box
                },
                data(){
                    return {
                        title:''
                    }
                },
                methods:{
                    testAction(val){
                        this.title = val
                    }
                }
            }

    2. Box.vue: 以对象形式接收list，
        1. html:
            <div class='Box'>
                <input type='text' v-model='testVal' />
                <button @click='btnAction'>点击</button>
            </div>
        2. js:
            export default {
                props:{
                    func:Function
                },
                data:{
                    return {
                        testVal:''
                    }
                },
                methods:{
                    btnAction(){
                        <!-- 调用func属性，触发父组件中的事件 -->
                        this.func(this.testVal)
                    }
                }
            }
```
> 空实例对象也可以实现子=>父，只是不推荐使用，因为空实例是全局的，只要整个项目还有，这个空实例就还存在

4. 使用v-if 和 v-else-if实现tab导航条，使用动态组件和keep-alive
```
    1. App.vue:
        1. html:
            <div id="app">
                <nav class="tabs">
                    <li class="tab"
                        v-for="(item, index) in ['one', 'two', 'three']" :key="item"
                        @click="btnAction(index)">
                        {{item}}
                    </li>
                </nav>
                <p>{{selectIndex}}</p>

            <keep-alive>
                <OneCom v-if="selectIndex===0"/>
                <TwoCom v-else-if="selectIndex===1"/>
                <ThreeCom v-else-if="selectIndex===2"/>
            </keep-alive>

            <!-- keep-alive作用：识别到标签内部有组件将要销毁，阻止该组件销毁，并且将组件缓存起来，下一次调用该组件时，不再创建，直接使用缓存中的组件 -->
                    <!-- 动态组件 component, 必须要设置属性is，值为组件的标签名字 -->
                    <!-- <component :is="comName"/> -->
            </div>
        2. js:
            import One from './components/One'
            import Two from './components/Two'
            import Three from './components/Three'
            export default {
                components: {
                    OneCom:One,
                    TwoCom:Two,
                    ThreeCom:Three
                },
                data(){
                    return {
                        selectIndex: 0
                    }
                },
                computed: {
                    comName(){
                        switch (this.selectIndex) {
                            case 0:
                                return 'OneCom';
                            case 1: 
                                return 'TwoCom';
                            case 2:
                                return 'ThreeCom'
                        }
                    }
                },
                methods: {
                    btnAction(val){
                        console.log('点击了：', val);
                        this.selectIndex = val;
                    }
                },
                errorCaptured(error){
                    console.log('app组件捕获到了异常');
                    console.log(error);
                    //处理异常

                    // 阻止异常再向上传递
                    // return false;

                    // 向上抛出异常
                    return true;
                }
            }
        3. css
            .tabs{
                width: 100%;
                height: 49px;
                position: absolute;
                left: 0;
                bottom: 0;
                display: flex;
                background: lightseagreen;
            }
            .tabs .tab{
                flex: 1;
                line-height: 49px;
                text-align: center;
                list-style: none;
            }
    2. One.vue: 同Two Three组件
        1. html:
            <div class="one">
                <h1>one组件</h1>
                <input type="text">
            </div>
        2. js
            console.log('one.vue文件执行');
            export default {
                created(){
                    console.log('one 创建');

                    'hello'.map(item=>{
                        console.log(item);
                    })
                },
                destroyed(){
                    console.log('one 销毁');
                },
                activated(){
                    console.log('one 组件成为活跃状态');
                    // 记录再次进入的时间
                },
                deactivated(){
                    console.log('one 组件失去活跃状态');
                    // 记录离开的时间
                },
                // 捕获异常的生命周期方法
                errorCaptured(){
                    //只能捕获子组件的异常，不能捕获自身组件的异常
                    console.log('one组件捕获到了异常');
                }
            }
        3. css
            .one input{
                border: 4px solid #333;
            }
```
### 动态组件

5. 使用动态组件component 实现tab导航条:动态组件是vue的内置组件吗，必须要有属性is，属性is的值为组件标签名
```
    1. App.vue:
        <keep-alive>
            <component :is='pageName'/>        
        </keep-alive>
```
6. 三个生命周期方法：
> activated=>活跃状态, deactivated=>失去活跃状态,
> errorCaptured=>捕获异常,只能捕获子组件的异常，不能捕获自身组件的异常

### 异步组件

7. 异步组件：当dom中调用这个组件，对这个组件实例化时，才访问组件对应的内容，从而加载组件对应的vue文件
```
    // import One from './components/One'
    // import Two from './components/Two'
    // import Three from './components/Three'

    export default {
        components: {
            // 异步组件：当dom中调用这个组件，对这个组件实例化时，
            //才访问组件对应的内容，从而加载组件对应的vue文件
            OneCom: function(){
                //得到OneCom组件的内容
                return import('./components/One')
            },
            TwoCom: ()=>import('./components/Two'),
            ThreeCom: ()=>import('./components/Three')
        },
```
- 项目导航栏tab使用v-if切换不合适，因为v-if有很大的切换开销，用户在tab进行切换频率是比较高的，所有不利于性能优化
使用v-show也不合适，因为v-show有很大的初始化开销，在用户没有切换到那一tab时，还好会创建出这个tab，也不利于性能优化，会影响项目的首页加载速度
项目中经常需要的一个效果：在从首页切换到其他页时，不希望首页被销毁，同时切换页刚好创建出来，
  + 即需要用到时才创建出其他tab，可以动态组件和keep-alive结合使用：<keep-alive>包裹动态组件<component is='' >
  + keep-alive可以将即将被销毁的组件缓存下来，不让组件被销毁，在下一次调用时 直接使用缓存中的组件
    - 不仅可以包裹component动态组件，可以用保存以上组件标签使用v-if来进行切换的方式，同样不会被销毁，并且下次直接使用缓存好的组件，
    - 而且组件内的input输入框如果输入了内容，切换到其他组件 再切回来时，input输入框里的内容还存在

- 异步组件：当dom中调用这个组件，对这个组件实例化时，才访问组件对应的内容，从而加载组件对应的vue文件
  + 今后的项目中尽量都使用异步组件

- 在单行文本输入框中 v-model等价于绑定一个value属性和实现input事件

- 执行顺序：
  + props => data => computed


### 插槽<slot />

> 在生命周期当中，渲染到的template会创建vm.$el并替换，所以在子组件标签纸当中写入的dom结构并不能被解析出来，使用 插槽就可以占位
```
1. App.vue:
    <div id='app'>
        <wrap>
            <h1>test h1</h1>
            <h2>test h2</h2>
            <h3>test h3</h3>
            <p>test p</p>
        </wrap>
    </div>

2. Wrap.vue: 在这个组件内部使用slot标签，项目解析wrap结构时，如果解析到slot标签，就会在外部App组件中 检查wrap子组件标签内部是否有dom结构，有就解析出来在slot标签的位置上
    <div class='wrap'>
        <h1>Wrap组件</h1>
        <slot />  // 
    </div>
```
### slot的name属性
> 在dom结构在设置属性slot='值',对应的slot标签的name属性就等于这个值，所有内容接收放在在不同位置
```
1. Wrap.vue:
    <div class='wrap'>
        <!-- 这里接收 h*标签 -->
        <slot name='h'>
        <hr />
        <h1>Wrap组件</h1>
        <hr />
        <!-- 这里接收其他标签 -->
        <slot />
        <!-- 这里接收p标签 -->
        <slot name='p' />
    </div>
2. App.vue:
    <div id='app'>
        <wrap>
            <h1 slot='h'>test h1</h1>
            <h2 slot='h'>test h2</h2>
            <h3 slot='h'>test h3</h3>

            <p slot='p'>test p</p>

            <div>其他一</div>
            <span>其他二</span>
            <!-- 不仅对普通标签有作业，组件标签也可以 -->
            <Box />
        </wrap>
    </div>
```
