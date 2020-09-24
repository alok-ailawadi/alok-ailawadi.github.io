# Java Optional and its best practices

**Tony Hoare** introduced '***null references***' in 1964 in ALGOL. And in 2009 apologised for it calling it 
***billion dollar mistake***. 

***Why?***\
Because it is responsible for *ability to compile successfully and then crash on runtime*, *memory leaks*, *security flaws*.
For more than 50 years softwares are failing in production because of *null references*.

An attempt was made to get over it in java8 by introducing ***Optional***. 

##What is Optional

Optional is a container.\
It contains object of a defined type.\
It may be empty.

Optional is like a milk bottle , it may contain milk or it may not contain milk. 
But bottle (container) is always there.

***Naming convention***\
The code is readable if we name the Optional as 'Maybe' of the class which it a container of.\
e.g. \
For milk bottle example 
```java
Optinal<Milk> mayBeMilk ;
```

Calling methods on mayBe optional becomes more readable.
e.g. 
```java
maybeMilk.ifPresent();
maybeMilk.orElse(new Milk("Status.ORDERED"));
```


## Optional Creation

A. create an Empty Optional
```java
Optional<Milk> mayBeMilk = Optional.empty();
``` 
B. Create with an instance of milk
```java
Optional<Milk> mayBeMilk = Optional.of(new Milk());
```
Note: In method *of()* if one tries to pass null it will result with *NullPointerException*

C. Create with null
```java
Optional<Milk> mayBeMilk = Optional.ofNullable(null);
```

## Optional methods
### ifPresent()
Do something with milk object if Optional is present
```java
mayBeMilk.ifPresent(milk->System.out.println("drink" + milk.milkType));
```
method signature
```java
void ifPresent(Comsumer<? super T> consumer)
```

### isPresent()
Check if the Optional is not empty
```java
if(mayBeMilk.isPresent()){
    //do Something 
}
```
method signature
```java
boolean isPresent()
```
### orElse()
Return Optional value or default value if the optional is empty
In this example if there is no milk , get skimmed milk
```java
Milk orderedMilkForDrinkning = mayBeMilk.orElse(new Milk(MilkType.SKIMMED));
```
Method Signature
```java
T orElse(T other)
```
### orElseGet()
Same as above but now instead of creating a new instance of object, get it from a *Supplier* (e.g. factory implementation using supplier)
```java
Supplier<Milk> milkman = ()->Milk(MilkType.SKIMMED);

Milk orderedMilkForDrinkning = mayBeMilk.orElseGet(milkman);

```
**Note**: One major difference in *orElse()* and *orElseGet()* is the evaluation of code in *orElse*.. block.
In case of *orElse()* the code is evaluated even if the Optional is not empty.\
But in case of *orElseGet()* the the code is evaluated only if the Optional is empty.\
This has an important performance impact.

##Stream like methods for Optional.
If you know Java streams, you know *filter()*, *map()* and *flatmap()* . Optionl has same method with 
roughly similar meaning for these methods.

### filter
```java
Optional<T> filter(Predicate<? super T> predicate)
```
Suppose due to lactose intolerance you can only have Soy milk. This can be represented using Optional as
```java
mayBeMilk.filter(milk->milk.milkType==MilkType.SOY)
         .ifPresent(milk->System.out.println("Consume "+ Milk.milkType));
```
In the code above the filter will be applied only if the mayBeMilk Optional is not empty.

### map
```java
<U>Optional<U> map(Function<? super T, ? extends U> other)
```
map is used to **transform** Optional of one type to optional of another type.

Milk is used for creating cheese, the calorific value of cheese depends on the type of milk used in creating cheese 
(encapsulated in cheese's constructor).

You want to consume cheese only if its calorific value per 100 gm is less than 150KCal.
```java
mayBeMilk.map(m->MilkProcessor.getCheese(m))
                .filter(cheese->cheese.calorificValue < 150)
                .ifPresent(cheese -> System.out.println("Eat it please"));
```

### flatMap
```java
<U>Optional <U> flatmap(<? super T , Optional<U>> mapper)
```
For explaining flatmap operation, I will have to elaborate more of *Milk* class.
Milk may be fortified with Vitamin D or not, thus

```java
class Milk{
    MilkTtype milkTtype;
    String orderStatus;
    Optional<VitD> mayBeVitD;
}
```

Your child needs Vitamin D fortified milk to grow strong, obviously.
So you would enquire it by using map as described above
```java
        mayBeMilk.map(m->m.mayBevitD)
                .filter(vitD->vitD.strength < 10000)
                .ifPresent(cheese -> System.out.println("Drink it please"));
```

but above will not compile.\
Because map on optional returns another Optional so the result of *map(m->m.mayBevitD*) will be *Optional<Optional<VitD>>*
and not *Optional<VitD>* as we require.

how do we get Optional<VitD>, flatmap to the rescue. it will flatmap in Optional flattens Optional<Optional> to Optional.
```java
mayBeMilk.flatMap(m->m.mayBevitD)
                .filter(vitD->vitD.strength < 10000)
                .ifPresent(cheese -> System.out.println("Drink it please"));
``` 

##Summary
 Optional serves two puposes
 1. As you see from the examples above , Optional takes *NullPointerException* out of equation. It also forces the 
 programmer to make provisions for *absence of value*.
 2. The code is more readable. Much better than code blocks of *if(milk!=null)* 
