---
description: 流行度：★★★★☆
---

# 抽象工厂

 **抽象工厂模式**是一种创建型设计模式， 它能创建一系列相关的对象， 而无需指定其具体类。

![](../.gitbook/assets/image%20%2827%29.png)

### 抽象工厂的适应场景

* 如果代码需要与多个不同系列的相关产品进行交互，但是由于无法提前获取相关信息，或者出于对未来扩展性的考虑，你不希望代码基于产品的具体类进行构建，在这种情况下，你可以使用抽象工厂。

> 抽象工厂为你提供了一个接口，可用于创建每个系列的产品的对象。只要代码通过该接口创建对象，那么你就不会生成与应用程序已生成的产品类型不一致的产品。

* 如果你有一个基于一组抽象方法的类，且其主要功能因此变得不明确，那么在这种情况下可以考虑使用抽象工厂模式。

> 在良好的程序中，每个类仅仅负责一件事。如果一个类与多种类型的产品进行交互，就可以考虑将工厂方法抽取到每个独立的工厂类或具备完整功能的抽象工厂类中。

### 实现方式

1. 以不同的产品类型与产品变体为维度绘制矩阵。
2. 为所有产品声明抽象产品类接口，然后让所有具体产品类实现这些接口。
3. 声明抽象工厂接口，并且在接口中为所有产品提供一组构建方法。
4. 为每种产品变体实现一个具体的工厂类。
5. 在应用程序种开发初始化代码。该代码根据应用程序配置当前环境，对特定具体的工厂类进行初始化，然后将该工厂对象传递给所有需要创建产品的类。
6. 找出代码中对所有产品构造函数的直接调用，将其替换为对工厂对象中相应的构建方法的调用。

### 抽象工厂的优点

* 你可以确保同一工厂生成的产品互相匹配
* 你可以避免客户端和具体产品代码的耦合
* 单一职责原则。你可以将产品生成的代码抽取到同一位置，使代码更加容易维护
* 开闭原则。向应用程序中引入新产品变体时，无需修改客户端代码

### 代码示例

```cpp
#include <iostream>
#include <string>

/*产品A的接口类*/
class AbstractProductA{
public:
    virtual ~AbstractProductA(){};
    virtual std::string UsefulFunctionA() const = 0;
};

/*具体产品*/
class ConcreteProductA1: public AbstractProductA{
public:
    std::string UsefulFunctionA() const override{
        return "The result of the product A1";
    }
};

class ConcreteProductA2: public AbstractProductA{
public:
    std::string UsefulFunctionA() const override{
        return "The result of the product A2";
    }
};

/*产品B的接口类*/
class AbstractProductB{
public:
    virtual ~AbstractProductB(){};
    virtual std::string UsefulFunctionB() const = 0;
    virtual std::string AnotherUsefulFunctionB(const AbstractProductA &collaborator) const = 0;
};

/*具体产品*/
class ConcreteProductB1: public AbstractProductB{
public:
    std::string UsefulFunctionB() const override{
        return "The result of the product B1";
    }
    
    std::string AnotherUsefulFunctionB(const AbstractProductA& collaborator) const override{
        const std::string result = collaborator.UsefulFunctionA();
        return "The result of the B1 collaborating with (" + result + ")";
    } 
};

class ConcreteProductB2: public AbstractProductB{
public:
    std::string UsefulFunctionB() const override{
        return "The result of the product B2";
    }
    
    std::string AnotherUsefulFunctionB(const AbstractProductA& collaborator) const override{
        const std::string result = collaborator.UsefulFunctionA();
        return "The result of the B2 collaborating with (" + result + ")";
    } 
};

/*抽象工厂类基类 用于定义接口*/
class AbstractFactory{
public:
    virtual AbstractProductA* CreateProductA() const = 0;
    virtual AbstractProductB* CreateProductB() const = 0;
};

/*针对每种不同的变种编写对应的工厂类，在类的内部创建对象并返回指向对象的指针*/
class ConcreteFactory1: public AbstractFactory{
public:
    AbstractProductA* CreateProductA() const override{
        return new ConcreteProductA1();
    }
    AbstractProductB* CreateProductB() const override{
        return new ConcreteProductB1();
    }
};

class ConcreteFactory2: public AbstractFactory{
public:
    AbstractProductA* CreateProductA() const override{
        return new ConcreteProductA2();
    }
    AbstractProductB* CreateProductB() const override{
        return new ConcreteProductB2();
    }
};

/*客户端代码只能和抽象工厂一起使用*/
void ClientCode(const AbstractFactory& factory){
    const AbstractProductA* product_a = factory.CreateProductA();
    const AbstractProductB* product_b = factory.CreateProductB();
    
    std::cout << product_b->UsefulFunctionB() << std::endl;
    std::cout << product_b->AnotherUsefulFunctionB(*product_a) << std::endl;
    
    delete product_a;
    delete product_b;
}

int main(int argc, char *argv[])
{
    std::cout << "Client: Testing client code with the first factory type: \n";
    ConcreteFactory1 *f1 = new ConcreteFactory1();
    ClientCode(*f1);
    delete f1;
    
    std::cout << std::endl;
    std::cout << "Client: Testing the same client code with the second factory type: \n";
    ConcreteFactory2 *f2 = new ConcreteFactory2();
    ClientCode(*f2);
    delete f2;
    
    return 0;
}
```

### **运行结果**

```text
Client: Testing client code with the first factory type:
The result of the product B1.
The result of the B1 collaborating with the (The result of the product A1.)

Client: Testing the same client code with the second factory type:
The result of the product B2.
The result of the B2 collaborating with the (The result of the product A2.)
```

### 

### Reference

1. [设计模式](https://refactoringguru.cn/design-patterns)

