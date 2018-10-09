#### Java类的编译过程
	编译过程即是将java源码转成class二进制字节码的过程
	编译过程主要有三步：
		1. 词法分析和输入到符号表
		2. 注解处理
		3. 语义分析和生成字节码
	详细过程如下：
		源码.java ——> 词法分析器 ——> tokens 流 ——> 语法分析器 ——> 语法树/抽象语法树
		——> 语义分析器 ——> 注解抽象语法树 ——> 字节码生成器 ——> 字节码.class

#### JVM 类生命周期
	加载、验证、准备、解析、初始化
	图示

![](https://github.com/luoyejingxue/interview-note/blob/master/java/class-life-cycle.jpg)

	##### 类的加载机制
		类的加载器分四大类：
![](https://github.com/luoyejingxue/interview-note/blob/master/java/classLoader.png)

|类型|语言|默认加载路径|配置加载路径|
|:-----:| :----: | :-----:|:-----:|
|根加载器 Bootstrap Class Loader|C++|JAVA_HOME/JRE/lib/rt.jar|-Xbootclasspath 指定目录|
|扩展加载器 Extension Class Loader|JAVA|JAVA_HOME/JRE/lib/ext/*.jar|-Djava.ext.dirs 指定目录|
|系统应用加载器 App Class Loader|JAVA|系统classpath|-Djava.class.path 指定目录|
|用户自定义加载器 Customer Class Loader|JAVA|自定义目录|自定义目录|	
		
		Java程序在执行前都要检查类是否已经被加载

		检查过程：
			检查类是否已经被加载，从底层往上层检查各个加载器已经加载的类，顺序是
			App Class Loader ——> Extension Class Loader ——> Bootstrap Class Loader,
			一旦发现该类已经被加载，就马上使用该类。如果没有找到，就加载类。
		类加载过程：
			加载类的过程与检查过程顺序想法，从上层往下层的顺序加载。
			从加载器检查自己的加载路径，找到要加载的类，一旦找到该类就进行加载。
			注意：对于每个加载器，最多只能加载一次系统绝对路径下的同一个类。
				对于类而言，可以被不同加载器重复加载，只要你把类放到类加载器的加载路径下

		ClassLoader：双亲委派模型
			如果一个类加载器收到了类加载的请求时，它首先不会自己去加载这个类，而是把请求委托给父加载器去完成加载，
			依次向上，这也就是类加载的顺序，从上到下，只有父加载器无法完成加载，子加载器才去加载此类。

		优势：Java中Object类，是在rt.jar中的，每个类加载器都要加载它，如果不使用双亲委派模型,由各个类加载器自己去加载，那么系统中就会
			存在很不同的Object类,这样避免了重复加载.
			 另外从安全因素考虑，比如系统的Integer类，已经由父加载器加载了，就不会在继续加载，防止了核心API被篡改。

	##### 类的连接过程
		连接过程分三个部分：
			验证：验证类符合JAVA规范和JVM规范,和编译阶段的语法语义分析不同,
				验证阶段主要的工作是文件验证、元数据验证、字节码验证、符号引用验证
			准备：为类的静态变量分配内存，初始化为系统的初始值。（不初始化静态代码块）
				对于final的静态变量,即常量，如public static final int value=123,准备阶段直接赋值为123
				对于非final的静态变量，如public static int value=123,在准备阶段赋值为0，而不是123，而把123赋值给value是在初始化阶段才进行。
			解析：将符号引用转为直接引用(对象和实例的地址指针、实例变量和方法偏移)
				符号引用：以一组符号来描述所引用的目标，与虚拟机内存布局无关，引用的目标不一定已经被加载到虚拟机内存中
				直接引用：可以直接指向目标的指针、相对偏移量。直接引用与虚拟机内存有关，而且引用的目标对象必须已经被加载到虚拟机内存中
				解析动作主要针对类或者接口、字段、类方法、接口方法四类符号引用进行解析

	##### 类的初始化过程
		初始化类的静态变量和静态代码块为用户自定义的值。
		非静态类在实例化类，在java堆中创建对象的时候，才会进行初始化操作。
		初始化的顺序和java源码从上到下的顺序一致。
		
|一定发生类的初始化（主动调用）|不会发生类的初始化（被动调用）|
|:-----:| :----: |
|main方法所在的类||
|new一个类的对象|数组定义类的引用，不会触发初始化|
|调用类的静态成员和静态方法|引用常量不会触发，已加入方法区的常量池|
|修改类的静态成员的值|访问静态域，只有定义该域的类才被初始化|
|初始化子类时，父类没有被初始化，则先初始化父类|通过子类调用父类的静态变量，子类不会初始化，父类会初始化|
|使用java.lang.reflect的包对类进行反射调用||
```java
//主动调用和被动调用的区别
	class NewParent {

	    static int hoursOfSleep = (int) (Math.random() * 3.0);

	    static {
	        System.out.println("NewParent was initialized.");
	    }
	}

	class NewbornBaby extends NewParent {

	    static int hoursOfCrying = 6 + (int) (Math.random() * 2.0);

	    static {
	        System.out.println("NewbornBaby was initialized.");
	    }
	}

	public class ActiveUsageDemo {

	    // Invoking main() is an active use of ActiveUsageDemo
	    public static void main(String[] args) {

	        // Using hoursOfSleep is an active use of NewParent,
	        // but a passive use of NewbornBaby
	        System.out.println(NewbornBaby.hoursOfSleep);
	    }

	    static {
	        System.out.println("ActiveUsageDemo was initialized.");
	    }
	}

	//输出结果为
	//ActiveUsageDemo was initialized.
	//NewParent was initialized.
	//1

	//解析
	//没有输出NewBornBaby was initialized,是因为没有主动去调用NewbornBaby,如果改为NewBornBaby.hoursOfCrying,
	//那么这个时候就会去调用NewBornBaby
```
	
	<clinit>
		类构造器是在编译之后会在字节码文件中生成<clinit>方法，类构造器会将静态语句块、静态变量（非final）初始化，收敛到<clinit>方法中，收敛的顺序按照源码中的顺序。
		
		静态初始化块中只能访问到定义在它之前的类变量，定义在它之后的类变量，在前面的静态初始化中可以赋值，但不能直接访问。（用类名可以.变量名可以）

		类构造器不需要显示的调用父类构造器，要保证在调用子类构造器之前，父类构造器方法已经执行完了

		由于父类构造器方法先于子类构造器执行，所以父类中定义的静态初始化块要优先于子类先赋值

		类构造器方法对于类和接口并不是必须的，如果一个类中没有静态初始化块、静态变量赋值操作，则编译器不会为该类生成类构造器方法

		接口不能使用静态初始化块，但可以有类变量赋值操作，因此接口和类都一样可以生成<clinit>，在接口中的变量是static final，在准备中就进行了赋值
			接口与类的不同是：
				接口执行<clinit>不需要执行父接口的<clinit>，只有当父接口中定义静态变量被使用，父接口才会被初始化

				其次，接口的实现类在初始化时同样不会执行接口的<clinit>

		jvm会保证<clinit>在多线程环境中被正确的加锁和同步，当多个线程去初始化一个类，只会有一个线程去执行这个类的<clinit>

	<init>
		实例构造器，会将语句块、变量初始化、调用父类构造器等操作放到<init>方法中

		只有在执行<clinit>后才会去执行<init>,按照先父类、后子类，先成员变量、后实例构造方法的顺序执行。

		##### 属性在不同时期的赋值示例
```java
	class Singleton {

	    private static Singleton mInstance = new Singleton();// 位置1
	    public static int counter1;
	    public static int counter2 = 0;

	//    private static Singleton mInstance = new Singleton();// 位置2

	    private Singleton() {
	        counter1++;
	        counter2++;
	    }

	    public static Singleton getInstantce() {
	        return mInstance;
	    }
	}

	public class InitDemo {

	    public static void main(String[] args) {

	        Singleton singleton = Singleton.getInstantce();
	        System.out.println("counter1: " + singleton.counter1);
	        System.out.println("counter2: " + singleton.counter2);
	    }
	}
	```
	当位置1时，打印
	```
	counter1:1
	counter2:0
	```
	当位置2时，打印
	```
	counter1: 1
	counter2: 1
	```
	三个属性在准备阶段都会根据类型赋予默认值，在初始化阶段会根据显示的表达式再次进行赋值，顺序从上到下执行

	##### 另外一道关于初始化的面试题
	```java
		public class Test {
			public static int k = 0;
			public static Test t1 = new Test("t1");
			public static Test t2 = new Test("t2");
			public static int i = print("i");
			public static int n = 99;

			private int a = 0;
			public int j = print("j");

			{
				print("构造块");
			}

			static {
				print("静态块");
			}

			public Test(String str){
				System.out.println((++k) + ":" + str + " i="+i+" n="+n);
				++i;
				++n;
			}

			public static int print(String str){
				System.out.println((++k) + ":" + str + " i="+i+" n="+n);
				++n;
				return ++i;
			}

			public static void main(String args[]){
				Test t = new Test("init");
			}
		}
	```
	运行结果
	```
		1:j   i=0    n=0
		2:构造块   i=1    n=1
		3:t1   i=2    n=2
		4:j   i=3    n=3
		5:构造块   i=4    n=4
		6:t2   i=5    n=5
		7:i   i=6    n=6
		8:静态块   i=7    n=99
		9:j   i=8    n=100
		10:构造块   i=9    n=101
		11:init   i=10    n=102
	```

	解析：
		1. 由于Test是JVM启动类，属于主动调用，所以会依次进行loading、linking、initialization三个过程
		2. 经过loading与linking阶段，所有的属性都有了默认值，然后进入最后的initialization阶段
		3. 在initialization阶段，先对static属性赋值，然后在非static。k 第一个显示赋值为0
		4. 接下来t1属性，此时Test在initialization阶段，static变量无需再次初始化，所以忽略static属性的赋值，只对非static属性进行赋值
			就需要执行 a=0; 再是 j = print("j") ，这个时候 就会有1：j i=0 n=0
			接下来执行代码块{print("构造块")}，打印出来是 2: 构造块 i=1 n=1
			在执行构造方法 打印出来 为  3: t1 i=2 n=2
		5. 接着对t2进行赋值，过程同t1,会打印如下的
			4:j i=3 n=3
			5:构造块 i=4 n=4
			6:t2 i=5 n=5
		6. 之后在执行 i = print("i")
			会打印出 7:i i=6 n=6
			在对n 进行赋值为99
		7. 到目前位置static成员赋值已经完成，接下来就到了static代码块 {print("静态块")}
			会打印 8：静态块 i=7 n=99
		8. 所有static属性赋值完成,接下来是非static的赋值
			执行a=0 , j = print("j"),并打印
			9：j i=8 n=100
		9. 在是非静态代码块 {print("构造块")}
			10：构造块 i=9 n=101
		10. 构造函数 11：init i=10 n=102




