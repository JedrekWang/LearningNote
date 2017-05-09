# ClassLoader小结
Java的ClassLoader负责将Java类的字节码(.class文件)加载到Java虚拟机(JVM)中
>
ClassLoader的概念被广泛应用到Java框架中(ex:Spring),下面我来自己总结一下ClassLoader
>
首先Java虚拟机用到一个对象的过程如下：首先程序员编写了一个Java类(.java文件)，经过Java编译器
转换为字节码(.class文件)，然后类加载器加载该字节码并生成一个java.lang.Class对象，通过Class的
各种方法(ex:直接newInstance();)来生成一个对象。
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
