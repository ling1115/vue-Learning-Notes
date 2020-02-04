[TOC]
### slot 接收到的组件标签，怎么是外部组件与这个内部组件进行传值
> Wrap 和 Box 是非父子组件,父组件都是App.vue

#### 组件标签嵌套的通信方式：使用空实例对象进行传值
1. 可以使用空实例对象进行传值
>  VueComponent 是 Vue实例的一个子类，基础Vue的所有属性和方法

>  Vue.prototype原型属性，使用它设置一个属性$center=new Vue()，
  main.js中vm.center访问不是这个对象，就会去原型链上寻找，
  那么在局部组件中范围this.center，也就可以获得这个对象，如果局部组件内部具有center属性，就访问不到这个对象中的center，
  为了区分这两属性，在prototype设置属性加一个$符号。
  
> 由此 所有组件都可以访问到这个对象，可以利用这个对象进行组件传值就没必要新建文件 来创建一个空实例对象

2. Vue.prototype 创建一个实例对象: Vue.prototype.$center = new Vue({})

#### slot传值
> dom结构中Wrap标签 和 Box标签是父子节点，
可以通过访问 组件标签在外部使用时的 子节点或父节点 的 组件对象：
- 子节点是一个数组，因为子节点可以有多个，父节点是一个对象，因为父节点只有一个
  + 通过this.$children 或 this.$parent 只能访问到组件标签对象，不能访问到普通dom元素

  + 可以通过this.$slots 可以访问到slot装载到的所有对象所有dom元素，包括普通标签，
        获取到的是虚拟对象节点VNode，如果是组件对象，则通过componentInstance来获取组件对象
        如果是普通节点，则componentInstance为undefined

  + 如果slot有设置name属性，则this.$slots可以获取到的分为两类，一个key值为default 包含所有没有设置slot的标签对象，一个key值为name属性值，包含设置slot属性的标签对象

  + 获得节点对象之后，就可以进行赋值：
        如给父节点传值 this.$parent.SonData = this.message

### ref：给标签设置ref，访问dom元素，在当前组件时唯一性
> 
    给普通标签设置ref，通过this.$refs 取得的是 dom对象
    给组件标签设置ref，通过this.$refs 取得的是组件对象
    普通标签或 组件标签 使用v-for循环设置ref，可以取得一个数组，所有标签对象都保存在其中

### 过渡动画

#### <transition>是vue的内置对象----css过渡
```
1. App.vue:
    1. html:
        <div id="app">
            <p>{{isSelected}}</p>
            <input type="checkbox" v-model="isSelected">
            <!-- 
                transition组件如果不设置name，执行动画时添加的class为：
                    v-enter v-enter-to v-enter-active
                    v-leave v-leave-to v-leave-active
                    v-appear v-appear-to v-appear-active
                transition组件如果设置name，执行动画时添加的class为：
                    [name]-enter [name]-enter-to [name]-enter-active
                    [name]-leave [name]-leave-to [name]-leave-active
                    [name]-appear [name]-appear-to [name]-appear-active
                自定义transition组件添加动画的class名字：
                    enter-class
                    enter-to-class
                    enter-active-class

                    leave-class
                    leave-to-class
                    leave-active-class
                    <!-- appear是第一次显示时的 -->
                    appear-class
                    appear-to-class
                    appear-active-class
            -->
            <transition appear    enter-class="one" enter-to-class="two" enter-active-class="three"
                        appear-class="four" appear-to-class="two" appear-active-class="three">
                <p class="test" v-if="isSelected">hello world</p>
            </transition>
        </div>
    2. css:
        .test{background: red;}
        /* .v-enter{opacity: 0;background: #fff;}
        .v-enter-to{opacity: 1;background: red;}
        .v-enter-active{transition: 2s;}
        .v-leave{transform: scale(1);}
        .v-leave-to{transform: scale(0);}
        .v-leave-active{transition: 3s transform;} 
        */

        /* .fade-enter{opacity: 0;background: #fff;}
        .fade-enter-to{opacity: 1;background: red;}
        .fade-enter-active{transition: 2s;}
        .fade-leave{transform: scale(1);}
        .fade-leave-to{transform: scale(0);}
        .fade-leave-active{transition: 3s transform;} 
        */

        .one{opacity: 0;background: #fff;}
        .four{opacity: 0;background: #000;}
        .two{opacity: 1;background: red;}
        .three{transition: 4s;}

2. Box.vue:
    <template>
        <div class="box">
        </div>
    </template>
    <style>
        .box{
            width: 300px;
            height: 200px;
            background: lightblue;
        }
    </style>
```
> 如果enter/leave 的第一帧与enter-to / leave-to的初始状态一致，则可以用enter-to替换enter-active，否则不能替换，只能用enter-active / leave-active

#### transition---动画
```
1. App.vue:
    1. html:
        <div id="app">
            <p>{{isSelected}}</p>
            <input type="checkbox" v-model="isSelected">
            <transition enter-active-class="fadeIn" leave-class leave-to-class="zoomOut">
                <p class="test" v-if="isSelected">hello world</p>
            </transition>
        </div>
    2. css:
        .test{background: red;}
        @keyframes fadeIn{
            0%{
                opacity: 0;
            }
            100%{
                opacity: 1;
            }
        }
        .fadeIn{
            animation: fadeIn 2s;
        }
        @keyframes zoomOut{
            0%{
                transform: scale(1);
            }
            100%{
                transform: scale(0);
            }
        }
        .zoomOut{
            animation: zoomOut 3s;
        }
```
#### transition----既有过渡也有动画
```
1. App.vue:
    1. html:
        <div id="app">
            <p>{{isSelected}}</p>
            <input type="checkbox" v-model="isSelected">
            <!-- transition组件既有transition动效，又有animation动效，时间使用更长的那个时间
                如果需要指定时间时哪个类型。给transition组件设置type属性：transition/animation

                自定义时间，直接设置属性duration
            -->
            <transition enter-active-class="slideIn" name="fade" 
                        :duration="{enter: 2000, leave: 1000}">
                <p class="test" v-if="isSelected">hello world</p>
            </transition>
        </div>
    2. css:
        .test{background: red;}
        @keyframes slideIn{
            0%{
                transform: translateX(100%);
            }
            100%{
                transform: translateX(0);
            }
        }
        .slideIn{
            animation: slideIn 5s;
            transition: 1s;
        }
        .fade-enter{
            opacity: 0;
        }
        .fade-enter-to{
            opacity: 1;
        }
```
#### 使用anmiate.css
```
1. public/index.html
    引入animate.css：
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css">
2. App.vue:
    1. html:

    2. css
```
> 有的动画效果的动画时间已经设定好，有的没有设定，所以需要手动设置animation-duration

#### javascript 钩子函数
```
1. html:
    <div id="app">
        <p>{{isSelected}}</p>
        <input type="checkbox" v-model="isSelected">
        <transition @before-enter="handleBeforeEnter" @enter="handleEnter" @after-enter="handleAfterEnter">
            <p class="test" v-if="isSelected">hello world</p>
        </transition>
    </div>
2. js:
    import Box from './components/Box'
    export default {
        components: {
            Box
        },
        data(){
            return {
                isSelected: true
            }
        },
        methods: {
            handleBeforeEnter(el){
                console.log('进入动画执行前');
                console.log(el);
                el.style.background = '#fff';
            },
            handleEnter(el, done){
                console.log('已经进入');
                setTimeout(() => {
                    el.style.background = 'green';
                }, 1000);

                setTimeout(() => {
                    done();
                }, 2000);
            },
            handleAfterEnter(el){
                console.log('进入动画执行完毕');
                el.style.background = '#fff';
            }
        }
    }
3. css:
    p{transition: 1s background;}
```
> 当只用 JavaScript 过渡的时候，在 enter 和 leave 中必须使用 done 进行回调。否则，它们将被同步调用，过渡会立即完成。

#### transition-group 多组标签作用
```
1. html:
    <input type="text" ref="in">
    <button @click="addAction">添加</button>
    <ul>
        <transition-group name="fade">
            <li v-for="(item, index) in list" :key="item.id">
                {{item.val}}
                <button @click="deleteAction(index)">X</button>
            </li>
        </transition-group>
    </ul>
2. js:
    export default {
        data(){
            return {
                isSelected: false,
                list: [],
                flag: 0
            }
        },
        methods: {
            addAction(){
                this.list.push({
                    val: this.$refs.in.value,
                    id: this.flag++
                });
                this.$refs.in.value = '';
            },
            deleteAction(index){
                this.list.splice(index, 1);
            }
        }
    }
3. css:
    .fade-enter{
        opacity: 0;
    }
    .fade-enter-to{
        opacity: 1;
    }
    .fade-enter-active{
        transition: 1s opacity;
    }

    .fade-leave{
        opacity: 1;
    }
    .fade-leave-to{
        opacity: 0;
    }
    .fade-leave-active{
        transition: 1s opacity;
    }
```
