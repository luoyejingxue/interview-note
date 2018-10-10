#### JAVA多线程三大核心
	##### 原子性
		Java的原子性跟数据库事务的原子性差不多，一个操作要么全部执行成功或者失败
		JVM只保证基本的原子性，类似i++ 之类的操作，看似原子操作，其实里面有
			1. 获取i的值
			2. 自增
			3. 在赋值给i
		想要i++ 操作保持原子性，就要用到synchronized 或者lock进行加锁处理

		基础类中的AtomicInteger 这样的原子类，本质上是利用CPU级别的CAS指令来完成的

		最多用到的方法: incrementAndGet()

		```java
			public final long incrementAndGet() {
		        for (;;) {
		        	//返回的值是使用volatile关键词修饰的，保证了内存可见性
		            long current = get();
		            long next = current + 1;
		            //判断当前值是否被更新过，如果等于说明没有被更新过然后将当前值更新为next
		            //，否则，进入循环，直到更新成功为止
		            if (compareAndSet(current, next))
		                return next;
		        }
		    }

		    public final boolean compareAndSet(long expect, long update) {
		        return unsafe.compareAndSwapLong(this, valueOffset, expect, update);
		    }
		```

	##### 可见性
		现代计算机中，由于CPU直接从主内存中读取数据的效率不高，所以都会对应的CPU高速缓存，先将主内存中的数据读到
		缓存中，线程修改数据之后首先更新到缓存，之后才会更新到主内存。如果此时还没有将数据更新到主内存，其他线程来读取的就是修改之前的数据
![](https://github.com/luoyejingxue/interview-note/blob/master/java/visibility.jpg)
		如图所示，volatile关键字保证了内存可见性，当线程A更新volatile修饰的变量时，它会立即刷新到主线程，并将其他缓存中的该变量的值清空，导致其余 线程只能去主内存读取最新的值
		使用volatile关键字修饰的变量每次读取都会得到最新的数据，不管那个线程对这个变量的修改都会立即刷新到主内存中
		对volatile修饰的变量的写操作可能在读操作之前

	##### 顺序性
		JVM为了提高整体的效率进行指令重排，会导致执行的代码顺序变更
		java中volatile可以保证顺序性，synchronized和lock也可保证顺序性

#### synchronized 关键字
	