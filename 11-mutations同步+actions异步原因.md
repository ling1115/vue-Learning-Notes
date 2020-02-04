- 在组件只能可以通过this.$store.state访问到状态，为什么不能在组件中直接修改state?
  > 为了保证数据时单向流动的，只能在store中操作数据，而组件只能够使用状态，不能修改，

- 为什么mutations同步，而actions可以异步？
  > mutations中必须是同步操作，而在actions中可以有异步操作，
所有mutations才能操作state,如果actions中能操作state的话，数据会变得难以管理

  > mutations中的数据变化是可以被vue的开发工具vue-devTools观测到。
actions中的数据变化是不可以被vue的开发工具vue-devTools观测到。
所以需要在mutations中做操作。

- 什么时候应vuex？
  > 多个组件共享数据，中大型项目，

### vuex以模块使用
> 设置命名空间为true,即namespaced:true 可以让模块下的getters和index.js 中的getters区分开

> vuex中的store分模块管理，需要在store的index.js中引入各个模块，为了解决不同模块命名冲突的问题，将不同模块的namespaced:true，之后在不同页面中引入getter、actions、mutations时，需要加上所属的模块名

#### 模块没有设置命名空间的getters访问方式
```
// ...mapGetters(['nameLength', 'count'])
// ...mapGetters({
//     len: 'nameLength',
//     num: 'count'
// })

给模块设置命名空间的访问方式
...mapGetters({
    len: 'nameLength',
    num: 'cart/count'
})
```
#### 模块没有设置命名空间的mutations访问方式
```
// 触发store root的mutations事件 和 模块中的mutations 都会触发
// this.$store.commit('setLon', value);

// 触发设置了命名空间模块的mutations事件
this.$store.commit('address/setLon', value);
this.$store.commit('address/setLat', value);
```
#### 模块没有设置命名空间的actions访问方式：
> 和mutations一样

