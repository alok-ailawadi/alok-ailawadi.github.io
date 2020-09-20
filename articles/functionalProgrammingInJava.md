<!-----
NEW: Check the "Suppress top comment" option to remove this info from the output.

Conversion time: 1.228 seconds.


# Table of Contents 
`- [Introduction](#introduction) 
- [Methodology](#methodology) 
- [Concepts](#concepts) 
  * [Language features](#language-features) 
- [Function Mathematical definition](#function-mathematical-definition) 
- [Some concepts](#some-concepts) 
  * [Function](#function) 
  * [Side effect ](#side-effect-) 
  * [Immutable data](#immutable-data) 
  * [Functions as first class citizens](#functions-as-first-class-citizens) 
  * [Higher order function](#higher-order-function) 
- [Functions in Java](#functions-in-java) 
- [Java function recipes](#java-function-recipes) 
  * [Supplier ](#supplier-) 
  * [Consumer](#consumer) 
  * [Function](#function) 
  * [Functional Composition](#functional-composition) 
  * [Predicate](#predicate) 
  * [Predicate composition ](#predicate-composition-) 

## Introduction

If you are an experienced java developer, or you have learnt coding using Java, then learning functional programming is not natural. Because functional programming in Java is not natural.

Java 8 onwards have introduced many features that help you code in declarative style. You may be using streams, map, reduce, collect and many more. But still won’t be knowing if this is truly functional.

This cook book attempts to understand functional programming, how to do it in java, more importantly how to identify if the code written by you or your team member is actually functional or just an imposter.


## Methodology

There are many articles teaching functional programming in java. But as I said earlier functions in java are not natural and if you dive straight to functional programming in java you will understand little.

So, here I will introduce concepts using a simple language like javascript. 

Then I will take you to the Java world to understand how and importantly why it is implemented like it is in Java.

After that we will jointly walk through certain examples to check whether the implementation is functional or just declarative

This will be as much learning for me as it will be you

So let's just begin




## Concepts

A functional code is truly functional if **it has no side-effects**. Absence of side-effects is the only qualifying feature. What it means



*   It does not rely on data outside current function
*   It does not change the data outside the current function.

Rest all is just language features, programming techniques and advantages of functional programming


### Language features

To support functional programming



1. Immutable data
2. First class  functions
3. Tail call optimisation 

Programming techniques
1. Map
2. Reduce
3. Pipelining
4. Recursing 
5. Currying
6. Higher order functions

Advantages
1. Parallelization
2. Lazy evaluation
3. determinism


## Function Mathematical definition

In mathematical terms, a function is a **binary relation** between **two sets (input set and output set)**, where **each input **is **related** to** exactly one** **output**.

It is denoted as:

*__f(x) = y__*
Where x is input set
And y is output set


## Some concepts


### Function

Just like mathematical functions, takes inputs and provides  output based on the input value.


### Side effect 

If during an execution of a function, it changes the state of the environment such that calling the same function again and again with the same inputs creates different outputs, then the function/method has side effects. Consider the following code

Example with side effect


```java
{

    int[] arr = new int[] {2, 4, 8};

    public int[] nonFunctionalSum(int number){
   	 for(int i = 0; i < arr.length ; i++){
   		 arr[i] = arr[i] + number;
   	 }
   	 return arr;
    }
```


In example above, clearly calling the same method `nonFunctionalSum` multiple times will result in a different array as output as the method is changing the state of the variable( `int[] arr` ) which is defined outside of the scope of the method.

Now consider the same functionality in side effect free using java 8 streams


```java
public int[] functionalSum(int number){
   	 return Arrays.stream(arr).map(i->i + number).toArray();
    }
```


Not only does this look cleaner but regardless of how many times this method is called with the same int parameter, the result will always be the same as this method is not changing the state of any data which is not in the scope of the method.


### Immutable data
An immutable data is the data structure whose state can not be modified after it is created. 

*__Why is it important?__*
Functional programming is side effect free, having a mutable data in a function introduces potential side effects as we have seen above. 
Also if the data is immutable, it cannot be shared for modification between threads and will help in safe parallel processing.


### Functions as first class citizens

In a programming language functions are first class citizens if functions can be 



*   ***Stored*** in variable, object or array
*   Passed as an ***argument*** to a function.
*   Returned as a ***return value*** from a function

*Javascript* treats functions as first class citizens.
Following example in javascript describes all three properties of functions.

__Function stored in a variable__
```javascript
function sum(num1, num2)
{
   return num1 + num2;
}
let fn = sum; // Assigned to a variable
console.log(fn(2,4));
```


**Function stored in an object**


```javascript
function sum(num1, num2)
{
   return num1 + num2;
}
let obj = {additionResult:sum}; //function on object
console.log(obj.additionResult(2,4));
```

**Function as return value**

```javascript
function abc(){
   var num1 = 2;
   var num2 = 4;
   function add() {
       return num1 + num2;
   };
   return add; // returning function as
}
let callabc = abc(); //callabc now has reference to add()
console.log(callabc());
```

Incidentally, in this example we are also seeing an example of ***closure***.
For more information on lexical scope and closures see this excellent [article](https://maximdenisov.gitbooks.io/you-don-t-know-js/content/scope_&_closures/intro.html).

**Function as argument in a functions**

```javascript
function abc(){
   var num1 = 2;
   var num2 = 4;
   function add() {
       return num1 + num2;
   };
   return add; // returning function as
}
//function as parameter
function xyz(fn) {
   console.log(fn());
}
xyz(callabc);
xyz(abc);

Output: 
6
[Function: add]
```



### Higher order function

A higher order function is a function that takes function as an argument or returns function. We have seen that in javascript in example above.


## Functions in Java

Java has methods. Technically one can only pass objects (class instance) or primitives in method parameters. Also, methods can return either object, primitive and void.

Functions are not the first class citizens in Java language. 

The problem is java method can have objects (instance of class or interface) and primitives only as method parameters and we need to pass function in the method.

Java 8 came up with a concept of ‘functional interface’. A functional interface will have  exactly one abstract method(which needs to be implemented in the instance). It may have one or more default methods.


## Java function recipes

Before we start on recipes, let's explore the java.util.function_ _package.

Important interfaces are: 

### Supplier 

Represents supplier of results. Its functional method is _**get()**_. 
It accepts no argument and supplies object of type T.
One of the primary usage of Supplier is **_deferred execution. _**

**Consider this example**


```java
Supplier<LocalDate> dateSupplier = LocalDate::now;
LocalDate currentDate = LocalDate.now();
LocalDate currentDate2 = dateSupplier.get();
```


Other prominent use of Supplier is to refine the factory pattern. The traditional factory pattern implementation break the open-close principal (OCP), whenever new type is to be added for factory to return it requires change in factory class. 

With Java 8 implementation using suppliers, we can directly add a new type with its supplier in the factory dynamically. 

**Java 8 factory**


```java
public class ShapeFactory {
   private static Map<String , Supplier<? extends Shape>> shapes = new HashMap<>();
   static {
       shapes.put("CIRCLE", Circle::new);
       shapes.put("SQUARE", Square::new);
       shapes.put("UNKNOWN", UnkownShape::new);
   }

   public static void registerSupplier(String shapeType, Supplier<? extends Shape> supplier){
       shapes.put(shapeType, supplier);
   }

   /**
    *  Factory get method using supplier instead of traditional if then else construct used earlier
    */
   public static Shape getShape(String shapeType){
        Optional<Supplier<? extends Shape>> opt =Optional.ofNullable(shapes.get(shapeType));
        if(opt.isPresent()){
            return opt.get().get();
        }
        return shapes.get("UNKNOWN").get();
   }

   public static void main(String[] args) {

       System.out.println(ShapeFactory.getShape("CIRCLE").getDescription());
       /*messy ball of mud  is not in the list of know shapes */
       System.out.println(ShapeFactory.getShape("BALL-OF-MUD").getDescription());
   }
}

Output:

This is circle
This is an unknown shape
```



### Consumer

It is a Functional interface and hence eligible for assignment target for a lambda expression or method reference.
Represents an operation that accepts a single argument and produces no result.

*The consumer’s operation generally have side-effect e.g. changing state while persisting in DB or an API call*

Its functional method is **_accept(T t)._**

Example


```java
Consumer<String> = s->System.out.println(s); //Consumer that accepts a String object
s.accept("hello world"); // prints hello world
```


An interesting case is composition of consumers using the default method **_andThen()_**


```java
default Consumer<T> andThen(Consumer<? super T> after)
```


**Example**


```java
/*
    This method now has side-effect as it changes the list
*/
public static Consumer<List<String>> normalizeNames = list -> {
   for(int i = 0 ; i < list.size()  ; i++){

       list.set(i, list.get(i).substring(0,1).toUpperCase()
               + list.get(i).substring(1,list.get(i).length()).toLowerCase());
   }
};
public static Consumer<List<String>> print = s-> System.out.println(s);

public static void main(String[] args) {
   List<String> names = Arrays.asList("ashok", "Kishore", "NAKUL", "moHit");
   normalizeNames.andThen(print).accept(names);
}

Output:
[Ashok, Kishore, Nakul, Mohit]
```



### Function

Represents a functional interface that accepts one argument and produces a result.

Its abstract method name is **_apply(). _**

The Interface is defined as 


```java
Interface Function<T,R>
T-> type of input to the function
R->type of the result of the function
```



### Functional Composition

Functional composition is a design pattern which helps in combining two or more functions as per the requirement.

There are two methods provided for the purpose

```java
default <V> Function<T, V> andThen(Function< ?  super R, ? extends V> after)
```


Returns the composed function that first applies __this__ function (i.e. Function&lt;T,R>) and then applies the __after__function (that is why the after function’s input type is &lt;? super R>) 

V-> the return type of the composed function 
R-> the return type of the _this _function.
T-> the input type of _this_ function. 



Example 


```java
public class FunctionExample {

   static Function<Integer, Double> halfIt = value -> value / 2.0;

   static Function<Integer, Integer> tripleIt = value -> value * 3;

   static Function<Integer, Double> tripleAndhalf = tripleIt.andThen(halfIt);

   public static void main(String[] args) {

       System.out.println(tripleIt.apply(5)); // 15
       /******* and then ********/
       System.out.println(halfIt.apply(5 * 3)); // 15 / 2.0 = 7.5

       /***** is equivalent to ******/
       System.out.println(tripleAndhalf.apply(5)); // 7.5

       /***** is equivalent to ****/
       System.out.println(tripleIt.andThen(halfIt).apply(5));// 7.5

   }
}
```


The other method is _compose()_.


```java
default <V> Function<V, R> compose(Function<? super V, ? extends V> before)
```


Returns a composed function that first applies __before__function to its input and then the __this__ function to the result.

This is the reason biFunction can not have compose method because result of before will have one output and biFunction expects two inputs.

Continuing with above example


```java
static Function<Integer, Double> composeIt = halfIt.compose(tripleIt);

/***** is equivalent to ****/
System.out.println(composeIt.apply(5));

/***** is equivalent to ****/
System.out.println(halfIt.compose(tripleIt).apply(5));
```



### Predicate

Represents the mathematical predicate(boolean-valued function) of one argument.

The functional interface abstract method is **_test(). _**


### Predicate composition 

Predicate interface provides intuitive methods, **_and()_** and **_or()_** to join two or more predicates to test multiple conditions


```java
default Predicate<T> and(Predicate<? super T> other)

default Predicate<T> or(Predicate<? super T> other)
```


**Example**

**Supporting classes**


```java
enum Department{

   SALES, FINANCE, TECH, MARKETING, LEGAL;

}

enum Designation {

   MANAGER, LEAD, DIRECTOR, PRESIDENT;

}

class Employee{

   Employee(String name, Department department, Designation designation){
       this.name = name;
       this.department = department;
       this.designation = designation;
   }

   String name;
   Department department;
   Designation designation;
}
```


**Predicate example with compostion**


```java
public class PredicateExample {

   static Predicate<Employee> isFinanceDepartment = emp -> emp.department==Department.FINANCE;
   static Predicate<Employee> isSalesDepartmet = emp -> emp.department==Department.SALES;
   static Predicate<Employee> isDirector = emp -> emp.designation==Designation.DIRECTOR;

   public static void main(String[] args) {

       Employee emp1 = new Employee("Ashok", Department.FINANCE, Designation.DIRECTOR);
       Employee emp2 = new Employee("Kishor", Department.SALES, Designation.MANAGER);
       Employee emp3 = new Employee("Shah", Department.SALES, Designation.DIRECTOR);

       if(isFinanceDepartment.test(emp1)){
           System.out.println(emp1.name + " is from sales");
       }

       if(isFinanceDepartment.test(emp2)){
           System.out.println(emp1.name);
       }else{
           System.out.println("Not from finance hence not allowed entry");
       }

       /****** compostion with and *******/
       if(isFinanceDepartment.and(isDirector).test(emp1)){
           System.out.println(emp1.name + " is from Finance and is director");
       }
         /****** compostion with or *******/
       if(isFinanceDepartment.or(isSalesDepartmet).test(emp3)){
           System.out.println(emp3.name + " is allowed entry to conference");
       }
   }
}
```

