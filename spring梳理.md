```tex
bean的使用： 
加载-》解析-〉创建实例-》使用-》销毁

ioc： 容器， 存放bean对象-》存储的数据结构
	list
	set
	map ✅
```

**容器**

```properties
创建对象的方式：
	1,new 
	2,反射
	
创建的对象每次都是新的还是公用的？
	spring中默认的是单利的
	
反射的代码：
  Class.forName()
  this.getClassO
  Object.class
  constroctor = this.getDeclearConstructor()
  constructor.newInstance();
 
容器中的beans:
	xml文件中存储bean的描述信息，
	
	抽象层解析配置文件的内容
	BeanDefination:
		BeanFactory PostProcessor: 后置处理 如占位符
	Beanfactory:
	
bean的生命周期：
	实例化->填充属性->执行aware接口->bean处理 beanPostprocessorBefore beanPostprocessorAfter->完整对象
		aware接口存在的意义：方便bean对象获取容器中的属性值
	
```

```properties
实例化:
初始化:给属性赋值， 1/填充属性。 2/执行初始化方法 
```



