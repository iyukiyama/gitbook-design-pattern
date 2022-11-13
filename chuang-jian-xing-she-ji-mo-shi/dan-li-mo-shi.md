# 单例模式

## 模式说明

某些场景要求一个对象只能有一个实例，可以通过单例模式实现。由于只能有一个实例，因此该类的构造器必须用`private`修饰来禁止通过`new`操作符来新建实例。类内持有一个`private`修饰的本类类型的实例，声明为类成员，如该类类名`Singleton`，可声明为： `private static Singleton instance;` 通过类内的一个`public`修饰的`getIntance`方法来获取实例，该方法也是`static`的。根据实例产生的时机分为饿汉模式和懒汉模式，懒汉模式中有线程安全和不安全的写法。

**饿汉模式** 类属性Singleton instance在声明时就实例化。 `private static Singleton instance = new Singleton();`

**懒汉模式** 类属性`Singleton`只声明但不实例化，将实例化动作放到`getInstance`方法中，则只有调用`getInstance`时才能获取`Singleton`的实例。懒汉模式因为在调用`getInstance`时才获得实例，存在多线程竞争的问题，因此可以结合`volatile`(避免指令重排)和`synchronized`(保证原子性)以双锁检测方式将`Singleton`写成线程安全的类。

本示例演示饿汉写法，非线程安全懒汉写法和双锁检测线程安全懒汉写法。

\


## 结构

**单例类**: 持有一个本类的类属性，维护一个`getInstance`方法。

\


## 代码演示

```java
package com.yukiyama.pattern.creation;

/**
 * 单例模式
 */
public class SingletonDemo {

	public static void main(String[] args) {
		SingletonSimple s1 = SingletonSimple.getInstance();
		SingletonSimple s2 = SingletonSimple.getInstance();
		// 输出“true”，说明s1与s2是同一个实例
		System.out.println(s1 == s2);
		SingletonHungry s3 = SingletonHungry.getInstance();
		SingletonHungry s4 = SingletonHungry.getInstance();
		// 输出“true”，说明s3与s4是同一个实例
		System.out.println(s3 == s4);
		Singleton s5 = Singleton.getInstance();
		Singleton s6 = Singleton.getInstance();
		// 输出“true”，说明s3与s4是同一个实例
		System.out.println(s5 == s6);
	}

}

/**
 * 饿汉模式
 * 在类加载时创建常量化实例，不存在多线程导致可能出现多个实例的问题
 */
class SingletonHungry{
	// 在定义SingletonHungry类型的属性时直接实例化，类内可以访问private构造器
	private static final SingletonHungry instance = new SingletonHungry();
	
	// 将构造器声明为private，外部无法用new获取
	private SingletonHungry() {}
	// 外部通过一个public的getInstance()方法获取该类实例
	public static SingletonHungry getInstance() {
		return instance;
	}
}

/**
 * 懒汉模式
 * 非线程安全版
 */
class SingletonSimple {
	private static SingletonSimple instance;
	
	// 将构造器声明为private，外部无法用new获取
	private SingletonSimple() {}
	// 外部通过一个public的getInstance()方法获取该类实例
	public static SingletonSimple getInstance() {
		// 每次获取前判断该类实例是否已存在，若无则new一个
		if (instance != null) {
			instance = new SingletonSimple();
		}
		return instance;
	}

}

/**
 * 多线程下的双锁检测(Double-Check Locking)单例
 * 懒汉模式
 */
class Singleton{
	// 以volatile修饰
	private static volatile Singleton instance;
	
	private Singleton() {}
	public static Singleton getInstance() {
		// 第一次判断的目的是避免每次getInstance()都加锁
		// 若已经存在实例，直接返回
		if(instance == null) {
			synchronized (Singleton.class) {
				// 再次判断是防止两个线程在instance==null时
				// 同时进入第一个if内，由于加锁，其中一个先new了
				// 实例，此时必须再判断一次防止第二个也new一个实例
				if(instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```
