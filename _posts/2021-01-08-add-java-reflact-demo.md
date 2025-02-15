---
layout: post
title: java反射机制
categories: Java
description: java反射的基本概念及代码层面的实现及使用。
keywords: java, 反射机制
topmost: true
---

java反射机制是Java中比较重要的概念，广泛应用于各种框架中，简单的应用有java使用jdbc连接数据库时，使用反射机制加载jdbc驱动。
因此，本文决定从基础到简单应用对Java反射机制做一个简单的总结。

## 1.何为反射?
#### 1.1反射的概念
现实生活中有很多反射的例子，比如医院里面的X射线，西游记里面的照妖镜等，都是对反射的一个很好的解释。因此，可以看出反射其实具有透视的作用，可以透视一个类，透视一个对象。
有一个很形象的比喻：虽然你可以在Java中由很多对象（虽然现实生活中是一个单身狗，哈哈），但这些对象在反射面前就是裸奔的，反射可以打破Java语言的封装特性。
#####	1.1.1反射几个例子
再来几个贴近生活的反射的例子：
（1）Eclipse/IDEA的代码自动提示功能，如自动提示一个对象、方法、属性等；
（2）在配置文件中配置servlet，但实际并没有使用new servlet去创建一个对象，那servlet是如何创建的呢？
下面看下servlet的创建过程：在配置文件中配置servlet的类的全路径，比如"com.wshy.testdemo.servlet"，tomcat可以根据类的完整名称创建对象。


#####	1.1.2反射例子的思考
`上面的第二个示例中用到了一个类的全路径去创建对象，说白了，类的全路径就是一个字符串，也就是通过字符串去创建对象`，这句话非常重要，因为这反映出java的反射机制可以不需要使用new一个对象，可以直接使用字符串（即类的全路径名）去创建出一个对象。看到这里是不是感觉new对象并不是找对象的唯一方式，不要在一棵树上吊死，要尝试更多的方法去`“找对象”`。

#####	1.1.3反射的官方定义
扯了这么多，官方对反射就总结一句话：`在程序运行的过程中，可以动态的创建对象，并获取对象的基本信息，包括属性、方法等；`

## 2.反射基础及原理
#### 2.1反射的基石
##### 2.1.1字节码文件
这里想一下java程序运行的过程：
首先编写一个helloworld程序（从入门到放弃的必要程序），想要在任意平台上运行得到helloword的代码，就需要用到JVM的知识，这里不做过多介绍，只需要知道JVM只能识别以.class结尾的字节码文件，也叫机器码文件。所以便有了这样经典的流程：
`helloWorld.java  --->helloWorld.class --->jvm内存`，这里的helloWorld.class就是字节码文件。
这里引用下网上的资源：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708224016192.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
##### 2.1.2字节码文件对象
知道了字节码的概念，那就要引出字节码文件对象的概念了。
大家都知道，在java中，有类和对象两个概念，万事万物皆对象，桌子、黄瓜、左右手，都是你的`对象`（这里有点污，请萌新自动绕行）。所以，类也是一个对象，字节码文件也是一个对象。
所以，当.class文件被加载到JVM内存中之后，JVM就认为字节码文件是一个字节码文件对象，美名曰：`“类对象”`或`“class对象”`，下面统称为**类对象**。
##### 2.1.3如何获取字节码文件的对象
字节码文件也是一个类，既然是类，那就能通过类去获取对象，官方提供三种方式去获取类对象的对象。
（1）通过Object的`getClass()`方法获取
（2）通过`类型.class`获取
（3）通过`class.forName("类的全路径名")`获取（这种方式使用的最多）
扯了这多，就是为了引出反射的概念，下面，正式开始玩反射！
## 3.正式玩反射
下面以代码的形式进行反射的介绍
##### 3.1不使用反射创建对象
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708230258967.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
`思考一个问题：在无参构造函数中抛出一个异常，对象还能创建成功吗？`
看代码得答案，此时会因为无参构造函数抛出异常，而使对象创建失败！
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708230628820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
##### 3.2使用反射创建对象
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708231338216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
##### 3.2获取类方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708232608260.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
##### 3.4获取类属性
这里有个坑，需要注意下，先看代码
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708233020693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
从例子中可以看出，使用①的方式，并没有获取到类属性，这是什么原因呢？
查阅资料后可以知道，`getFields()方法只返回public修饰的变量，不返回private修饰的变量，而示例中，userName使用private修饰。`
而`getDeclaredFields()可以返回public、private、protected修饰的变量`。
##### 3.5反射在单例模式中的思考
`思考一个问题：在常用的设计模式的单例模式中，如何避免单例被反射攻击？`
方法一:设置构造方法私有化，但并不能彻底解决，反射仍有可能创建对象。
方法二：在构造方法中添加异常，这个方法比较彻底，试想本类都无法创建对象（上文中有提到，无法创建对象的情形），因此反射更无法创建对象了。。。。
##### 3.6反射破坏单例模式
针对3.5中的方法一设置构造方法私有化，并不能彻底解决单例模式被反射攻击的问题，这里做下解释，直接看代码。
**第一步**，将构造函数私有化
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020070912193014.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
**第二步**，使用反射中的`getDeclaredConstructor ()`方法读取构造函数，不过需要加上`setAccessible (true)`才可以使单例模式失效，进而创建多个对象，不构成单例模式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200709122212900.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDU1MDExOA==,size_16,color_FFFFFF,t_70)
看完上边有关反射的东西， 对常用框架里的配置文件是不是有点思路了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200709122839209.png)
上图是Spring配置文件里的常见的bean配置，这看起来是不是可以用反射很轻易的就可以实现：解析xml然后把xml里的内容作为参数，利用反射创建对象。

除了这个，常用的框架里还有很多地方都用到了反射，`反射是框架的灵魂，具备反射知识和思想，是看懂框架的基础`。

平常用到的框架，除了配置文件的形式，现在很多都使用了注解的形式，其实注解也和反射息息相关，使用反射也能轻而易举的拿到类、字段、方法上的注解，然后编写注解解析器对这些注解进行解析，做一些相关的处理，所以说不管是配置文件还是注解的形式，它们都和反射有关。

## 4.本文示例代码

```java
package com.wshy.testdemo;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

/**
 * @author wshy
 * @data 2020/7/8
 **/
public class userEntity {

    private String userName;

    //私有化构造方法
    private userEntity () {
        System.out.println ("对象初始化，这是无参构造方法！");
        //throw new RuntimeException ();
    }

    public String getUserName () {
        return userName;
    }

    public void setUserName (String userName) {
        this.userName = userName;
    }

    public static void main (String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
        //1.使用new创建对象，默认调用无参构造函数
        userEntity userentity = new userEntity ();
        userentity.setUserName ("这是使用new创建对象");
        System.out.println (userentity.getUserName ());

        System.out.println ("-------------------------------------");

        //2.使用反射机制创建对象
        Class clazz = Class.forName ("com.wshy.testdemo.userEntity");
        userEntity userentity2 = (userEntity) clazz.newInstance ();
        userentity2.setUserName ("这是使用反射创建对象");
        System.out.println (userentity2.getUserName ());

        System.out.println ("-------------------------------------");

        //2.1使用反射API
        //获取类方法
        Method[] methods = clazz.getMethods ();
        for(Method method : methods) {
            System.out.println ("类的方法：" + method.getName ());
        }
        //获取类属性
        Field[] fields = clazz.getFields ();
        for(Field field : fields) {
            System.out.println ("getFields获取类的属性：" + field.getName ());
        }
        //获取类属性
        Field[] fields2 = clazz.getDeclaredFields ();
        for(Field field : fields2) {
            System.out.println ("getDeclaredFields获取类的属性：" + field.getName ());
        }

        System.out.println ("-------------------------------------");
        //反射攻击单例模式的私有构造方法
        Constructor c = clazz.getDeclaredConstructor ();
        c.setAccessible (true);
        userEntity userEntity3 = (userEntity) c.newInstance ();
        userEntity3.setUserName ("反射攻击单例模式，即使你的构造方法为私有化！");
        System.out.println ("反射攻击单例模式：" + userEntity3.getUserName ());

    }
}
```
通过这篇文章，简单总结了反射的基本原理和应用场景，通过这些总结，可以看出我们并不是每次都要new一个`“对象”`，你也可以使用反射去找`“对象”`，这对单身狗而言是一种福利呀！
