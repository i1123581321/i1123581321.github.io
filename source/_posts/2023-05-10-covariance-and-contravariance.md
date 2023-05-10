---
title: 协变与逆变
tags:
  - Type System
categories:
  - computer science
mathjax: false
documentclass: ctexart
classoption: UTF8
geometry:
  - margin=1in
  - a4paper
date: 2023-05-10 20:43:51
---

# 协变与逆变

## 前言

程序语言类型系统中的协变（covariance）与逆变（contravariance）是支持子类型和泛型（即类型参数化）的程序语言中，类型构造器的一种性质。

举一个简单的例子，Java 中的 `List` 就是一个类型构造器。将 `Integer` 作为参数传入该构造器，即可得到一个具体的类型：`List<Integer>`。

本文的后续将使用如下类型关系：

* 基本类型 `Object`
* `Object` 的子类型 `Animal`
* `Animal` 的子类型 `Cat` 与 `Dog`

时刻记住子类型需要遵循 Liskov substitution principle，即任何使用父类型的地方，都可以用子类型进行代替。

由此可以得出对协变和逆变的定义。考虑类型构造器 $T$ 和类型 $A, B$，其中 $A$ 是 $B$ 的子类型，记作 $A \leq B$

* 如果 $T(A) \leq T(B)$，则称 $T$ 为*协变*的
* 如果 $T(B) \leq T(A)$，则称 $T$ 为*逆变*的
* 如果 $T(A)$ 与 $T(B)$ 不存在子类型关系，则称 $T$ 为*不变*的

## 协变与逆变的规则

一个类型构造器应当是协变、逆变或不变的，取决于该类型构造器所支持的操作类型。通常来说需要遵循以下原则才能确保类型安全

* 如果该类型构造器*生产*其类型参数的对象，则该类型构造器是协变的
* 如果该类型构造器*消费*其类型参数的对象，则该类型构造器是逆变的
* 如果该类型构造器同时*生产和消费*其类型参数的对象，则该类型构造器是不变的

下面将从几个实际的例子出发解释为什么会有这样的规则。

### 协变

考虑一个如下定义的类型构造器

```java
public interface Producer<T> {
    T produce();
}
```

以及构造出的类型 `Producer<Animal>`。现在考虑该类型的子类型 `Producer<T>`，那么作为参数的类型 `T` 应当满足怎样的条件呢？

考虑该类型定义的规约，类型 `Producer<Animal>` 的实例在调用 `produce` 方法时需要返回一个 `Animal` 类型的对象。如果将类型看作是操作的集合（i.e. `Animal` 类型所定义的成员方法），则显然，根据 LSP，作为子类型的 `Producer<T>` 的实例应当返回一个*至少*支持 `Animal` 类型所支持所有操作的新对象，才能满足 LSP。显然类型 `T` 只能是 `Animal` 或其子类型。

上述结论同样可以简单地通过反证法得到。考虑 `T` 类型为 `Animal` 的父类型，如 `Object`。则在使用子类型 `Producer<Object>` 实例替换父类型 `Producer<Animal>` 时，调用方通过父类型的方法签名期望得到一个支持 `Animal` 类型所有操作的对象，但实际上子类型返回了一个仅支持 `Object` 所有操作的对象，如果 `Animal` 的操作集合是 `Object` 的严格超集（这在有子类型的系统中十分常见），则调用方在调用 `Animal` 类型独有操作时会产生运行时错误，这显然破坏了 LSP。

### 逆变

考虑一个如下定义的类型构造器

```java
public interface Consumer<T> {
    void consume(T t);
}
```

以及构造出的类型 `Consumer<Animal>`。现在考虑该类型的子类型 `Consumer<T>`。

同样，我们考虑该类型构造器的方法 `consume` （在分析时均只考虑被调用方内部的规约，而不考虑调用方如何处理参数或结果）。根据类型签名，该实例期望传入对象支持 `Animal` 所支持的所有操作，则子类型 `Consumer<T>` 如果想替换父类型实例，也只能假定传入对象*至多*支持 `Animal` 类型所支持的所有操作。此时 `T` 为 `Animal` 类的父类型。

使用反证法的思路同上节。

### 不变

考虑一个最简单的容器作为类型构造器

```java
public interface Container<T> {
    T get();

    void put(T t);
}
```

则构造出的类型不应当具有子类型关系。考虑 `Container<Animal>` 与 `Container<Cat>`，无论前者是后者的子类型还是后者是前者的子类型，都会在 `get` 或 `put` 方法上破坏 LSP。因此一个支持读写的容器类型构造器都应当实现为不变的。

### 多参数

上述情况均只考虑一个类型参数的情况，在类型参数有多个时，不难得出类型构造器对生产的类型应当是协变的，对消费的类型应当是逆变的。考虑如下多参数类型构造器

```java
public interface Action <T, R> {
    R action(T t);
}
```

考虑构造得到的类型 `Action<Animal, Animal>`，则只有 `Action<Object, Cat>` 或 `Action<Object, Dog>` 是其子类型（不考虑 `T, R` 为 `Animal` 的情况）

## Java 中的逆变与协变

在 Java 中，类型构造器默认都是不变的。如果想实现协变与逆变的效果，可以在声明构造类型时使用相应的通配符。考虑如下定义：

```java
public interface Action <T, R> {
    R action(T t);
}
```

则如果想构造一个实际类型 `Action<Animal, Animal>`，应当将变量类型声明为

```java
Action<? super Animal, ? extends Animal> a;
```

此时变量 `a` 可以引用对应的子类型且只要编译通过，便不会破环 LSP。

```java
a = new Action<Object, Cat>(){ // valid
    @Override
    public Cat action(Object o) {
        return null;
    }
};
```

即 Java 中无法将类型构造器定义为协变或逆变，程序员只能根据类型构造器对类型参数对象的使用方式（生产或消费），在构造具体类型时*正确地*使用通配符实现协变和逆变的语义。事实上，Java 编译器会避免错误的通配符使用。在使用 `<? extends T>` 形式的通配符时，所有以类型参数为参数类型的方法，传入实参时只能传入 null。而当使用 `<? super T>` 时，所有以类型参数为返回值类型的方法所返回的对象只能使用 `Object` 接收。

```java
List<? extends Integer> a = new ArrayList<>();
a.add(1);               // compile error
a.add(null);            // valid
Integer i = a.get(0);   // valid

List<? super Integer> b = new ArrayList<>();
b.add(1);               // valid
i = b.get(0);           // compile error
Object o = b.get(0);    // valid
```

Java 在实现数组时将其实现为了协变，这也带来了潜在的风险

```java
Animal[] a;
a = new Cat[10]; // valid
a[0] = new Animal(); // ArrayStoreException
```

上述代码会通过编译，然后在运行时抛出异常。

## C# 中的协变与逆变

C# 中通过 `in` 和 `out` 关键词，可以定义类型构造器相对于类型参数的协变或逆变。在不使用关键词时，类型构造器默认是不变的。

```c#
delegate T Producer<out T>();
```

则如下代码是合法的

```c#
Producer<Animal> producer = () => new Cat();
```