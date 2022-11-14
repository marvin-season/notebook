# 原理基础

## 观察者模式

别名**发布订阅模式**

例如：

```js
Jetbrain 公司正在开发一款新的代码编辑器：fleet，进入官网可以看到左上角有一个订阅内容，
订阅者需要输入邮箱等可以确认身份的信息，当fleet有更新时，Jetbrain公司 就会给你填写的邮箱发一份邮件
```

上述便是观察者模式的流程，数据模型简化如下：

```tsx
interface Subscribe {
  id: string; //'类似邮箱：唯一',
}

interface Publisher {
  id: string; // '一般都有个id，标明唯一性'
  list: Set<Map<string, string>>
}
```

Subscribe注册自己到Publisher中的list，有更新时Publisher广播通知list中的每一员

## Object.defineProperty()

The static method **`Object.defineProperty()`** defines a new property directly on an object, or modifies an existing property on an object, and returns the object.

可以使用这个api定制化对象，例如:

```js
Object.definrProperty({}, name, {
  value: '',
  set(newValue){
    value = newValue;
  },
  get(){
    return value;
  }
})
```

其中value为对象的值，set、get函数则会在操作、读取对象时调用。

基于此，vue2在set、get函数中加入了更新、读取数据时的额外逻辑操作，set时通知页面更新数据，读取时建立发布订阅关系

## computed原理

![vue2computed原理](/Users/marvin010528/workspace/note/Vue2源码赏析记录.assets/vue2computed原理.png)

每个计算属性对应一个自己的watcher，当初次读取这个属性时，watcher会调用内部的getter方法；

watcher内部的getter方法是外部传入的回调，对于计算属性这个getter是定义计算属性时的getter函数，

vue2中的computed有缓存的功能，实现方法是 替换计算属性原来的getter函数（类似于责任链），在替换后的getter函数中实现缓存的功能