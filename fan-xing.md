# 泛型

#### 一、泛型的作用与定义

**1.1 泛型的作用**

使用泛型能写出更加灵活通用的代码 泛型的设计主要参照了 C++的模板，旨在能让人写出更加通用化，更加灵活的代码。模板/泛型代码，就好像做雕塑时的模板，有了模板，需要生产的时候就只管向里面注入具体的材料就行，不同的材料可以产生不同的效果，这便是泛型最初的设计宗旨。

> **泛型将代码安全性检查提前到编译期**

泛型被加入 Java 语法中，还有一个最大的原因:解决容器的类型安全，使用泛型后，能让编译器在编译的时候借助传入的类型参数检查对容器的插入，获取操作是否合法，从而将运行时ClassCastException转移到编译时。比如：

```java
List dogs =new ArrayList(); 
dogs.add(new Cat());
```

在没有泛型之前，这种代码除非运行，否则你永远找不到它的错误。但是加入泛型之后

```java
List<Dog> dogs=new ArrayList<>();
dogs.add(new Cat());//Error Compile
```

会在编译的时候就检查出来。

> **泛型能够省去类型强制转换**

在JDK1.5之前，Java容器都是通过将类型向上转型为Object类型来实现的，因此在从容器中取出来的时候需要手动的强制转换。

```java
Dog dog=(Dog)dogs.get(1);
```

加入泛型后，由于编译器知道了具体的类型，因此编译期会自动强制转换，使得代码更加优雅。

**1.2泛型的定义**

自 JDK 1.5 之后，Java 通过泛型解决了容器类型安全这一问题，而几乎所有人接触泛型也是通过 Java 的容器。那么泛型究竟是什么?

> **泛型的本质是参数化类型**

也就是说，泛型就是将所操作的数据类型作为参数的一种语法。

```java
public class Play<T>{
		T play(){
		
		}
}
```

其中T就是作为一个类型参数在Play被实例化的时候所传递来的参数，比如：

```java
Play<Integer> playInteger = new Play<>();
```

这里T就会被实例化为Integer

#### 二、通配符与嵌套

**2.1 通配符**

`？`：表示类型不确定，只能用于声明变量或者形参上，不能用在创建泛型类、泛型方法和接口上

```java
public static void main(String[] args){
		List<?> list = new ArrayList<Integer>();
		list = new ArrayList<String>();
		test(list);

		public static void test(List<?> list){
				
		}
}
```

**2.2泛型嵌套**

定义两个泛型类，MyClass类的泛型就是student类，而student类的泛型是String类

```java
Class Student<T>{
		private T score;
		public T getScore(){
				return score;	
		}
		public void setScore(T score){
				this.score = score;
		}
}
Class MyClass<T>{
		private T cls;
		public T getCls(){
				return cls;
		}
		public void setCls(T cls){
				this.cls = cls;
		}
		
		public static void main(String[] args){
				Student<String> stu = new Student<String>();
				stu.setScore("great");
		}

		MyClass<Student<String>> cls = new MyClass<Student<String>>();
		cls.setCls(stu);
		Student<String> stu2 = new Student<String>();
		stu2 = cls.getCls();
		System.out.println(stu2.getScore());//great
}
```

如上就实现了泛型的嵌套，在HashMap中对键值对进行遍历的时候，也利用了泛型的嵌套

```java
public static void main(String[] args){
		Map<String,String> map = new HashMap<String,String>();
		map.put("a","java");
		map.put("b","python");
		
		Set<Entry<String,String>> entrySet = map.entrySet();
		for(){
				String key = entry.getKey();
				String value = entry.getValue();
		}
}
```

#### 三、泛型的上下边界

`? extends E`是泛型的上边界，`? super E`是泛型的下边界

**3.1 \<? extends E>**

List\<? extends A> 表示这个list里面存的是A的子类，具体是啥不知道，只知道范围。

可假设两个范围，A子类，A父类

1. 关于add：
   * A子类：如果你允许add的元素是A的子类，由于泛型参数限定`? extends A`,所以ArrayList可以指定的类型也是A的子类，那么无法保证add的对象一定是ArrayList指定类型的子类对象，比如：ArrayList指定C，list却add了A()对象，这就是非法的！（因为list并不知道指定了C，它可以确定的范围就是可以add所有A的子类）所以A子类该范围不行！
   * A父类：ArrayList指定类型肯定在(?,A]范围内，所以这种情况肯定不行！
2.  关于get：

    list不知道你存入的ArrayList事什么类型，但是可以确定使用A的引用肯定可以接受ArrayList中的元素（无论ArrayList设定什么类型），因为A肯定是里面元素的父类。但是使用其他类型接受就不行，因为如果ArrayList的类型是指定的类型的父类，是接受不了的。例如：`BaseStudent extends Student`

    ```java
    public class Main{
    		public static void main(String[] args){
          	ArrayList<Student> list1 = new ArrayList<>();
          	list1.add(new Student("1",1));
          	ArrayList<Student> list2 = new ArrayList();
          	list2.add(new Student("2",2));
          	//------
          	list1.addAll(list2);
          	System.out.println(list1);
          	//[Student[name=1,age=1],Student[name=2,age=2]]
          	//------
          	ArrayList<BaseStudent> list3 = new ArrayList<>();
          	list3.add(new BaseStudent("3",3));
          	list1.addAll(list3);
          	System.out.println(list1);
          	//[Student[name=1,age=1],Student[name=2,age=2],Student[name=3,age=3]]
        }
    }
    ```



**3.2\<? super E>**

`List<? super C>`表示list里面存的是C和其父类，具体是啥不确定，只知道范围。

* 关于add：
  1. C子类：如果允许add，就允许list来add任何C的子类元素，因为ArrayList指定的范围是C和其父类，ArrayList可以准确接受假设范围的所有对象。所以，C子类这个范围可行，这样就有一个标准的类型判断依据，不像extends，根本无法确定判断标准，编译器就不会知道怎么做。
  2. C父类：显然和上面A子类情况是一样。因为list无法知道ArrayList指定类型，所以无法确定判断标准。
* 关于get：
  1. 因为list只允许add C的子类对象，你或许在想：那我直接用C不就可以接受ArrayList数据吗？并不是的，ArrayList在赋值给list之前可能里面已经有值了，这个值的类型如果是ArrayList指定类型，并且是C的父类，那么C就无法接受！而且list不知道ArrayList会指定哪个类型，只知道范围，无法确定是哪个父类，所以干脆用Object那肯定就能接受了，所以返回的其实只有Object能接受，否则就得强转。

#### 四、RxJava中深入理解泛型

**4.1 响应式编程**

与我们传统编码(函数式编程)不一样,传统编码是做完这件事之后做另外一件事,给人的感 觉都是单线程的,可能会开新线程去。处理耗时操作,在处理完成之后通过回调去处理之后的事情。而响应式编程提供给我们的是一种不一样的思想,在响应式编程的世界中一切执行流程都是基于事件的,以事件为驱动。

**4.2 观察者模式**

观察者模式是这样子的,我先举个例子看大家能不能理解

老师在讲台上讲课,而所有的学生都会观察着老师的一举一动,而老师每产生一个事件(比 如说在黑板上写下一串公式),则对应着所有的学生都观察到了老师的这一举动,自己则在 自己的笔记本中记录,大脑中进行思考.而老师却不关心自己的学生对这一举动做什么事.

好了,例子就是这样的,我们来分析一下这个例子跟观察者模式有个什么关系? 这个例子中,老师可以产生事件,学生观察着老师,而老师在产生事件之后咳嗽一下,通知所

有的学生,我刚才做了什么事,你们应该也需要做点自己的事情 而这就产生了几个概念,观察者,被观察者,事件,事件的处理与消费 被观察者中存在观察者的引用,即教师知道自己要通知的学生都有谁 被观察者在产生事件之后通知观察者,即教师产生事件之后通知每一位观察着自己的学生

**4.3 RxJava基本概念**

RxJava是对观察者模式的一种高级运用，或者说是一种升级，他把观察者模式具体化，更加明确了各个对象之间的关系

> Observable：可观察者、被观察者
>
> Observer：观察者
>
> Subscribe：订阅、事件

Observable和Observer通过subscribe()方法实现订阅关系，从而Observable可以在需要的时候发出事件来通知Observer。

RxJava泛型相关

> 自定义泛型接口：interface Observer
>
> 泛型类：class ImplObserver implements Observer
>
> 泛型方法： Observer call(T t)

泛型的作用域：

如果将泛型声明放在`泛型接口、泛型类`上，则该泛型在该类中就是确定的了，

如果将泛型声明在`方法`上，则该泛型只在该方法中有效，

如果泛型方法上声明的泛型类型和类或接口中声明的泛型一致，则会在该方法中隐藏类或接口上的泛型

```java
/**
*将泛型声明放在接口上
*/
public interface Observable<T>{
  
}
```

```java
public  static void main(){
	
}
```
