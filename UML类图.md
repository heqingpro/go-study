### UML 类图

#### 1、类图基础属性

```none
-表示private  
#表示protected 
~表示default,也就是包权限  
_下划线表示static  
斜体表示抽象  
```

#### 2、类与类之间关系

-  **泛化 (Generalization)**
  - 泛化(Generalization)表示类与类之间的继承关系，接口与接口之间的继承关系，或类对接口的实现关系
  - 继承 **空心三角形+实线**表示
  - 例：鸟类继承抽象类动物
- **实现 (Realization)**
  - 实现表示一个class类实现interface接口（可以是多个）的功能
  - 使用**空心三角形+虚线**表示
  - 例：大雁需要飞行，就要实现飞()接口
- **关联 (Association)**
  - 对于两个相对独立的对象，当一个对象的实例与另一个对象的一些特定实例存在固定的对应关系时，这两个对象之间为关联关系。
  - 关联关系用**实线箭头**表示。
  - 例：企鹅下蛋需要‘知道’气候的变化，需要‘了解’气候规律。当一个类‘知道’另一个类时，可以用关联。
- **聚合 (Aggregation)**
  - 表示一种弱的‘拥有’关系，即has-a的关系，体现的是A对象可以包含B对象，但B对象不是A对象的一部分。 **两个对象具有各自的生命周期**。
  - 聚合关系用**空心的菱形+实线箭头**表示。
  - 例：每一只大雁都属于一个大雁群，一个大雁群可以有多只大雁。当大雁死去后大雁群并不会消失，两个对象生命周期不同。
- **组合 (Composition)**
  - 组合是一种强的‘拥有’关系，是一种contains-a的关系，体现了严格的部分和整体关系，**部分和整体的生命周期一样**。
  - 组合关系用**实心的菱形+实线箭头**表示，还可以使用连线两端的数字表示某一端有几个实例。
  - 例：鸟和翅膀就是组合关系，因为它们是部分和整体的关系，并且翅膀和鸟的生命周期是相同的。
- **依赖 (Dependency)**
  - 对于两个相对独立的对象，当一个对象负责构造另一个对象的实例，或者依赖另一个对象的服务时，这两个对象之间主要体现为依赖关系。
  - 依赖关系用**虚线箭头**表示
  - 例：动物依赖氧气和水。调用新陈代谢方法需要氧气类与水类的实例作为参数