[TOC]

>   vue 资源中有很多别人写的第三方插件，可以选用合适自己的。    
>   进入vue图像界面工具 安装组件命令：vue ui  还是测试版本    
>   npm run serve   
npm run build
```
v-model 等价于 绑定一个value值，并实现input事件 => :value='' ,@input='事件'
    <tabs :value='selectId' @input="selectAction">
    <tabs v-model="selectId">
```
### UI组件
-   mint-ui => 移动端
    >    饿了么开发
    安装：npm i mint-ui -S

    +   完整引入使用：
    ```
        1. main.js:
            <!-- 完整引入mint-ui
            引入mint-ui的逻辑 -->
            import MintUI from 'mint-ui'
            <!-- 引入mint-ui的样式 -->
            import 'mint-ui/lib/style.css'

            <!-- 使用mintUI插件 -->
            Vue.use(MintUI)

        2. App.vue:
            1. html
                <!-- <mt-header fixed title="固定在顶部"></mt-header> -->
                <mt-navbar v-model="selected">
                    <mt-tab-item id="1">选项一</mt-tab-item>
                    <mt-tab-item id="2">选项二</mt-tab-item>
                    <mt-tab-item id="3">选项三</mt-tab-item>
                </mt-navbar>

                <!-- tab-container -->
                <mt-tab-container v-model="selected">
                    <mt-tab-container-item id="1">
                    <mt-cell v-for="n in 10" :title="'1内容 ' + n" />
                    </mt-tab-container-item>
                    <mt-tab-container-item id="2">
                    <mt-cell v-for="n in 4" :title="'2测试 ' + n" />
                    </mt-tab-container-item>
                    <mt-tab-container-item id="3">
                    <mt-cell v-for="n in 6" :title="'3选项 ' + n" />
                    </mt-tab-container-item>
                </mt-tab-container>

                <button @click="btnAction">按钮</button>
            2. js:
                export default {
                    data(){
                        return {
                        selected: '1'
                        }
                    },
                    methods: {
                        btnAction(){
                        //弹框，toast
                        this.$toast('网络错误');
                        }
                    }
                }
    ```
    +   按需引入使用：引入所有样式，功能按需引入，因为官网中支持按需引入的方法只支持babel v.6版本，而我们现在使用的是babel v7
    ```
        1. index.html:
            <!-- 引入mint-ui的样式 -->
            拷贝mint-ui依赖包中的style.css 和 style.min.css , 复制到vue项目中的public/css/
            然后在index.html中引入

        2. App.vue:
            1. html:
                <mt-navbar v-model="selected">
                    <mt-tab-item id="1">选项一</mt-tab-item>
                    <mt-tab-item id="2">选项二</mt-tab-item>
                    <mt-tab-item id="3">选项三</mt-tab-item>
                </mt-navbar>

                <!-- tab-container -->
                <mt-tab-container v-model="selected">
                    <mt-tab-container-item id="1">
                    选项1的内容部分
                    </mt-tab-container-item>
                    <mt-tab-container-item id="2">
                    选项2的内容部分
                    </mt-tab-container-item>
                    <mt-tab-container-item id="3">
                    选项3的内容部分
                    </mt-tab-container-item>
                </mt-tab-container>

                <button @click="btnAction">按钮</button>
            2. js：
                import {Toast, Navbar, TabItem, TabContainer, TabContainerItem} from 'mint-ui'

                // Vue.component(Navbar.name, Navbar);
                // Vue.component(TabItem.name, TabItem);
                // Vue.component(TabContainer.name, TabContainer);
                // Vue.component(TabContainerItem.name, TabContainerItem);

                export default {
                    components: {
                        [Navbar.name]: Navbar,
                        [TabItem.name]: TabItem,
                        [TabContainer.name]: TabContainer,
                        [TabContainerItem.name]: TabContainerItem
                    },
                    data(){
                        return {
                        selected: '1'
                        }
                    },
                    methods: {
                        btnAction(){
                        //弹框，toast
                        Toast('网络错误');
                        }
                    }
                }
    ```
>   由于mint-ui很久没有更新 维护，有些功能不适合逐步更新的框架，所以全部组件都测试一遍

-   vant => 移动端
    >   有赞公司开发的
    安装：npm i vant -S  || yarn add vant

    +   完整引入使用：
    ```
        1. main.js:
            import Vant from 'vant'
            import 'vant/lib/index.css'
            Vue.use(Vant)
    ```
    +   按需引入组件和样式使用：
    ```
        1. App.vue:
            import Button from 'vant/lib/button'
            import 'vant/lib/button/style'

            export default {
                components: {
                    [Button.name]: Button,
                    [Rate.name]: Rate,
                    [Popup.name]: Popup
                }
            }
    ```
    +   安装插件 实现按需引入：
    ```
        安装babel插件：npm i babel-plugin-import -S
        1. babel.config.js：babel 7，在babel.config.js中配置：
            module.exports = {
                presets: [
                    '@vue/app'
                ],
                plugins: [
                    ['import', {
                    libraryName: 'vant',
                    libraryDirectory: 'es',
                    style: true
                    }, 'vant']
                ]
            }
        2. App.vue:
            import {Button, Rate, Popup} from 'vant'
            export default {
                components: {
                    [Button.name]: Button,
                    [Rate.name]: Rate,
                    [Popup.name]: Popup
                }
            }
    ```
-   element ui => pc端
    >   饿了么开发
-   iview => pc端
