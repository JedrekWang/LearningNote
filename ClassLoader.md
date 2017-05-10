# ClassLoader小结
Java的ClassLoader负责将Java类的字节码(.class文件)加载到Java虚拟机(JVM)中
>
ClassLoader的概念被广泛应用到Java框架中(ex:Spring),下面我来自己总结一下ClassLoader
>
首先Java虚拟机用到一个对象的过程如下：首先程序员编写了一个Java类(.java文件)，经过Java编译器
转换为字节码(.class文件)，然后类加载器加载该字节码并生成一个java.lang.Class对象，通过Class的
各种方法(ex:直接newInstance();)来生成一个对象。
>
tips：
这里提一下Java虚拟机如何确定两个类是否相等，除了比较类的名字外还会比较类的类加载器是否相等。
换言之，如果两个类的名字相同但类加载器不同，则虚拟机会把它们当成两个类
>
Java的类加载器有两类，一种是系统自带的ClssLoader,一种是程序员自己编写的ClassLoader
>
* 对于系统自带的ClassLoader有三种：
    > bootStrap ClassLoader(引导类加载器) -> 加载Java的核心库
    >> extensions ClassLoader(扩展类加载器) -> 加载Java的扩展库
    >>>system ClassLoader(系统类加载器)   -> 根据CLASSPATH来加载类
>
通过 ClassLoader的getParent()方法我们可以得到他们的父子关系为：bootStrap ClassLoader是
extensions ClassLoader的父加载器，extensions ClassLoader是 system ClassLoader的父加载器，extensions
`````
public class ClassLoaderTest {
    public static void main(String[] args) {
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        while(classLoader != null) {
            System.out.println(classLoader);
            classLoader = classLoader.getParent();
        }
    }
}
`````
上述代码证实了这点，需要指出的是bootstrap ClassLoader由于jdk的原因会返回null
>
运行结果为：```sun.misc.Launcher$AppClassLoader@42a57993
sun.misc.Launcher$ExtClassLoader@28d93b30```
>
Java使用了一种特殊的机制来加载类：双亲委托机制.即：当一个类请求给一个类加载器时，这个类加载器不会立即加载，而是
委托给父加载器依次类推，只有当父类无法加载时，才会由自己加载。
>
这种机制的优点:
1. 避免重复加载，当父类加载器可以加载时，子类加载器不会加载
2. 安全，如果用户自己定义了一个Object类，会先加载Java核心包的Object
>
由于这种机制，使得开启类加载的可能与真正加载类的类加载器不一致，前者称为初始加载器，后者称为定义加载器，而Java比较对象
是使用定义加载器，所有定义加载器才是真正用于Java类的认证的
>
第二种类加载器是用户自己定义的类加载器，用户可以继承ClassLoader类，然后自己实现类的findClass()方法
>
PS: 对于Web容器来说，比如Tomcat的类加载机制与上面所讲的刚好相反，即先自己尝试加载类，然后再交给父加载器。这样做的目的是
Web 应用自己的类的优先级高于 Web 容器提供的类，当然Java核心库的类除外，原因上面已经陈述。

by Jedrek
