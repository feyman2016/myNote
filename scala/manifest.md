## scala高阶类型系统笔记

### 1.使用Manifest

​	scala的类型系统十分强大，对于函数或者方法，编译器期望知道返回值的类型，而当返回值类型不一定的时候，就会出现问题，因为scala必须根据运行时的数组生成不同的字节码指令。比如如下返回一个Array中首个元素的方法：

```
scala> def first[A](x: Array[A]) = Array(x(0))
<console>:11: error: No ClassTag available for A
       def first[A](x: Array[A]) = Array(x(0))
```

编译器在运行时并不知道返回值的类型，所以会报错。

​	scala提供了一个在运行时捕获类型的机制，即使用[A: scala.reflect.ClassTag]捕捉隐式参数implicit evidence$1: scala.reflect.ClassTag[A]，该隐式参数可以构造对应的运行时类型。

```
scala> def first[A: scala.reflect.ClassTag](x: Array[A]) = Array(x(0))
first: [A](x: Array[A])(implicit evidence$1: scala.reflect.ClassTag[A])Array[A]

scala> first(Array(1,2))
res1: Array[Int] = Array(1)

scala> first(Array("2123",2))
res2: Array[Any] = Array(2123)
```

​	注意，manifest虽然很强大，但是最好只在编译器无法推断出类型时使用，否则有可能演化成代码中的病毒，一处使用了manifest，其他地方也需要使用manifest。

### 2.捕捉类型约束

scala的类型推断器推断类型的时候，顺序是从左到右，请看如下例子：

```
scala> def foo[A](col:List[A])(f : A => Boolean) = null
foo: [A](col: List[A])(f: A => Boolean)Null

scala> foo(List("String"))(_.isEmpty)
res1: Null = null

scala> def bar[A](col: List[A], f: A => Boolean) = null
bar: [A](col: List[A], f: A => Boolean)Null

scala> bar(List("String"),_.isEmpty)
<console>:13: error: missing parameter type for expanded function ((x$1: <error>) => x$1.isEmpty)
       bar(List("String"),_.isEmpty)
                          ^

scala> bar[String](List("String"),_.isEmpty)
res3: Null = null

```

foo函数有两个参数列表，在具象化第一个参数列表的时候，类型推断器可以推断出A的类型，用于第二个参数列表中的匿名函数，而bar函数只有一个参数列表，因此类型推断器并不知道`_.isEmpty`中`_`的类型，从这里可见，函数的科里化（currying）除了常用的实现偏应用函数的作用，还有协助类型推断器推断类型的作用。

##  

