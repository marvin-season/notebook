 



### vue2

数据代理: person 中的gender 代理 gender

```js
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

vue对象

```js
```



### vue3

