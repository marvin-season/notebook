## Vue

### 数据代理

```javascript
let gender = 'male'

let person = {
  name: '章三',
  age: 22
}

Object.defineProperty(person, 'gender', {
  value: '男',
  enumerable: true,//配置属性可以枚举
  writable: true, // 可修改
  configurable: true,//可以删除

  set(value){
    // 修改处罚
    gender = value;
  },
  get(){
    // 访问触发
    return gender;
  }
})
```

### MVVM

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MVVM</title>
    <script src="./vue.js"></script>
</head>

<body>
    //VIEW
    <div id="app">   
        {{ message }}
    </div>
    
    <script>
    //VIEW-MODEL
        var app = new Vue({
            el: '#app',
            //MODEL
            data: {
                message: 'Hello Vue!'
            }
        })
    </script>
</body>

</html>
```

### 指令

```html
v-model: 双向数据绑定
v-bind: 单向数据绑定
v-html/text: 插入html/文本
v-if & v-show: 渲染/显示
v-on: 绑定事件
```

### 过滤器

```javascript
// 自定义全局过滤器
Vue.filter('format1', function (value) {
    console.log(value);
    return value.replace(/小/g, "大");
});
Vue.filter('format2', function (value) {
    console.log(value);
    return value.replace(/生/g, "神");
});


```

### 使用过滤器

```javascript
<div id="app1">
    <p>{{name|format1|format2}}</p>
</div>
var app1 = new Vue({
    el: "#app1",
    data: {
        name: "小学生马文澍, 小学生李洋, 小学生梅宇新, 小学生董成龙"
    }
});
```

### 全局组件

```javascript
//    1、创建一个组件构造器
let Profile = Vue.extend({
    template: `
<div>
<img src="./images/bg.jpg" width="20%"/>
<p>我是组件的一个段落</p>  
</div>
`
});
//  2、注册一个组件
Vue.component("abc", Profile);
var app = new Vue({
    el: "#app",
    data: {

    }
})
```

`每个组件只能有一个根元素`

#### 简化创建组件

```javascript
//定义一个组件的模板
<template id="abc">
    <div>
    	<img src="./images/bg.jpg" width="20%" alt="">
    	<p>我是一个段落</p>
	</div>
</template>

//使用这个模板
Vue.component("abc", {
    // 寻找 id = "abc" 的模板
    template: "#abc"
});
var app = new Vue({
    el: "#app",
    data: {

    }
});

```

### 局部组件

```javascript
<div id="app1">
    <abc></abc>
</div>
<div id="app2">
    <abc></abc>
</div>
<script src="./vue.js"></script>
<script>
    var app1 = new Vue({
        el: "#app2",
        components: {
            "abc": {
                template: "#abc"
            }
        }
    });
</script>
```

##### 组件内部属性的使用

**data**

```js
//组件内的错误的书写方式
new Vue({
    el: "#app",
    data:{
        
    }
})
//正确的书写方式
new Vue({
    el: "#app",
    data: function(){
        return{
            //...
        }
    }
})
// 取而代之的是，一个组件的 data 选项必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝：

```

## 全局事件总线

- PassengerA
- PassengerB

- $bus ¡ PassengerA 向总线订阅事件 PassengerB 向总线发布事件

PassengerA <------订阅----- $bus <-----发布-----PassengerB

**main.js**

```javascript
new Vue({
    render: h => h(App),
    beforeCreate() {

        // 往Vue 原型上放置一个消息总线对象， 这个对象全局可操作
        // this指的是vc的实例对象 ，Vue.prototype = VueComponent.prototype.__proto__
        Vue.prototype.$bus = this
    }
}).$mount('#app')
```

**类消息订阅者(接受者)**

```javascript
function subscribe() {
    console.log('向消息总线上订阅事件:', 'getMsgB')
    this.$bus.$off('getMsgB')
    // 向vm原型对象上的总线对象订阅事件
    this.$bus.$on('getMsgB', (data) => {
        console.log('收到订阅消息 => ', data)
    })
}
```

**类消息发布者(发送者)**

```javascript
publish()
{
    // 向vm原型对象上的总线对象发布事件
    console.log('消息已发布: ', this.msgB)
    this.$bus.$emit('getMsgB', this.msgB)
}
```

****

## 发布订阅

```nodejs
# 安装工具包
npm i pubsub-js
```

**订阅消息**

```javascript
//  使用pubsub-js发布订阅模型传输数据
function subscribe() {
    // 使用订阅号取消订阅
    this.subId && pubsub.unsubscribe(this.subId)

    console.log('订阅消息:', 'getMsgB')
    // 订阅消息得到一个订阅号
    this.subId = pubsub.subscribe('getMsgB', (msgName, data) => {
        console.log('收到订阅消息 => ', msgName, ' : ', data)
    })
}
```

**发布消息**

```javascript
function publish() {
    pubsub.publish('getMsgB', this.msgB)
    console.log('消息已发布: ', this.msgB)
}
```

## 远程调用

**解决跨域**

```javascript
// 本地地址: http://localhost:8080
// 远程地址: http://localhost:9000

// 浏览器同源策略，请求到达服务器后返回，浏览器查看响应头，
// 发现没有相关的响应头，响应数据被浏览器阻塞
```

**方案一**
jsonp
<br>
**方案二**
服务端塞响应头 cors
<br>
**方案三**
代理服务器: 代理主机地址同当前主机，所以请求代理服务器浏览器不会阻塞响应的数据，代理主机与服务器之间不存在跨域(浏览器的同源策略限制)

```javascript
// vue.config.js
// 1、
module.exports = {
    devServer: {
        // 配置实际转发主机地址　
        proxy: 'http://localhost:9000'
    }
}
// 2、本机向代理服务器发送请求
axios.get('http://localhost:8080/students').then((resp) => {
    console.log('请求成功: ', resp.data)
}, (err) => {
    console.log('请求失败: ', err)
})
```

**完整的代理配置解决跨域问题**<br>
类似Nginx

vue.config.js

```javascript
module.exports = {
    devServer: {
        // 正向代理
        proxy: {
            // 匹配路径路由
            '/student': {
                // 转发到服务器
                target: 'http://localhost:9000/',
                // 正则路径替换 
                // http://localhost:9000/student --> http://localhost:9000/
                pathRewrite: {'^/student': ''},
                ws: true,
                changeOrigin: true
            },
            // 匹配多个路径
            '/car': {
                target: 'http://localhost:5001',
                pathRewrite: {'^/car': ''},
            }
        }
    }
}
```

发送请求

```javascript
function server1() {
    axios.get('http://localhost:8080/student/students').then((resp) => {
        console.log('请求成功: ', resp.data)
    }, (err) => {
        console.log('请求失败: ', err)
    })
}

function server2() {
    axios.get('http://localhost:8080/car/cars').then((resp) => {
        console.log('请求成功: ', resp.data)
    }, (err) => {
        console.log('请求失败: ', err)
    })
}
```

## 使用路由

```text
路由就是一系列映射关系
安装： npm i vue-router -S
```

**配置路由插件**

```javascript
import Vue from 'vue'
import VueRouter from "vue-router";

Vue.use(VueRouter)

export default new VueRouter({
    routes: [
        {
            path: '/path1',
            name: '',
            component: () => import(''),
            children: [
                {
                    path: 'path2/:id/:name', // 提供参数占位符
                    name: '',
                    props: function ($route) {
                        return {p: 'p'}
                        // 提供给组件接受参数
                        // props: ['p']
                    },
                    meta: {}, // 配置额外属性
                }
            ]
        }
    ]
})
```

**参数接受**

```javascript
// 当前组件对应的路由对象 this.$route
this.$route.query
this.$route.params
// 全局的路由器 this.$router
```

### 路由守卫

```javascript
//全局前置守卫：初始化时执行、每次路由切换前执行
router.beforeEach((to, from, next) => {
    console.log('beforeEach', to, from)
    if (to.meta.isAuth) { //判断当前路由是否需要进行权限控制
        if (localStorage.getItem('school') === 'atguigu') { //权限控制的具体规则
            next() //放行
        } else {
            alert('暂无权限查看')
            // next({name:'guanyu'})
        }
    } else {
        next() //放行
    }
})

//全局后置守卫：初始化时执行、每次路由切换后执行
router.afterEach((to, from) => {
    console.log('afterEach', to, from)
    if (to.meta.title) {
        document.title = to.meta.title //修改网页的title
    } else {
        document.title = 'vue_test'
    }
})

// 独享守卫

const routes = [
    {
        path: '',
        // ...
        beforeEnter(to, from, next) {
            console.log('beforeEnter', to, from)
            if (to.meta.isAuth) { //判断当前路由是否需要进行权限控制
                if (localStorage.getItem('school') === 'atguigu') {
                    next()
                } else {
                    alert('暂无权限查看')
                    // next({name:'guanyu'})
                }
            } else {
                next()
            }
        }
    }
]

// 组件守卫
//进入守卫：通过路由规则，进入该组件时被调用
function beforeRouteEnter(to, from, next) {
}

//离开守卫：通过路由规则，离开该组件时被调用
function beforeRouteLeave(to, from, next) {
}
```

## vuex

```text
vuex作为一个插件， 可以为vue提供集中的数据管理服务
```

```javascript
// 引入插件
import vuex from 'vuex'
// 使用插件
vue.use(vuex)

new Vuex.Store({
    state: {},
    getters: {},
    mutations: {},
    actions: {}
})
```

**具体使用查看官方文档**

vuex模块化管理

```javascript
// 定义
new Vuex.Store({
    modules: {
        moduleA: {
            namespaced: true,
            state: {
                a: 1
            },
            getters: {},
            mutations: {
                add(context, param) {

                }
            },
            actions: {}

        },
        moduleB: {
            namespaced: true,
            state: {
                b: 2
            },
            getters: {},
            mutations: {},
            actions: {}
        }
    }
})

// 便捷使用

new Vue({
    data: {},
    // ...
    computed: {
        ...mapState('moduleA', {a: 'a'}),
        ...mapState('moduleB', ['b']),
        ...mapGetters('moduleB', [''])
    },
    methods: {
        ...mapMutations('moduleA', {}),
        ...mapMutations('moduleB', {})

    }
})

// 直接使用
this.$store.commit('moduleA/add', 'param')
```

# Vue3

