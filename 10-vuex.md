[TOC]

>   vuex是一个专门为vue.js应用程序开发的状态管理模式，就是管理全局数据的一种模式，可以实现组件数据共享，集中式管理组件的数据，
安装：npm i vuex -S
### vuex基本使用---state
```
1. store.js:
    import Vue from 'vue'
    import Vuex,{Store} from 'vuex'

    Vue.use(Vuex)
    
    const state = {

    }

    <!-- 创建管理状态的仓库 -->
    const store = new Store({
        state
    })

    export default store

2. main.js:
    import store from './store.js'

    new Vue({
        ...,
        <!-- 声明的状态需要在创建仓库对象是提供这些状态，key值固定的，value值不少 -->
        store
    })
```

#### 使用store中的状态的便捷方法
1. 使用计算属性：One.vue:
```
    1. html:
        <p>{{this.$store.state.appName}}</p>
        <p>{{appName}}</p>
    2. js:
        export default {
            data(){
                return {
                    value:''
                }
            },
            //在计算属性中，将全局状态转为组件的局部计算属性
            // 全局数据变化，组件的数据就变化
            // 这么做方便在组件中调用全局状态
            computed: {
                appName(){
                    return this.$store.state.appName
                },
                arrData(){
                    return this.$store.state.list;
                 }
             },
        }
```
2. 使用vuex中的，mapState，这个方法与第一种完全等价
```
    computed: mapState(['appName', 'list']) // 数组写法不能更换名字
    computed: mapState({
        appName:'appName',
        arrDate: 'list'
    })
```
3. 要将自定义计算属性与状态管理数据的计算属性合并一起，可以使用Object.assign或者...扩展符
    将两个computed合并为一个
```
    computed: Object.assign(
        {},
        mapState({
            appTitle: 'appName',
            arrData: 'list'
        }),
        {
            count(){
                return this.a + this.b;
            }
        }
    ),
    或者
    computed: {
        ...mapState({
           appTitle: 'appName',
           arrData: 'list'
        }),
        count(){
            return this.a + this.b
        }
    },
```
### vuex基本使用---getter
```
1. store.js:
    // 全局的getter
    // 通过全局的state,其他的getters计算得到的数据
    // getter只能通过依赖的状态值计算得到数据
    // 不能直接设置值，因为没有set方法
    const getters = {
        listLength(state, getters){
            return state.list.length;
        },
        type(state, getters){
            if(getters.listLength % 2 == 0){
            return '偶数';
            }else{
            return '奇数';
            }
        }
    }

2. 使用getter的便捷方法：
    import {mapGetters} from 'vuex'
    export default {
        computed: {
            // ...mapGetters(['listLength', 'type'])
            ...mapGetters({
                length: 'listLength',
                flag: 'type'
            })
        },
        created(){
            // console.log(this.$store.getters.listLength);
            // console.log(this.$store.getters.type);

            // console.log(this.listLength);
            // console.log(this.type);

            console.log(this.length);
            console.log(this.flag);
        }
    }
```
### vuex基本使用---mutations
```
1. store.js:
    // mutation是修改全局状态state的事件
    // 必须是一个同步函数，函数内部不能执行异步操作
    // mutation方法是vuex中唯一能够操作state的方法
    const mutations = {
        addDataToList(state, params){
            console.log('addDataToList触发了');
            state.list.push(params.value);
        },
        setCity(state, params){
            state.city = params.value;
        }
    }
2. Three.vue: 使用mutations的便捷方法：
    1. html:
        <div class="page" id="three">
            <h1>mutation</h1>
            <p>{{$store.state.appName}}</p>
            <p>{{$store.state.list}}</p>
            <p>{{$store.getters.listLength}}</p>
            <p>{{$store.getters.type}}</p>
            <input type="text" ref="in">
            <button @click="addAction">添加</button>
            <button @click="addData({value: $refs.in.value})">添加</button>
        </div>
    2. js:
        import {mapMutations} from 'vuex'
        export default {
            //将全局的mutation方法转为组件的methods方法
            // methods: mapMutations(['addDataToList']),
            // methods: mapMutations({
            //     addData: 'addDataToList'
            // })

            methods: {
                ...mapMutations({
                    addData: 'addDataToList'
                }),
                addAction(){
                    let value = this.$refs.in.value;
                    //触发store中的mutation方法
                    // 参数1:mutations事件名字
                    // 参数2:调用mutations中的事件传的值
                    // this.$store.commit('addDataToList', {value: value});

                    this.$store.commit({
                        type: 'addDataToList',
                        value: value
                    });
                }
            }
        }
```
### vuex基本使用---actions
>   使用axios进行数据请求：npm i axios -S
```
1. store.js:

    // actions是可以包含异步操作全局事件
    // 在actions中不能够修改state，需要调用mutations才能修改state
    // actions接受参数：1上下文，2传递的参数，3还有一个保留字段 可以不用管它
    const actions = {
        getLocationCity(context, params){
            console.log('getLocationCity触发了');
            //发送请求
            // axios.get('http://localhost:8080/restapi/bgs/poi/reverse_geo_coding', {
            axios.get('/restapi/bgs/poi/reverse_geo_coding', {
                params: {
                    latitude: params.latitude,
                    longitude: params.longitude
                }
            })
            .then(response=>{
                console.log('成功');
                console.log(response.data.city);
                // 再actions中不能直接操作state
                // 需要调用mutations才能修改state
                context.commit('setCity', {
                    value: response.data.city
                });
            })
            .catch(error=>{
                console.log('失败');
                console.log(error);
            })
        }
    }

2. Four.vue: 使用axios的便捷方法：

    1. html
        <div class="page" id="four">
            <h1>action</h1>
            <p>{{$store.state.appName}}</p>
            <input type="text" placeholder="经度" v-model="longitude"/>
            <input type="text" placeholder="纬度" v-model="latitude"/>
            <button @click="btnAction">获得所在城市</button>
        </div>
    2. js:
        import {mapActions} from 'vuex'
        export default {
            data(){
                return {
                    longitude: null,
                    latitude: null
                }
            },
            methods: {
                // ...mapActions(['getLocationCity']),
                ...mapActions({
                    getCity: 'getLocationCity'
                }),
                btnAction(){
                    console.log(this.longitude, this.latitude);
                    // 触发store中的actions事件
                    // 参数1:事件名字
                    // 参数2:传的参数
                    // this.$store.dispatch('getLocationCity', {
                    //     longitude: this.longitude,
                    //     latitude: this.latitude
                    // });

                    // this.$store.dispatch({
                    //     type: 'getLocationCity',
                    //     longitude: this.longitude,
                    //     latitude: this.latitude
                    // })

                    // this.getLocationCity({
                    //     longitude: this.longitude,
                    //     latitude: this.latitude
                    // });

                    this.getCity({
                        longitude: this.longitude,
                        latitude: this.latitude
                    });
                }
            }
        }

3. 解决跨域：创建vue.config.js,配置服务器代理

    //vue项目的配置文件
    module.exports = {
        //配置vue项目的服务器
        devServer: {
            //配置服务器的代理
            proxy: {
                //当请求以'/restapi'开头时
                // 那么将请求转发给https://h5.ele.me'这台服务器
                // 得到的结果再响应给页面
                '/restapi': {
                    target: 'https://h5.ele.me',
                    //是否修改域名
                    changeOrigin: true
                }
            }
        }
    }
```

>   要实现外部传入的属性/数据变，引入的内部组件也跟着变，可以使用计算属性

>   如果属性变化，导致其他数据也发生变化,那么这个数据就放在计算属性中进行计算
如果属性发生变化，需要进行事件操作，刷新dom，操作dom，请求数据。。。
应该用侦听器，监听属性的变化，从而对应的操作。
