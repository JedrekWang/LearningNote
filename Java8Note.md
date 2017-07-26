# Java 8 特性小结 PART I

## 1. Java接口可实现default method
>
    >下列代码的输出结果为： hehe
``````
public class InterfaceTest {
    public static void main(String[] args) {
        A a = new A() {};
        a.print();
    }
}

interface A {
    default void print() {
        System.out.println("hehe");
    }
}
``````
>
  新版本的接口在原来抽象方法的基础上新增了方法的默认实现，在生成对象时把该接口实现为
匿名对象。即 new InerfaceName(){ };大括号中是对接口的其他非default(abstract)方法
的具体实现。

## 2. Lambda表达式
>
  Lambda表达式的基础语法为:
(parameters) -> expression 或 (parameters) -> {statements;}
>
  Lambda表达式是通过与相关的函数式接口相一致，(函数式接口是一种只有一个抽象方法的接口，
可实现若干的default方法.Java提供了一个注解@FunctionInterface来表明该接口为一个
函数式接口,若不是则出现编译错误),相一致表示一个Lambda表达式与该接口唯一的一个抽象方法匹配。
>
  理论上不用必须加上@FunctionInterface注解，只要保证有一个抽象方法即可，但是加上注解会有
错误提醒，所有推荐使用。
>
`````
public class LambdaTest {
    public static void main(String[] args) {
         Sum sum = (a,b)->a+b;
         int answer = sum.add(1,2);
        System.out.println(answer);
    }
}
@FunctionalInterface
interface Sum{
    int add(int a, int b);
`````
    > 上述代码中注意``Sum sum = (a,b)->a+b;``接口的抽象方法与这个Lambda表达式对应，并且将
    方法实现为两个int型参数相加

## 3. 方法(Method)和构造器(Constructor)引用
 Java 8 允许通过符号`::`来为方法和构造器添加引用
 >
 首先测试一下方法的引用：
 >
 `````
 public class MethodTest {
    public static void main(String[] args) {
        Coder coder = new Coder();
        Power power = coder::exchange;
        String answer = power.insert("HelloWorld");
        System.out.println(answer);
    }
}

class Coder {
    public String exchange(String s) {
        return s+".java";
    }
}
@FunctionalInterface
interface Power {
    String insert(String s);
}
 `````
 >
 上列代码着重看`Power power = coder::exchange;`我们为coder对象的exchange方法添加了一个Power引用
 当我们调用power对象引用的insert方法时，会自动执行exchange的具体实现，上述代码的运行结果为：`HelloWorld.java`
 >
 然后我们看下`::`是如何作用如构造器的：
 >
 `````
 public class ConstructorTest {
    public static void main(String[] args) {
        PeopleFactory<People> factory = People::new;
        People people = factory.create("Jedrek","Wang");
        System.out.println(people.toString());
    }
}

class People {
    String firstName;
    String lastName;

    People() {}

    People(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return "People{" +
                "firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                '}';
    }
}
interface PeopleFactory<P extends People> {
    P create(String firstName, String lastName);
}
 `````
 >
 上述代码中`PeopleFactory<People> factory = People::new;`为People类的构造器(new)添加了一个PeopleFactory<People>引用,
 使得使用引用的create抽象方法会自动调用People的构造函数来生成对象，值得注意的是如果有多个构造器，Java编译器会自动选择最适合的
 构造器
 >
## 4. Lambda表达式的作用范围
    Lambda表达式与匿名类类似，即使用外围类范围的变量时需为final型的变量，但Lambda表达式只需要变量隐含为final即可(即可不声明为
final，但你不能改变值),如果后面更改值，编译器不会执行该代码
````
public class ScopeTest {
    public static void main(String[] args) {
        int num = 1;
        Sum sum = (a,b)->a+b;
        int answer = sum.add(1,num);
        num = 2;  //这行代码不会编译
        System.out.println(answer);
    }
}
````
>
如上所示，虽然讲num值设为2，但输出的值却任然为1+1而不是2+1

>
by Jedrek
