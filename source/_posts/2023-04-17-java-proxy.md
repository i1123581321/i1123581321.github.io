---
title: Java 中的代理
tags:
  - Java
categories:
  - computer science
  - java
mathjax: false
documentclass: ctexart
classoption: UTF8
geometry:
  - margin=1in
  - a4paper
date: 2023-04-17 15:07:08
---



# Java 中的代理

## 前言

代理模式是设计模式的一种，通常用于为无法修改的对象扩展额外的功能。代理模式的实现通常是通过生成一个与被代理者拥有相同接口的代理对象，对代理对象的调用会被转发给实际的对象，而额外的功能则在转发过程中实现。运行期 AOP 通常就是通过代理模式实现的。

下面以一个简单的例子说明 Java 中不同方式的代理实现。假设现在有两个接口，分别管理用户与商品的添加和查询

```java
// UserService.java

public interface UserService {
    boolean add(String username);

    boolean exist(String username);
}


// ProductService.java

public interface ProductService {
    boolean add(long id);

    boolean exist(long id);
}
```

以及两个接口对应的实现

```java
// UserServiceImpl.java

public class UserServiceImpl implements UserService {
    @Override
    public boolean add(String username) {
        System.out.println("add user " + username);
        return true;
    }

    @Override
    public boolean exist(String username) {
        System.out.println("check user " + username);
        return true;
    }
}

// ProductServiceImpl.java

public class ProductServiceImpl implements ProductService {
    @Override
    public boolean add(long id) {
        System.out.println("add product " + id);
        return true;
    }

    @Override
    public boolean exist(long id) {
        System.out.println("check product " + id);
        return true;
    }
}
```

使用一个简单的客户类来调用提供的方法

```java
public class Client {
    private final ProductService productService;
    private final UserService userService;

    public Client(ProductService productService, UserService userService) {
        this.productService = productService;
        this.userService = userService;
    }

    public void action(){
        userService.add("Tom");
        userService.exist("Tom");
        productService.add(1L);
        productService.exist(1L);
    }
}
```

编写一个单元测试

```java
@Test
public void defaultTest(){
    Client client = new Client(new ProductServiceImpl(), new UserServiceImpl());
    client.action();
}
```

并运行

```console
$ mvn test -Dtest="ProxyTest#defaultTest"
[INFO] Running org.example.ProxyTest
add user Tom
check user Tom
add product 1
check product 1
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.041 s - in org.example.ProxyTest
```

可以看出服务的实现类被正确地调用了。

现在假定我们需要实现如下功能

* 拦截所有名为 `add` 的方法调用，在调用前打印 `before` 字样
* 如果参数类型为 `String`，将其首字母转换为小写
* 如果参数类型为 `long`，将其加一

以下将展示如何用不同的代理方法实现上述功能

## 静态代理

静态代理是最简单的代理方式，大部分情况下在面向接口编程时，我们只需要实现一个被代理的接口并且使用组合的方式将调用转发给被代理对象即可。没有实现接口的情况下，也可以通过继承的方式实现代理。

```java
// UserServiceProxy.java

public class UserServiceProxy implements UserService {
    private final UserService service;

    public UserServiceProxy(UserService service) {
        this.service = service;
    }

    @Override
    public boolean add(String username) {
        System.out.println("before");
        username = lowerFirstChar(username);
        return service.add(username);
    }

    @Override
    public boolean exist(String username) {
        return service.exist(username);
    }
}


// ProductServiceProxy.java

public class ProductServiceProxy implements ProductService {
    private final ProductService service;

    public ProductServiceProxy(ProductService service) {
        this.service = service;
    }

    @Override
    public boolean add(long id) {
        System.out.println("before");
        id = id + 1;
        return service.add(id);
    }

    @Override
    public boolean exist(long id) {
        return service.exist(id);
    }
}
```

同样编写单元测试

```java
@Test
public void staticTest(){
    Client client = new Client(
            new ProductServiceProxy(new ProductServiceImpl()),
            new UserServiceProxy(new UserServiceImpl())
    );
    client.action();
}
```

并运行

```console
$ mvn test -Dtest="ProxyTest#staticTest"
[INFO] Running org.example.ProxyTest
before
add user tom
check user Tom
before
add product 2
check product 1
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.041 s - in org.example.ProxyTest
```

可以看出成功实现了需要的功能。但是静态代理有不少弊端

* 代码冗余：打印 `before` 的代码出现了两次
* 需要为每一个被代理的对象都创建一个代理类

动态代理正是为了解决这样的弊端而出现的

## JDK 动态代理

JDK 自身提供了一套动态代理的实现机制，其中核心类为 `java.lang.reflect.InvocationHandler` 与 `java.lang.reflect.Proxy`

增强代理方法时，用户需要自己实现一个 `InvocationHandler` 类，通常会使用组合的方式将被代理的对象注入进去

```java
public class MyHandler implements InvocationHandler {
    private final Object target;

    public MyHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (method.getName().equals("add") && args.length == 1){
            System.out.println("before");
            Object arg = args[0];
            if (arg instanceof String username){
                username = lowerFirstChar(username);
                return method.invoke(target, username);
            } else if (arg instanceof Long id) {
                id = id + 1;
                return method.invoke(target, id);
            } else{
                return method.invoke(target, args);
            }
        } else {
            return method.invoke(target, args);
        }
    }
}
```

获取对象则是通过 `Proxy.newProxyInstance` 方法

```java
public static <T> T getProxy(Class<T> clazz, InvocationHandler handler){
    Object proxy = Proxy.newProxyInstance(clazz.getClassLoader(), new Class<?>[]{clazz}, handler);
    return clazz.cast(proxy);
}
```

这里只考虑了被代理类实现一个接口的情况，将该接口作为参数传递进来并生成代理对象，然后将结果转换为被代理的接口类型。

编写单元测试

```java
@Test
public void jdkTest(){
    Client client = new Client(
            JDKProxy.getProxy(ProductService.class, new MyHandler(new ProductServiceImpl())),
            JDKProxy.getProxy(UserService.class, new MyHandler(new UserServiceImpl()))
    );
    client.action();
}
```

并运行

```console
$ mvn test -Dtest="ProxyTest#jdkTest"
[INFO] Running org.example.ProxyTest
before
add user tom
check user Tom
before
add product 2
check product 1
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.04 s - in org.example.ProxyTest
```

可以看出同样实现了增强功能

JDK 动态代理的问题在于要求被代理的对象必须实现某个接口，这样才能根据接口生成代理类。对于没有实现接口的被代理对象，则可以使用 CGLIB 实现动态代理

## CGLIB

CGLIB 的使用方式与 JDK 动态代理大同小异。CGLIB 不需要被代理类实现接口，通过继承的方式实现代码的增强。

同样需要实现一个 `MethodInterceptor` 类来增强代码

```java
public class MyInterceptor implements MethodInterceptor {
    private final Object target;

    public MyInterceptor(Object target) {
        this.target = target;
    }

    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        if (method.getName().equals("add") && objects.length == 1){
            System.out.println("before");
            Object arg = objects[0];
            if (arg instanceof String username){
                username = lowerFirstChar(username);
                return method.invoke(target, username);
            } else if (arg instanceof Long id) {
                id = id + 1;
                return method.invoke(target, id);
            } else{
                return method.invoke(target, objects);
            }
        } else {
            return method.invoke(target, objects);
        }
    }
}
```

并且通过 `Enhencer` 获取增强后的代理对象

```java
public static <T> T getProxy(Class<T> clazz, MethodInterceptor interceptor){
    Enhancer eh = new Enhancer();
    eh.setSuperclass(clazz);
    eh.setCallback(interceptor);
    return clazz.cast(eh.create());
}
```

然而 CGLIB 由于使用了不安全的代码，与 JDK 17+ 存在兼容问题，在此便不再演示测试结果。