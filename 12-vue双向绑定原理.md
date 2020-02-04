[TOC]

## 一、设计思路
   MVVM框架相对传统的dom操作模式，最大的优点就是数据的双向绑定。它借鉴了桌面应用程序MVC的设计思想，Model与View之间通过ViewMode关联，ViewModel负责将Model数据的变化显示到View上，通过将View的改变反馈到Model上这就是我们常说的双向绑定数据机制。



那如何设计MVVM模型模型呢。需要解决两个关键问题：

- 1、如何知道数据更新。

- 2、数据更新后，如何通知变化。

下面我们就分别介绍下vue是如何实现的，理解了这两点，基本上也就明白了双向绑定的机制。

## 二、数据劫持
在ES5中有Object.defineProperty()方法，它能监听各个属性的set和get方法。
```
 let data ={name:'tcy'}
  Object.defineProperty(data,'name',{
  	set: function(newValue) {
        console.log('更新了data的name:' + newValue);
    },
    get: function() {
        console.log('获取data数据name');
    }
  })
data.name="fyn";//更新了data的name:fyn
data.name;//获取data数据name
```
- Object.defineProperty()方法，有三个参数，分别为待监听的数据对象，待监听的属性，以及对set，get的监听方法。

- 上例中，对data对象的name属性进行监听，当执行"data.name='fyn'"触发set方法，执行"data.name"触发get方法。

- vue正是采用了该方法，对data的属性进行劫持。我们来模拟实现其劫持的过程。
```
//模拟vue的data数据
// var vm = new Vue(
// {
//   data:{
//       name:'tcy',
//       age:'20'
//   }
// }
// 	)
let data ={name:'tcy',age:'20'}
function observe(data){
	//获取所有的data数据对象中的所有属性进行遍历
    const keys = Object.keys(data)
    for (let i = 0; i < keys.length; i++) {
    	let val = data[keys[i]];
       defineReactive(data, keys[i],val)//为每个属性增加监听
    }
}
function defineReactive(obj,key,val){
   Object.defineProperty(obj, key, {
    enumerable: true,//可枚举
    configurable: true,//可配置
    get: function reactiveGetter () {
      //模拟get劫持
      console.log("get劫持");
      return val;
    },
    set: function reactiveSetter (newVal) {
       	//模拟set劫持
     console.log("set劫持,新值："+newVal);
     val = newVal;
    }
  })
}
observe(data);
data.name="fyn";//set劫持,新值：fyn
console.log(data.name);//get劫持,fyn
```
- data模拟vue.data对象，observer中对data的属性进行遍历，调用defineReactive对每个属性的get和set方法进行劫持。

- 由此，data数据的任何属性值变化，都可以监听和劫持，上述的第一个问题就解决了。

- 那view端的数据变化是如何知道的呢，view端改变数据的组件无外乎input，select等，可以用组件的onchange事件监听，这里就不再重点描述。

- 接下来就要解决第二个问题，监听到数据变化后如何通知。

## 三、发布与订阅
vue在双向绑定的设计中，采用的是观察-订阅模式，前面所讲的数据劫持，其实就是为属性创建了一个观察者对象，监听数据的变化。接下来就是创建发布类和订阅类，如下：

![image](https://img-blog.csdnimg.cn/20190420173653318.png)

- observer，创建数据监听，并为每个属性建立一个发布类。

- Dep是发布类，维护与该属性相关的订阅实例，当数据发生更新时，会通知所有的订阅实例。

- Watcher是订阅类，注册到所有相关属性的Dep发布类中，接受发布类的数据变更通知，通过回调，实现视图的更新。

下面我们就模式发布和订阅的过程。
```
//对象数据
let data ={name:'tcy',age:'20',sex:'male'};
//发布器的uid
let uid=0;
//发布器
class Dep{
   constructor () {
      this.id = uid++//发布器的标识，每次加1
      this.subs = []//订阅者集合
    }
    //添加订阅者实例对象
    addSub(watcher){
      this.subs.push(watcher);
    }
    //移除订阅者实例对象
    removeSub (watcher) {
      remove(this.subs, watcher)
    }
    // 依赖收集函数，在 getter 中执行，在 Dep.target 上找到当前 watcher，并添加依赖
   depend() { 
       Dep.target && Dep.target.addDep(this)
    }
    //通知所有订阅者
    notify () {
    	const subs = this.subs.slice()
    	for (let i = 0, l = subs.length; i < l; i++) {
        subs[i].update();//更新
    }
}
}
//记录当前的watcher实例
Dep.target = null;
const targetStack = []
function pushTarget (_target) {
   if (Dep.target) targetStack.push(Dep.target)
	Dep.target = _target
}
function popTarget () {
  Dep.target = targetStack.pop()
}
 
//订阅者
class Watcher{
   constructor (
    vm,//vm数据对象
    expOrFn,//待监听的属性表达式
    cb//监听到变化后的回调函数
    ){
	this.vm=vm;
	this.expOrFn = expOrFn;
	this.cb = cb;
	this.value= this.get();
    }
   //添加自身订阅者到发布器
   addDep (dep) {
   	dep.addSub(this)
   }
  //通知更新
  update(){
    this.run();
  }
  //实现视图的更新
  run(){
     let oldValue = this.value//更新前数据
     let value =  this.get();//获取最新值
     if(value != oldValue){
      this.cb.call(this.vm);
  }
}
  //获取value值，并进行依赖收集
   get(){
     //将自身watcher订阅实例设置给Dep.target
     pushTarget(this);
     //这一步很重要，获取属性值，同时将订阅者实例添加到发布器中
      let value = this.expOrFn.call(this.vm);
      //将订阅实例从target栈中取出并设置给Dep.target
      popTarget();
      return value;
  }
}
 
function observer(data){
	//获取所有的属性进行遍历
	const keys = Object.keys(data)
	for (let i = 0; i < keys.length; i++) {
		let val = data[keys[i]];
       defineReactive(data, keys[i],val)//增加监听
   }
}
 
 
function defineReactive(obj,key,val){
    //为每个键都创建一个 dep
    let dep = new Dep();
    Object.defineProperty(obj, key, {
    enumerable: true,//可枚举
    configurable: true,//可配置
    get: function reactiveGetter () {
      if (Dep.target) {
      	dep.depend();
      }
      return val;
  },
  set: function reactiveSetter (newVal) {
       	val = newVal;
      dep.notify(); // 通知所有订阅者
  }
})
}
 
 
//第一步，为每个属性创建一个发布器，并设置set，get劫持
observer(data);
 
//第二步，创建监听并实现依赖收集
var watcher = new Watcher(data,() => {
  "name"+data.name + "age"+data.age
},() => {
  console.log("实现视图更新");
});
 
//第三步，数据变化，触发视图更新
data.name="fyn";//实现视图更新
data.sex="female";
 ```
下面简单介绍下这几个类

- 1、发布器Dep类

构造函数中定义了subs集合，保存所有注册的订阅者实例，uid为发布器对象的标识。

addSub,removeSub方法，添加和移除的订阅者，维护实例集合。

depend方法，将当前的watcher实例对象添加到subs集合中。这是会涉及到依赖收集，后面会讲到。

notify方法，遍历subs中所有维护的订阅者实例，并进行更新操作。

- 2、订阅者Watcher类，

在构造函数的入参中设置

vm-组件对象，即本例的data

expOrFn--属性或者表达式

cb--更新视图的回调函数

在实例化的时候，调用get方法，获取当前的监听属性的值，同时触发该属性的get方法(参见defineReactive的get方法)，调用dep.depend()将订阅实例添加上发布器Dep中(记住，这步很重要)

run方法，收到变化通知，比较数据的前后值，调用cb实现视图的更新。

- 3、defineReactive方法

该方法与前面的比较，总体框架是一致的，但增加了一些代码。

let dep = new Dep();

为数据对象中的每个属性建立一个发布器。

在get发方法中，将订阅者加入到该属性的发布器subs中

在set中，数据发生变化，调用dep.notify()通知所有的订阅者，最终执行run方法，比较value值是否发生了变化，如果是，则调用cb的回调方法进行视图更新。


---
如果上面的分析没看明白，没关系，我们用具体的实例，分析下其调用过程。

- 1、为每个属性创建一个发布器，并设置set，get劫持

执行observer(data);



- 2、第二步，创建监听，并实现依赖收集
```
我们考虑下，模板上有一段代码,"<p>name:{{data.name}},age:{{data.age}}",暂不考虑编译过程，翻译过来，

"name"+data.name + "age"+data.age
```
需要对这个表达式进行监听，我们期望name，age的属性发生任何变化，立即通知模板实现页面的刷新，因为data中还有个sex的属性，这个表达式没有涉及到，所以为了提高效率，这个属性的任何变化，无需实现监，这就是依赖收集的概念。

我们看下是如何做到的。

在创建这个实例的过程中，会调用get方法，执行下面一段代码：
```
let value = this.expOrFn.call(this.vm);
```
它会触发name，age属性的get劫持方法，调用dep.depend方法，将该监听加入到对应属性的dep对象中。



- 3、数据变化，触发视图更新

    - 当执行data.name="fyn"时，会被name属性的set方法劫持，调用dep.notify(),该属性的dep对象的subs是有watcher实例的，故执行该watcher实例的updata方法，实现视图更新。

    - 当执行data.sex="female"时，会被sex属性的set方法劫持，调用dep.notify(),但该属性的dep对象的subs是空值，不会有任何更新。

整个流程的方法调用示意图：
![image](https://img-blog.csdnimg.cn/20190420101300727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RjeTgz,size_16,color_FFFFFF,t_70)


四、总结
本文对vue的数据双向绑定主要流程和代码，以模拟的形式进行解释，vue的实际实现中，还有很多细节考虑，具体可以参考其源码observer目录下的相关文件。

------

原文链接：https://blog.csdn.net/tcy83/article/details/80959725
