# 原型模式

## 模式说明

需要获取一个类的多个实例的场景下，若该类的构造器需要做较多的初始化工作，则创建多个实例，会因为每次创建都需要执行构造器中的初始化过程，耗时耗资源。如果在创建第一个实例后，后续实例都直接拷贝该实例，就可以避免每次获取实例时的初始化开销，对于需要修改的字段针对性修改即可，这就是原型模式。根据对拷贝对象中的引用类型字段的拷贝程度分为浅拷贝和深拷贝。

**浅拷贝**

对原型`P`的实例`p1`执行实例`p1.clone()`方法拷贝给`p2`时(`P p2 = p1.clone()`)，对`p1`内的引用类型字段(准确地说是可变引用类型)`q`(类型`Q`)，只拷贝其引用，这就是浅拷贝。Java中重写`clone`方法时若只调用`super.clone()`，则该`clone`方法在使用时即为浅拷贝，即上述`p1.q == p2.q`。注意`clone`方法在`Cloneable`接口中是`native`方法，返回类型是`Object`，赋值给`p2`时要向下转型。

```java
// 第一个实例通过new创建
P p1 = new P();
// 此后通过拷贝创建
P p2 = (P)p1.clone();
```

可以这样描述：`p1.clone()`在堆中生成一个新的实例，p2是这个新实例的引用(地址)，新实例中有`p1`的基本数据和不变类型(`final`修饰的引用类型如`String`)的独立的完整拷贝，但对于可变的引用类型，只拷贝其引用。即如果`p1`中有一可变引用字段`Q`，那么`p1.q == p2.q，p1`和`p2`中的`q`指向同一个实例，即原本`p1`中的`q`实例。

**深拷贝**

以上述浅拷贝中的描述为例，深拷贝对`p`中的可变引用类型`q`，也拷贝一个完整的`q`到新实例`p2`中(与`q`并列的独立的`Q`类型新实例)，此时`p1.q !=p2.q`。深拷贝要求`Q`自身也要实现`Cloneable`接口并重写`clone`方法。并在原型`P`的重写`clone`方法内针对`q`要有类似如下的语句。

```java
p.q = (Q)p.getQ().clone();
```

\


本示例定义一个`Person`类，使其实现`Cloneable`接口并通过重写`clone`方法使其支持浅拷贝。在`Person`类中定义一个不变引用类型`Country`和一个可变引用类型`WorkExperience`，展示浅拷贝`Person`时，对不变类型`Country`拷贝出一个新实例，对可变引用类型`WorkExperience`拷贝的是它的引用(地址)。作为对比，本示例定义一个`PersonDeep`类，该类内有一个可变引用类型`WorkExperienceDeep`，使`WorkExperienceDeep`也实现`Cloneable`接口并重写`clone`方法，且在`PersonDeep`类的`clone`方法中，针对可变引用l类型`WorkExperienceDeep`，执行一条对它的`clone`方法来得到它的一个实例，从而实现对`PersonDeep`的深拷贝。

\


## 结构

**原型接口**: 通常为`Cloneable`。 **具体原型类**: 实现`Cloneable`接口并重写`clone`方法，分为深拷贝和浅拷贝。

\


### 代码演示

```java
package com.yukiyama.pattern.creation;

/**
 * 原型模式
 */
public class ProtoTypeDemo {

	public static void main(String[] args) {
		System.out.println("====如下是深拷贝示例====");
		// 声明一个支持深拷贝的实例p1
		PersonDeep pd1 = new PersonDeep();
		// 通过原型类中的clone()方法拷贝p1
		PersonDeep pd2 = (PersonDeep) pd1.clone();
		// p1和p2是两个不同的实例，输出“false”
		System.out.println(pd1 == pd2);
		// 设置p1
		pd1.setName("张三");
		pd1.setOccupation("程序员");
		pd1.setWorkExperienceDeep("Alibaba");
		// 设置拷贝而来的p2
		pd2.setName("李四");
		pd2.setOccupation("老师");
		pd2.setWorkExperienceDeep("Tsinghua");
		// 以下3行分别输出“张三”， “程序员”， “Alibaba”
		// WorkExperienceDeep没有被pd2修改为"Tsinghua"，说明PersonDeep是深拷贝类型
		System.out.println(pd1.getName());
		System.out.println(pd1.getOccupation());
		System.out.println(pd1.getWorkExperience().getCompany());
		// 以下3行分别输出“李四”， “老师”， “Tsinghua”
		System.out.println(pd2.getName());
		System.out.println(pd2.getOccupation());
		System.out.println(pd2.getWorkExperience().getCompany());
		// 如下输出“false”，说明p1和p2中的引用类型WorkExperience是不同的实例
		System.out.println(pd1.getWorkExperience() == pd2.getWorkExperience());
		
		System.out.println("====如下是浅拷贝示例====");
		Person p1 = new Person();
		Person p2 = (Person) p1.clone();
		// p1和p2是两个不同的实例，输出“false”
		System.out.println(p1 == p2);
		// 设置p1
		p1.setName("张三");
		p1.setOccupation("程序员");
		p1.setWorkExperience("Alibaba");
		p1.setCountry("CHINA");
		// 设置拷贝而来的p2
		p2.setName("李四");
		p2.setOccupation("老师");
		p2.setWorkExperience("Tsinghua");
		p2.setCountry("SINGAPORE");
		// 以下4行分别输出“张三”， “程序员”， “Tsinghua”，“CHINA”
		// WorkExperience被p2修改为"Tsinghua"，说明Person是浅拷贝类型
		// 另外，p1中的CHINA并没有因为p2设置为了SINGAPORE而受到影响，
		// 说明自定义的不变类Country在浅拷贝中也拷贝到了新实例，而不是只拷贝其引用
		System.out.println(p1.getName());
		System.out.println(p1.getOccupation());
		System.out.println(p1.getWorkExperience().getCompany());
		System.out.println(p1.getCountry());
		// 以下4行分别输出“李四”， “老师”， “Tsinghua”，“SINGAPORE”
		System.out.println(p2.getName());
		System.out.println(p2.getOccupation());
		System.out.println(p2.getWorkExperience().getCompany());
		System.out.println(p2.getCountry());
		// 如下输出“true”，说明p1和p2中的引用类型WorkExperience是相同的实例
		System.out.println(p1.getWorkExperience() == p2.getWorkExperience());
		// 如下输出“false”，说明p1和p2中的引用类型Country是不同的实例
		System.out.println(p1.getCountry() == p2.getCountry());
	}
}

/**
 * 深拷贝版
 * 原型类
 * 实现Cloneable接口并重写clone方法
 * 本类中的非不变引用类型字段也通过该字段自身的clone()进行拷贝
 */
class PersonDeep implements Cloneable{
	private String name;
	private String occupation;
	private int age;
	private WorkExperienceDeep workExperience;
	
	// 在构造器中实例化引用类型WorkExperience
	public PersonDeep() {
		workExperience = new WorkExperienceDeep();
	}
	
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getOccupation() {
		return occupation;
	}
	public void setOccupation(String occupation) {
		this.occupation = occupation;
	}
	public WorkExperienceDeep getWorkExperience() {
		return workExperience;
	}
	public void setWorkExperienceDeep(String company) {
		this.workExperience.setCompany(company);
	}
	// 重写clone()使得原型类Person能够执行clone()从而被拷贝
	@Override
	protected Object clone() {
		PersonDeep person = null;
		try {
			person = (PersonDeep) super.clone();
			person.workExperience = (WorkExperienceDeep) person.getWorkExperience().clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		return person;
	}
}

/**
 * 深拷贝版
 * 原型类型中的引用类型字段
 */
class WorkExperienceDeep implements Cloneable{
	private String company;

	public String getCompany() {
		return company;
	}
	public void setCompany(String company) {
		this.company = company;
	}
	@Override
	protected Object clone() {
		WorkExperienceDeep we = null;
		try {
			we = (WorkExperienceDeep) super.clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		return we;
	}
}

/**
 * 浅拷贝版
 * 原型类，实现Cloneable接口并重写clone方法
 */
class Person implements Cloneable{
	private String name;
	private String occupation;
	private int age;
	private WorkExperience workExperience;
	private Country country;
	
	// 在构造器中实例化引用类型WorkExperience
	public Person() {
		workExperience = new WorkExperience();
	}
	
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getOccupation() {
		return occupation;
	}
	public void setOccupation(String occupation) {
		this.occupation = occupation;
	}
	public WorkExperience getWorkExperience() {
		return workExperience;
	}
	public String getCountry() {
		return country.getCountryName();
	}
	public void setCountry(String countryName) {
		this.country = new Country(countryName);
	}

	public void setWorkExperience(String company) {
		this.workExperience.setCompany(company);
	}
	// 重写clone()使得原型类Person能够对外拷贝
	@Override
	protected Object clone() {
		Person person = null;
		try {
			person = (Person) super.clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		return person;
	}
}

/**
 * 浅拷贝版
 * 原型类型中的引用类型字段
 */
class WorkExperience{
	private String company;

	public String getCompany() {
		return company;
	}
	public void setCompany(String company) {
		this.company = company;
	}
}

/**
 * 浅拷贝版
 * 自定义不变类，用于演示在浅拷贝中对不变类的拷贝结果
 */
class Country{
	private String countryName;
	
	public Country(String countryName) {
		this.countryName = countryName;
	}
	public String getCountryName() {
		return countryName;
	}
}
```
