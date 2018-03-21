<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [【JAVA 类的加载过程】](#java-%E7%B1%BB%E7%9A%84%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B)
- [类的加载过程](#%E7%B1%BB%E7%9A%84%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B)
  - [类文件加载的顺序](#%E7%B1%BB%E6%96%87%E4%BB%B6%E5%8A%A0%E8%BD%BD%E7%9A%84%E9%A1%BA%E5%BA%8F)
  - [类实例创建过程](#%E7%B1%BB%E5%AE%9E%E4%BE%8B%E5%88%9B%E5%BB%BA%E8%BF%87%E7%A8%8B)
  - [静态方法和非静态方法都是被动调用](#%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95%E5%92%8C%E9%9D%9E%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95%E9%83%BD%E6%98%AF%E8%A2%AB%E5%8A%A8%E8%B0%83%E7%94%A8)
- [相关概念](#%E7%9B%B8%E5%85%B3%E6%A6%82%E5%BF%B5)
  - [static关键字：](#static%E5%85%B3%E9%94%AE%E5%AD%97)
    - [被修饰后的成员具备以下特点：](#%E8%A2%AB%E4%BF%AE%E9%A5%B0%E5%90%8E%E7%9A%84%E6%88%90%E5%91%98%E5%85%B7%E5%A4%87%E4%BB%A5%E4%B8%8B%E7%89%B9%E7%82%B9)
      - [随着类的加载而加载（类一加载，静态数据就立即在内存中加载空间）](#%E9%9A%8F%E7%9D%80%E7%B1%BB%E7%9A%84%E5%8A%A0%E8%BD%BD%E8%80%8C%E5%8A%A0%E8%BD%BD%E7%B1%BB%E4%B8%80%E5%8A%A0%E8%BD%BD%E9%9D%99%E6%80%81%E6%95%B0%E6%8D%AE%E5%B0%B1%E7%AB%8B%E5%8D%B3%E5%9C%A8%E5%86%85%E5%AD%98%E4%B8%AD%E5%8A%A0%E8%BD%BD%E7%A9%BA%E9%97%B4)
      - [优先于对象存在（对象消失了，static还在）](#%E4%BC%98%E5%85%88%E4%BA%8E%E5%AF%B9%E8%B1%A1%E5%AD%98%E5%9C%A8%E5%AF%B9%E8%B1%A1%E6%B6%88%E5%A4%B1%E4%BA%86static%E8%BF%98%E5%9C%A8)
      - [被所有对象所共享](#%E8%A2%AB%E6%89%80%E6%9C%89%E5%AF%B9%E8%B1%A1%E6%89%80%E5%85%B1%E4%BA%AB)
      - [当成员被静态修饰后，除了可以被对象调用外，还可以直接被类名调用](#%E5%BD%93%E6%88%90%E5%91%98%E8%A2%AB%E9%9D%99%E6%80%81%E4%BF%AE%E9%A5%B0%E5%90%8E%E9%99%A4%E4%BA%86%E5%8F%AF%E4%BB%A5%E8%A2%AB%E5%AF%B9%E8%B1%A1%E8%B0%83%E7%94%A8%E5%A4%96%E8%BF%98%E5%8F%AF%E4%BB%A5%E7%9B%B4%E6%8E%A5%E8%A2%AB%E7%B1%BB%E5%90%8D%E8%B0%83%E7%94%A8)
    - [使用注意](#%E4%BD%BF%E7%94%A8%E6%B3%A8%E6%84%8F)
      - [静态方法只能访问静态成员（方法和变量）](#%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95%E5%8F%AA%E8%83%BD%E8%AE%BF%E9%97%AE%E9%9D%99%E6%80%81%E6%88%90%E5%91%98%E6%96%B9%E6%B3%95%E5%92%8C%E5%8F%98%E9%87%8F)
      - [静态方法中不可以写this，super关键字](#%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95%E4%B8%AD%E4%B8%8D%E5%8F%AF%E4%BB%A5%E5%86%99thissuper%E5%85%B3%E9%94%AE%E5%AD%97)
      - [主函数是静态的](#%E4%B8%BB%E5%87%BD%E6%95%B0%E6%98%AF%E9%9D%99%E6%80%81%E7%9A%84)
    - [何时使用静态？](#%E4%BD%95%E6%97%B6%E4%BD%BF%E7%94%A8%E9%9D%99%E6%80%81)
      - [何时定义静态变量（类变量）](#%E4%BD%95%E6%97%B6%E5%AE%9A%E4%B9%89%E9%9D%99%E6%80%81%E5%8F%98%E9%87%8F%E7%B1%BB%E5%8F%98%E9%87%8F)
      - [何时定义静态函数](#%E4%BD%95%E6%97%B6%E5%AE%9A%E4%B9%89%E9%9D%99%E6%80%81%E5%87%BD%E6%95%B0)
    - [静态利弊：](#%E9%9D%99%E6%80%81%E5%88%A9%E5%BC%8A)
  - [内存结构](#%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84)
    - [栈内存](#%E6%A0%88%E5%86%85%E5%AD%98)
- [验证](#%E9%AA%8C%E8%AF%81)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



# 【JAVA 类的加载过程】
# 类的加载过程
## 类文件加载的顺序
1、先加载执行父类的静态变量及静态初始化块(执行先后顺序按排列的先后顺序)
2、再加载执行本类的静态变量及静态初始化块
>只要类没有被销毁，**静态变量及静态初始化块只会执行1次**，后续再对该类进行其他操作也不会再执行这两个步骤。

## 类实例创建过程
只有在调用new方法时才会创建类的实例
1、按照上面[类文件加载的顺序]()（类已被加载则跳过此步）
2、父类的非静态变量及非静态初始化块
3、父类的构造方法
4、本类的非静态变量及非静态初始化块
5、本类的构造方法
4、类实例销毁时候，首先销毁子类部分，再销毁父类部分

## 静态方法和非静态方法都是被动调用
即系统不会自动调用执行。所以用户没有调用时都不执行，主要区别在于静态方法可以直接用类名直接调用(实例化对象也可以)，而非静态方法只能先实例化对象后才能调用。

# 相关概念
## static关键字：
是一个修饰符，用于修饰成员（成员变量和成员函数）

### 被修饰后的成员具备以下特点：
#### 随着类的加载而加载（类一加载，静态数据就立即在内存中加载空间）
随着类的消失而消失，说明它的生命周期最长
#### 优先于对象存在（对象消失了，static还在）
静态先存在，对象后存在
#### 被所有对象所共享
节约内存空间
#### 当成员被静态修饰后，除了可以被对象调用外，还可以直接被类名调用
写法：类名.静态成员

### 使用注意
#### 静态方法只能访问静态成员（方法和变量）
非静态方法既可以访问静态也可以访问非静态
#### 静态方法中不可以写this，super关键字
因为静态优先于对象存在，所以静态方法中不可以出现this
#### 主函数是静态的
public static void main(String[] args){}

### 何时使用静态？
要从两方面下手：因为静态修饰的内容有成员变量和函数。
#### 何时定义静态变量（类变量）
当对象中出现共享数据时，该数据被静态所修饰。对象中的特有数据要定义成非静态存在于堆内存中。
#### 何时定义静态函数
当功能内部没有访问到非静态数据（对象特有数据），该功能可以定义成静态。

### 静态利弊：
利：
1、对对象的共享数据单独空间的存储，节省空间。没有必要每个对象都存储一份。
2、可以直接被类名调用
弊：
1、生命周期过长
2、访问出现局限性（只能访问静态）

## 内存结构
Java程序在运行时，需要在内存中的分配空间。为了提高运算效率，有对空间进行了不同区域的划分，因为每一片区域都有特定的处理数据方式和内 存管理方式。
### 栈内存
* 用于存储**局部变量**
* 当数据使用完，所占空间会**自动释放**
### 堆内存
* **数组和对象**（实体），通过new建立的实例都存放在堆内存中（**成员变量**随着对象的建立而建立，存在于对象所在的堆内存中）
* 每一个实体都有内存地址值（变量通过地址引用）
* 实体中的变量都有默认初始化值
* 实体不再被使用，会在不确定的时间内被垃圾回收器回收(**垃圾回收**机制)
### 方法区，本地方法区，寄存器



# 验证
| 加载顺序         | 父类静态变量=1                | 父类非静态变量=1 | 子类静态变量=1 | 子类非静态变量=1 |
| ------------ | ----------------------- | --------- | -------- | --------- |
| 【父类调用父类静态方法】 | Parent.pStaticMethod(); |           |          |           |
| 父类静态初始化块一    | 2                       |           |          |           |
| 父类静态初始化块二    | 3                       |           |          |           |
| 父类静态方法       | 4                       |           |          |           |
| 【子类调用子类静态方法】 | Child.cStaticMethod();  |           |          |           |
| 子类静态初始化块一    | 5                       |           | 2        |           |
| 子类静态初始化块二    | 6                       |           | 3        |           |
| 子类静态方法       | 7                       |           | 4        |           |
| 【子类实例化】      | Child c=new Child();    |           |          |           |
| 父类非静态初始化块一   | 8                       | 2         |          |           |
| 父类非静态初始化块二   | 9                       | 3         |          |           |
| 父类构造方法       | 10                      | 4         |          |           |
| 子类非静态初始化块一   | 11                      | 5         | 5        | 2         |
| 子类非静态初始化块二   | 12                      | 6         | 6        | 3         |
| 子类构造方法       | 13                      | 7         | 7        | 4         |
| 【父类实例化子类对象】  | Parent p=new Child();   |           |          |           |
| 父类非静态初始化块一   | 14                      | 2         |          |           |
| 父类非静态初始化块二   | 15                      | 3         |          |           |
| 父类构造方法       | 16                      | 4         |          |           |
| 子类非静态初始化块一   | 17                      | 5         | 8        | 2         |
| 子类非静态初始化块二   | 18                      | 6         | 9        | 3         |
| 子类构造方法       | 19                      | 7         | 10       | 4         |

| 加载顺序         | 父类静态变量=1                | 父类非静态变量=1 | 子类静态变量=1 | 子类非静态变量=1 |
| ------------ | ----------------------- | --------- | -------- | --------- |
| 【子类实例化】      | Child c=new Child();    |           |          |           |
| 父类静态初始化块一    | 2                       |           |          |           |
| 父类静态初始化块二    | 3                       |           |          |           |
| 子类静态初始化块一    | 4                       |           | 2        |           |
| 子类静态初始化块二    | 5                       |           | 3        |           |
| 父类非静态初始化块一   | 6                       | 2         |          |           |
| 父类非静态初始化块二   | 7                       | 3         |          |           |
| 父类构造方法       | 8                       | 4         |          |           |
| 子类非静态初始化块一   | 9                       | 5         | 4        | 2         |
| 子类非静态初始化块二   | 10                      | 6         | 5        | 3         |
| 子类构造方法       | 11                      | 7         | 6        | 4         |
| 【父类实例化子类对象】  | Parent p=new Child();   |           |          |           |
| 父类非静态初始化块一   | 12                      | 2         |          |           |
| 父类非静态初始化块二   | 13                      | 3         |          |           |
| 父类构造方法       | 14                      | 4         |          |           |
| 子类非静态初始化块一   | 15                      | 5         | 7        | 2         |
| 子类非静态初始化块二   | 16                      | 6         | 8        | 3         |
| 子类构造方法       | 17                      | 7         | 9        | 4         |
| 【父类调用父类静态方法】 | Parent.pStaticMethod(); |           |          |           |
| 父类静态方法       | 18                      |           |          |           |
| 【子类调用子类静态方法】 | Child.cStaticMethod();  |           |          |           |
| 子类静态方法       | 19                      |           | 10       |           |

```
public class ClassTest {	
	public static void main (String args[]) {
		System.out.println("【子类实例化】|Child c=new Child();");		
		Child c=new Child();
		System.out.println("【父类实例化子类对象】|Parent p=new Child();");		
		Parent p=new Child();
		System.out.println("【父类调用父类静态方法】|Parent.pStaticMethod();");
		Parent.pStaticMethod();
		System.out.println("【子类调用子类静态方法】|Child.cStaticMethod();");
		Child.cStaticMethod();
	}
}
```

```
public class ClassTest2 {	
	public static void main (String args[]) {
		System.out.println("【父类调用父类静态方法】|Parent.pStaticMethod();");
		Parent.pStaticMethod();
		System.out.println("【子类调用子类静态方法】|Child.cStaticMethod();");
		Child.cStaticMethod();
		System.out.println("【子类实例化】|Child c=new Child();");		
		Child c=new Child();
		System.out.println("【父类实例化子类对象】|Parent p=new Child();");		
		Parent p=new Child();
	}
}
```

```
public class Parent {
	// 父类静态变量
	static int m = 1;	
	// 父类非静态变量
	int n = 1;
	
	// 静态语句块1
	static {
		m++;
		// j++; 父类非静态变量不能在静态语句块中使用
		System.out.println("父类静态初始化块一|" + m);
	}
	// 静态语句块2
	static {
		m++;
		System.out.println("父类静态初始化块二|" + m);
	}

	// 构造函数
	public Parent() {
		m++;
		n++;
		System.out.println("父类构造方法|" + m + "|" + n);
	}

	// 非静态语句块
	{
		m++;
		n++;
		System.out.println("父类非静态初始化块一|" + m + "|" + n);
	}
	
	// 非静态语句块
	{
		m++;
		n++;
		System.out.println("父类非静态初始化块二|" + m + "|" + n);
	}

	// 非静态方法
	public void pMethod() {
		m++;
		n++;
		System.out.println("父类非静态方法|" + m + "|" + n);
		return;
	}

	// 静态方法
	public static void pStaticMethod() {
		m++;
//		j++; 父类非静态变量不能在静态方法中使用
		System.out.println("父类静态方法|" + m);
		return;
	}

	@Override
	protected void finalize() throws Throwable {
		super.finalize();
		System.out.println("销毁父类|");
	}
}
```

```
public class Child extends Parent {
	// 静态变量
	static int i = 1;
	// 非静态变量
	int j = 1;

	// 静态语句块1
	static {
		m++;
		i++;
		// j++; 非静态变量不能在静态语句块中使用
		System.out.println("子类静态初始化块一 " + "|" + m + "||" + i);
	}
	// 静态语句块2
	static {
		m++;
		i++;
		System.out.println("子类静态初始化块二 " + "|" + m + "||" + i);
	}

	// 构造函数
	public Child() {
		m++;
		n++;
		i++;
		j++;
		System.out.println("子类构造方法 " + "|" + m + "|" + n + "|" + i + "|" + j);
	}

	// 非静态语句块
	{
		m++;
		n++;
		i++;
		j++;
		System.out.println("子类非静态初始化块一" + "|" + m + "|" + n + "|" + i + "|" + j);
	}
	// 非静态语句块
	{
		m++;
		n++;
		i++;
		j++;
		System.out.println("子类非静态初始化块二" + "|" + m + "|" + n + "|" + i + "|" + j);
	}

	// 非静态方法
	public void pMethod() {
		m++;
		n++;
		i++;
		j++;
		System.out.println("子类继承非静态方法" + "|" + m + "|" + n + "|" + i + "|" + j);
		return;
	}

	// 静态方法
	public static void pStaticMethod() {// 静态方法不能被继承
		m++;
		i++;
		// j++; 非静态变量不能在静态方法中使用
		return;
	}

	// 非静态方法
	public void cMethod() {
		m++;
		n++;
		i++;
		j++;
		System.out.println("子类非静态方法" + "|" + m + "|" + n + "|" + i + "|" + j);
		return;
	}

	// 静态方法
	public static void cStaticMethod() {
		m++;
		i++;
		// j++; 非静态变量不能在静态方法中使用
		System.out.println("子类静态方法 " + "|" + m + "||" + i);
		return;
	}

	@Override
	protected void finalize() throws Throwable {
		super.finalize();
		System.out.println("销毁子类|");
	}
}
```




引用：
[类的加载顺序](http://www.cnblogs.com/guoyuqiangf8/archive/2012/10/31/2748909.html)
[Java中类的加载顺序剖析（常用于面试题）](http://www.jb51.net/article/62622.htm)



