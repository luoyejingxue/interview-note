##### ArrayList/Vector
	1. ArrayList 底层与 Vector底层都是使用的 Object数组
	2. ArrayList 不是线程安全的，Vectir类的所有方法上加了同步，所以是线程安全的
	3. 底层是数组的消耗，主要是在数组的扩容，以及指定位置添加数据

##### LinkedList/ArrayList
	1. ArrayList 底层是Object数组，LinkedList底层是双向循环链表数据结构
	2. ArrayList 与 LinkedList 都不是同步的，也不能保证线程安全
	3. 受到底层数据结构影响，ArrayList的空间浪费主要在list列表结尾会预留一部分的空间，而LinkedList的花费主要在每个元素多出了一个直接前驱，一个直接后继的指向
	4. ArrayList插入和删除元素，操作的是底层Object素组，时间复杂度为O(1),而LinkedList采用链表
		，复杂度在O(1)
	5. ArrayList 实现RandomAccess ，可以通过索引快速定位元素，所以支持快速随机访问，而LinkedList没有
链表的结构
![](https://github.com/luoyejingxue/interview-note/blob/master/java/linkedList.jpg)

##### HashMap底层实现
![](https://github.com/luoyejingxue/interview-note/blob/master/java/hashmap1.7.jpg)
![](https://github.com/luoyejingxue/interview-note/blob/master/java/hashmap1.8.jpg)
	HashMap 底层是基于数组和链表（红黑树）实现的
	其中有两个重要参数：
		容量 ，默认为16
		负载因子，默认为0.75
	当size > 16 * 0.75 时就会发生扩容

	put方法
		对传入的key做hash运算，计算出hashCode，然后根据数组长度取模计算出数组中的index的下标
		由于在计算中使用位运算比取模运算效率高很多，所以hashmap中规定的数组长度为2的n次方，其次key的hash值 & length-1 是一个质数
			，减少hash碰撞
		另外由于数组的长度限制，会出现不同的key在同一个index上面，这样就会通过table[index]处形成链表，采用头插法将数据插入到链表
	get方法
		对传入的key计算index,遍历链表，通过key.equals(k)来查找对应的元素
```
//推荐使用这种遍历方式，直接把key,value同时取出，不需要在去遍历数据
Iterator<Map.Entry<String, Integer>> entryIterator = map.entrySet().iterator();
        while (entryIterator.hasNext()) {
            Map.Entry<String, Integer> next = entryIterator.next();
            System.out.println("key=" + next.getKey() + " value=" + next.getValue());
        }
```
		
	并发环境HashMap，容易出现死循环
		并发场景发生扩容，调用resize()方法里面的rehash()时，容易出现环形链表，这样获取一个不存在的key时，
		当key的index正好在环形链表的下标时，就出现死循环

	HashMap在JDK1.7和JDK1.8的比较
![](https://github.com/luoyejingxue/interview-note/blob/master/java/hashmapcompare.jpg)
	
	HashMap1.8中put的流程图
![](https://github.com/luoyejingxue/interview-note/blob/master/java/hashmapput.jpg)

#### HashSet

#### LinkedHashMap