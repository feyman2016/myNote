## Very basics of the programming languages

#### 1. How to learn a new language?

Normally, this contains 3 steps:

- Syntax
- Type checking
- Evaluation





#### 2. Five Mainly Facets (Todo) 

You can learn a new programming languages from the five following facets:  

- Tools



#### 3. Generic rules in SML 

- Only **val**s, no **var**iables

  ```
  val a = 3;   (* bind value 3 to val a,a cannot be changed *)
  ```

- **functions** are unevaluated **values**

  functions are also values, they can be used where **vals** are used, they will be evaluated when they are called

- **Tuples** are syntactic sugars for **record**

  So the tuple is not necessary in ML, but it makes SML sweeter, so it's syntax sugar.

  ```
  {color: red, name: Rally} (* this is record, like dict in python ,or map in other *)

  (3,2,5)   (* int Tuples,just like in python or scala  *)
  {1:3,2:2,3:5} (* It is just the same as (3,2,5), but simpler *)
  ```

- **Pattern matching** everywhere

  Pattern matching is useful to substitute **if then else** , and helpful to write more concise code.

  ​

  **1 .**value binding is actually pattern matching, **a** is pattern , 3 is a value, they matches ,then the pattern a is bind to value 3.

  ```
  val a = 3;
  ```



2.   function evaluation uses pattern matching.

     ```
     fun add(a: int,b: int) =    (* add defines a function with type: int*int -> int  *)
     	a + b
     ```

     you can call the function like :

     ```
     add(1,3);  (* it will get 4, it's just like almost any other languages  *)
     ```

     you can also call it like:

     ```
     add (1,3) (* This is interesting, not so many languages support this, in scala , it's OK *)
     ```

     Normally, we will take fun **add** as a function that takes **two aguments** with type **int** and returns a **int**

     But, actually in SML , **every function has only one argument, which is a tuple**

     So fun **add** have the argument type of **(int*int)** , it's actually a **2 elements tuple** , when evaluate this,

     **(1,3) matches the pattern (a: int, b: int)** , then a is bind to 1, b is bind to 3 ,which extends the dynamic environments with two more value bindings, then comes the normal evaluation process.



​	Similarily, fun **add3** have a **3 elements tuple** , not 3 arguments.

```
	fun add3(a,b,c) = a + b + c;   
```

​	if a function return a tuple, it can be used as input for another func

```
	fun rotate(a,b) = (b,a);
	rotate(3,4);   (* it will get (4,3)  *)
	rotate( rotate(3,4) ) ;    (* it will get (3,4) *)
```

​	**Amazing ,hmm?**

​	

- **Static** environment and **dynamic** environment

  ​

