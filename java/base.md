1. #### Java的三大特性

	封装、继承、多态

	#####	封装：
		把一个对象的属性私有化，同时提供外界可访问属性的方法。

	#####	继承：
		在已有类的基础上，定义一个新类，有用父类非private的属性和方法，
		并增加新的属性和新的方法。对已有的类的扩展和复用

	#####	多态：
		程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，只有在程序运行期间才确定，即一个
		引用变量到底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，只有在程序运行期间才能确定。

		实现多态的两种形式：继承（多个子类对同一方法进行重写）和接口（实现同一接口并对同一方法进行实现）

2. #### 方法签名
	方法签名由方法名和参数列表（方法的参数和类型）组成
	方法签名不包括方法的返回类型、返回值、访问修饰符

3. #### Override和Overload (重写与重载)
	
	##### Override (重写)
		发生在子类、接口实现类中（重写的是父类或者接口中的方法）
		子类不能重写private方法、不能重写final方法
		重写的方法签名要不变
		方法名、参数列表必须相同
		返回值的范围小于等于父类
		抛出的异常小于等于父类
		访问修饰符范围大于等于父类

	##### Overload (重载)
		发生在同一个类中
		方法名必须相同
		重载的方法签名必然不同
		参数类型、个数、顺序不同
		方法返回值和访问修饰符可以不同
		发生在编译时

4. #### 接口和抽象类
	
	##### 接口
		接口的方法默认是public，在JDK1.8以前（不包括1.8）接口方法不能有实现
		接口中的实例变量默认是final类型，属于该类的
		一个类可以实现多个接口
		一个类实现接口，要实现接口中非default的所有方法（JDK1.8以后有default）
		接口可以被声明，但不能被new实例化
		接口是对行为的抽象
		接口没有构造器
		接口不能有main方法
	
	##### 抽象类
		抽象类的方法修饰符有public、protected、default
		抽象类的实例变量同类一样
		一个类仅能继承一个抽象类
		一个类需要重写抽象类的抽象方法
		不可实例化抽象类，其他与类一样
		抽象类有构造器
		抽象类可以有main方法，并运行它

5. #### 成员变量与局部变量
	|描述|成员变量|局部变量|
	|:-----:| :----: | :-----:|
	|作用范围|属于类|属于方法|
	|修饰|可被访问修饰符、static、final修饰|仅能被final修饰|
	|内存存储方式|由于属于类，对象存在堆内存中|属于方法，存在栈内存中|
	|生存周期|随着类的创建、销毁|随着方法的调用|

6. #### 静态方法与实例方法的区别
	外部调用静态方法，可使用类名.方法名，也可以使用对象名.方法名，但实例方法只可使用后者
	静态方法访问本类的成员时，只允许访问静态成员，实例方法无限制

7. #### == 与 equals
	
	##### ==
		作用是判断两个对象的地址是否相等。即判断两个对象是否为同一个对象。
		基本数据类型==比较的是值，引用数据类型==比较的是内存地址
		对于Integer -128——127（jvm的默认设置）比较的也是值，由于缓存，其他比较的是内存地址
	
	##### equals
		判断两个对象是否相等
		如果没有重写equals方法，那么equals 调用的Object里面的equals方法，里面的实现是使用的==，即同==
		如果重写了equals方法，根据重写的内容，来判断是否相等，我们也可以根据对象的内容来做判断
		注意：String str = "hello";  这样是先在常量池中查找对应的字符串，然后把引用指向该地址
	
	##### 代码示例
	```java
		public class EqualsDemo {
			public static void main(String[] args) {
				String a = new String("ab"); // a 为一个引用
				String b = new String("ab"); // b为另一个引用,对象的内容一样
				String aa = "ab"; // 放在常量池中
				String bb = "ab"; // 从常量池中查找
				System.out.println("aa==bb -> "+(aa==bb));// true

				System.out.println("a==b -> "+(a==b));// false，非同一对象
				System.out.println("a EQ b -> " + (a.equals(b)));// true
				System.out.println("42 == 42.0 -> "+(42==42.0));// true

				Integer i0 = 50;
				Integer i1 = 50;
				Integer i2 = new Integer(50);

				Integer i3 = 300;
				Integer i4 = 300;

				System.out.println("i0==i1 -> "+(i0==i1));//自动拆装箱时，会使用Integer.valueof()创建，如果在整数缓存中直接返回值，否则创建对象,返回true
				System.out.println("i3==i4 -> "+(i3==i4));// false，非同一对象
				System.out.println("i1==i2 -> "+(i1==i2));//由于默认的IntegerCache的值是从-128——127,而300不再其中，所以返回的是对象，对象不相等，返回false
			}
		}
	```

8. #### hashCode() 与equals()
	##### hashCode()
		hashCode是获取哈希码，实际上是返回一个int整数。哈希码的作用是确定该对象在哈希表中的索引位置
		在hashSet、hashMap中对象可以根据hashCode的值来判断对象加入的位置。如hashSet中不允许重复数据出现，可以先根据对象的hashCode值索引位置，如果该位置没有对象，那么就直接写入，如果有对象，则根据equals比较对象。
		hashCode默认会对堆上的对象产生独特的值，在com.icehand.**@2,后面的值

	##### 重写equals时hashCode的注意事项
		1.如果两个对象相等，则hashCode一定也相同
		2.如果两个对象相等，则equals返回一定是true
		3.两个对象hashCode相等，并不代表两个对象相等
		4.equals方法重写了，必须要重写hashCode，不然在用到hasCode的里面HashMap、HashSet里面
		  对重复放进去的对象，也区分为不同的对象（虽然调用equals方法返回的是true）

9. #### 自动装箱和自动拆箱
		装箱：把基本类型用它们对应的应用类型包装起来
		拆箱：将包装类型转换为基本数据类型

10. #### JAVA八大基本数据类型
	Java的八大基本类型：int、byte、long、short、float、char、double、boolean
	字符型：char
	布尔型：boolean
	数值类型：byte、short、int、long、float、double
	整数类型：byte、short、int、long
	浮点类型：float、double

	|类型|占位|最大存储|范围|
	|:-----:| :----: | :-----:|:-----:|
	|byte|8bit|255|-128 —— 127|
	|short|16bit|65536|-32768 —— 32767|
	|int|32bit|2的32次方减1|负的2的31次方到正的2的31次方减1|
	|long|64bit|2的64次方减1|负的2的63次方到正的2的63次方减1|
	|float|32bit||2^149 —— 2^128 -1|
	|double|64bit||4.9E-324（2^-1074）—— 2^1024 -1|
	|char|16bit|利用unicode存储|0 - 2^16 - 1|
	|boolean|1bit或31bit,依据jvm标准||

11. #### 值传递与引用传递
	值传递：实际参数把它的值传递给对应的形式参数，函数接收的是原始值的一个copy,此时内存中存在两个相等的基本类型，即实际参数与形式参数
		后面的方法对形式参数进行修改，对原始值都不会造成影响

	引用传递：方法调用时，实际参数的引用地址传递给方法相应的形参，函数接收的是实际参数的内存地址
		在方法执行时，形参和实际参数相同，都指向同一块内存地址，方法执行对引用的操作会影响到实际的参数。

12. #### 程序、进程、线程
	##### 程序
		程序是含有指令和数据的文件，被存储在磁盘或者其他的数据存储设备中。程序是静态的代码
	##### 进程
		进程是程序的一次执行过程，是系统运行程序的基本单位。进程是动态的。
		系统运行程序既是从一个进程的创建到销毁的过程。
		每个进程拥有某些系统资源如CPU时间、内存空间、文件等
	##### 线程
		线程是资源分配的最小单位，是CPU调度的最小单位，是进程执行的基本单位。一个进程可以拥有多个线程
		多个线程可以共享同一资源

13. #### 线程的状态
	1. 新建
		新疆一个线程对象
	2. 可运行
		线程创建成功，进入可运行线程池中，等待线程调度，获取CPU的使用权
	3. 运行中
		线程获取到CPU的使用权，执行对应的代码段
	4. 阻塞
		线程由于某种原因，放弃了CPU的使用权，让出了CPU的时间片，进入阻塞状态
		等待阻塞：运行中的线程执行o.wait()方法，jvm会把线程放入等待队列中,直到被o.notify()/notifyAll()唤醒
		同步阻塞：运行中的线程获取对象的同步锁时，若该同步锁被其他线程占用，则JVM会把该线程放入锁池中
		其他阻塞：运行中的线程执行sleep或者join()或者发起IO请求,JVM会把线程置为阻塞状态
	5. 死亡
		线程执行结束或者因异常退出，线程的生命周期结束，死亡的线程不可复生
	![](https://github.com/luoyejingxue/interview-note/blob/master/java/thread.jpg)

14. #### Java不可变类与可变类
	
	##### 不可变类
		所谓不可变类是指在这个类的实例一旦创建完成后，就不能修改其成员变量的值,JDK内Integer、Long、String
		等都是不可变类,类由fianl修饰
		不可变类是线程安全的，因为不存在更改类的成员变量，所以在线程之间可以互相共享
	
	##### 可变类
		相对于不可变类，可变类创建实例后可以改变其成员变量值
	
	##### 不可变类的设计
		1. 类添加final,保证类不被继承
		2. 保证所有的成员变量必须私有，并加上final修饰
		3. 不提供改变成员变量的方法，包括setter
		4. 通过构造器初始化所有成员，进行深拷贝（deep copy）
		5. 在getter方法中，不要直接返回对象本身，二十克隆对象，并返回对象的拷贝 
	
	##### String对象的不可变性优缺点
		1. 字符串常量池的需要
			字符串常量池可以将一些字符串常量放在常量池中重复使用，避免每次都创建相同的对象、节省存储空间
			如果字符串是可变的，那么相同的String还指向常量池的同一个内存空间，当某个变量改变内存的值时，那么
			其他的对象的值
		2. 线程安全考虑
			同一个字符串实例可以被多个线程共享
		3. 类加载器要用到字符串，不可变性提供了安全，以便正确的类被加载
		4. 支持hash映射和缓存
			因为字符串是不可变的，所以它创建的时候hashCode就被缓存了,不需要重新计算。Map常以String作为key，
			就是因为字符串的处理速度要快过其他的对象
		5. 如果需要改变String对象，就需要创建很多String对象

	##### String对象的不可变性，可以通过反射来破坏

15. #### String、StringBuilder、StringBuffer
	String 为不可变，StringBuilder和StringBuffer都是继承的AbstractStringBuilder，其中成员变量都可以被修改，所以为可变类
	String是线程安全、StringBuffer对操作方法都加了同步，所以也是线程安全，对于StringBuilder不是线程安全的
	String类的改变都需要生成新的String对象，然后将指针指向的新的对象
	StringBuffer每次操作是对象本身的操作，但方法采用同步
	StringBuilder也是操作对象本身，不需要生成新的对象，但线程不安全

	使用总结：
		String : 如果是操作少量的数据
		StringBuilder : 单线程操作大量字符传数据
		StringBuffer : 多线程操作大量字符串数据

16. #### final
	final 可作用的地方：类、变量、方法
	1、 对于一个final类，表示该类不可被继承，类中的所有方法被隐式指定为final
	2、 对于一个final变量，如果是基本数据类型，那么其数值就不在更改，如果是引用类型，那么在初始化后便不能在让其指向另外一个对象
	3、 对于final方法，可以将方法锁定，如果以final修饰，子类不能在更改；第二个原因是在早起版本会将final方法转为内嵌调用，提高效率
		类中的private方法默认是final的

17. #### this
		this用于引用类指向当前实例
		在类中this，可以访问当前的实例的变量和方法
		this调用本类的构造方法时，需要放在首行
		this不可在static方法中使用

18. #### super
		super用于从子类访问父类的变量和方法
		super调用父类中的其他构造方法时，调用要放在构造方法的首行
		super不能在static方法中使用

19. #### static
		1、 修饰成员变量和成员方法
			被static修饰的成员属于类，不单属于这个类的某个对象，被类所共享
		2、 静态代码块
			静态代码块定义在类中的方法外，静态代码块在非静态代码块之前执行
			（执行顺序：静态代码块 -> 非静态代码块 -> 构造方法）
			不管创建多少对象，静态代码块只执行一次
		3、 静态内部类
			静态内部类不需要依赖外围类的创建，非静态内部类有一个引用，指向创建它的外部类
			他不能使用任何外部类的非static成员变量和方法
		4、 静态导包（1.5后的特性）

```java
	public class TestA {
		{
			System.out.println("Demo A");
		}
		
		public TestA(){
			System.out.println("TestA constructor");
		}

		static{
			System.out.println("TestA static");
		}
	}

	public class TestB extends TestA{
		{
			System.out.println("Demo B");
		}

		public TestB(){
			System.out.printnln("TestB constructor");
		}

		static {
			System.out.println("TestA static");
		}
	}

	public class ABTest{
		public static void main(String[] args){
			TestB b = new TestB();
			TestB a = new TestB();
		}
	}

	//执行结果
	//TestA static
	//TestB static
	//Demo A
	//TestA constructor
	//Demo B
	//TestB constructor

	//Demo A
	//TestA constructor
	//Demo B
	//TestB constructor

	//执行顺序 父类静态代码块 ——> 子类静态代码块 ——> 父类非静态代码块 ——> 父类构造方法 ——> 子类非静态代码块 ——> 子类构造方法
	// 静态代码块只执行一次，之后实例化都不在执行，非静态代码块，在每次实例化时都会执行一次
	
```