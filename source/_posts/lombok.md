---
title: lombok简介以及使用
date: 2018-3-1 16:36:30
author: sssnto
tags: lombok
category: 后端框架
---

## lombok简介



>Lombok是一个可以通过简单的注解形式来帮助我们简化消除一些必须有但显得很臃肿的Java代码的工具，通过使用对应的注解，可以在编译源码的时候生成对应的方法。

官方地址：https://projectlombok.org 
github地址：https://github.com/rzwitserloot/lombok。
 
那么lombok到底是个什么呢，lombok是一个可以通过简单的注解的形式来帮助我们简化消除一些必须有但显得很臃肿的 Java 代码的工具，简单来说，比如我们新建了一个类，然后在其中写了几个字段，然后通常情况下我们需要手动去建立getter和setter方法啊，构造函数啊之类的，lombok的作用就是为了省去我们手动创建这些代码的麻烦，它能够在我们编译源码的时候自动帮我们生成这些方法。

lombok能够达到的效果就是在源码中不需要写一些通用的方法，但是在编译生成的字节码文件中会帮我们生成这些方法，这就是lombok的神奇作用。

虽然有人可能会说IDE里面都自带自动生成这些方法的功能，但是使用lombok会使你的代码看起来更加简洁，写起来也更加方便。

## lombok安装
lombok的安装跟一般引用jar包没有什么区别，可以到官网上下载最新的jar包，然后导入到项目里面就好啦。

Maven添加依赖

```
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.16.10</version>
    </dependency>
</dependencies>
```
Intellij idea开发的话需要安装Lombok plugin，同时设置 Setting -> Compiler -> Annotation Processors -> Enable annotation processing勾选。

## lombok使用
lombok使用过程中主要是靠注解起作用的，官网上的文档里面有所有的注解，这里不一一罗列，只说明其中几个比较常用的。

### @NonNull

@NonNull: 可以帮助我们避免空指针。
您可以在方法或构造函数的参数上使用@NonNull来让lombok为您生成一个空检查语句。
@NonNull 会先检查被注解的参数是否为空，如果为空立即抛出空指针异常。该方法会在进入方法的最顶端执行。

* with lombok
```
import lombok.NonNull;

public class NonNullExample extends Something {
  private String name;
  
  public NonNullExample(@NonNull Person person) {
    super("Hello");
    this.name = person.getName();
  }
}

```
* Vanilla Java
```
public class NonNullExample extends Something {
  private String name;
  
  public NonNullExample(Person person) {
    super("Hello");
    if (person == null) {
      throw new NullPointerException("person");
    }
    this.name = person.getName();
  }
}

```


----------

### @Getter and @Setter

Getter/@Setter可以帮助我们自动来生成默认的getter/setter。我们可以使用它们来注释任何字段。
一个默认的getter只是简单地返回这个字段，如果这个字段被称为foo（或者如果字段的类型是boolean，那么它是isFoo）就被命名为getFoo。 如果该字段名为foo，则返回void，并将与该字段具有相同类型的1个参数的情况设置为默认setter。 它只是将该字段设置为该值。

>除非明确指定AccessLevel，否则生成的getter / setter方法将为public，如下例所示。访问级别为PUBLIC, PROTECTED, PACKAGE, and PRIVATE。
通过使用特殊的AccessLevel.NONE访问级别，您可以始终手动禁用任何字段的getter / setter生成

@Getter and @Setter可以在字段上使用，也可以在类上使用。

* With Lombok
```
import lombok.AccessLevel;
import lombok.Getter;
import lombok.Setter;

public class GetterSetterExample {
  /**
   * Age of the person. Water is wet.
   * 
   * @param age New value for this person's age. Sky is blue.
   * @return The current value of this person's age. Circles are round.
   */
  @Getter @Setter private int age = 10;
  
  /**
   * Name of the person.
   * -- SETTER --
   * Changes the name of this person.
   * 
   * @param name The new value.
   */
  @Setter(AccessLevel.PROTECTED) private String name;
  
  @Override public String toString() {
    return String.format("%s (age: %d)", name, age);
  }
}

```
* Vanilla Java
```
public class GetterSetterExample {
  /**
   * Age of the person. Water is wet.
   */
  private int age = 10;

  /**
   * Name of the person.
   */
  private String name;
  
  @Override public String toString() {
    return String.format("%s (age: %d)", name, age);
  }
  
  /**
   * Age of the person. Water is wet.
   *
   * @return The current value of this person's age. Circles are round.
   */
  public int getAge() {
    return age;
  }
  
  /**
   * Age of the person. Water is wet.
   *
   * @param age New value for this person's age. Sky is blue.
   */
  public void setAge(int age) {
    this.age = age;
  }
  
  /**
   * Changes the name of this person.
   *
   * @param name The new value.
   */
  protected void setName(String name) {
    this.name = name;
  }
}

```


----------

未完待续。。。。。

