# 设计模式

### 目录

[01-创建者模式](01-chuang-jian-xing-mo-shi/)

* [ ] [工厂方法](01-chuang-jian-xing-mo-shi/gong-chang-fang-fa.md)
* [ ] [抽象工厂](01-chuang-jian-xing-mo-shi/chou-xiang-gong-chang.md)
* [ ] [生成器模式](01-chuang-jian-xing-mo-shi/sheng-cheng-qi-mo-shi.md)
* [ ] [原型模式](01-chuang-jian-xing-mo-shi/yuan-xing-mo-shi.md)
* [ ] [单例模式](01-chuang-jian-xing-mo-shi/dan-li-mo-shi.md)

[02-结构型模式](02-jie-gou-xing-mo-shi/)

* [ ] [适配器模式](02-jie-gou-xing-mo-shi/kuo-pei-qi-mo-shi.md)
* [ ] [桥接模式](02-jie-gou-xing-mo-shi/qiao-jie-mo-shi.md)
* [ ] [组合模式](02-jie-gou-xing-mo-shi/zu-he-mo-shi.md)
* [ ] [装饰模式](02-jie-gou-xing-mo-shi/zhuang-shi-mo-shi.md)
* [ ] [外观模式](02-jie-gou-xing-mo-shi/wai-guan-mo-shi.md)
* [ ] [享元模式](02-jie-gou-xing-mo-shi/xiang-yuan-mo-shi.md)
* [ ] [代理模式](02-jie-gou-xing-mo-shi/dai-li-mo-shi.md)

[03-行为模式](03-hang-wei-mo-shi/)

* [ ] [责任链模式](03-hang-wei-mo-shi/ze-ren-lian-mo-shi.md)
* [ ] [命令模式](03-hang-wei-mo-shi/ming-ling-mo-shi.md)
* [ ] [迭代器模式](03-hang-wei-mo-shi/die-dai-qi-mo-shi.md)
* [ ] [中介者模式](03-hang-wei-mo-shi/zhong-jie-zhe-mo-shi.md)
* [ ] [备忘录模式](03-hang-wei-mo-shi/bei-wang-lu-mo-shi.md)
* [ ] [观察者模式](03-hang-wei-mo-shi/guan-cha-zhe-mo-shi.md)
* [ ] [状态模式](03-hang-wei-mo-shi/zhuang-tai-mo-shi.md)
* [ ] [策略模式](03-hang-wei-mo-shi/ce-lve-mo-shi.md)
* [ ] [模板方法](03-hang-wei-mo-shi/mo-ban-fang-fa.md)
* [ ] [访问者模式](03-hang-wei-mo-shi/fang-wen-zhe-mo-shi.md)

### 什么是设计模式

设计模式是前人总结出软件设计中常见问题的典型解决方案。可用于解决代码中反复出 现的设计问题。算法更像是菜谱：提供达成目标的明确步骤。而模式更像是 蓝图：你可以看到最终的结果和模式的功能，但需要自己确 定实现步骤。

### 原则设计

* **封装变化内容**：找到程序中的变化内容并将其与不变的内容区分开。
* **面向接口进行开发， 而不是面向实现**：面向接口进行开发，而不是面向实现；依赖于抽象类型，而不是具体类。
* **组合优于继承**：
  * 子类不能减少超类的接口。
  * 在重写方法时，你需要确保新行为与其基类中的版本兼容。
  * 继承打破了超类的封装。
  * 子类与超类紧密耦合。
  * 通过继承复用代码可能导致平行继承体系的产生。
* **SOLID 原则**： 
  * **单一职责原则**：修改一个类的原因只能有一个。
  * **开放封闭原则**：对于扩展，类应该是“开放”的；对于修改，类则应 是“封闭”的。
  * **依赖倒置原则**：高层次的类不应该依赖于低层次的类。 两者都应该依赖于抽象接口。抽象接口不应依赖于具体实现。具体实现应该依赖于抽象接口。
  * **接口隔离原则**：客户端不应被强迫依赖于其不使用的方法。
  * **里氏替换原则**：当你扩展一个类时， 记住你应该要能在不修改客户端 代码的情况下将子类的对象作为父类对象进行传递。

Reference:

1. [设计模式组合讲解](https://refactoringguru.cn/design-patterns/creational-patterns)
2. [深入设计模式](https://refactoringguru.cn/design-patterns)



