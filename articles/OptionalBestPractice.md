# Optional Best Practices

Optional was introduced in Java8 to solve the dreaded _null reference_ problem.\
If used correctly it solves the issue to a large extent.\
This article attempts to give few of the best practices of using the Optional.
  
## Practice 1: Assignment
Do not assign null to an optional instance. Optional is a _container_ which _contains_ an instance of a class which may be _absent_. Optional itself should not be null
```java
public Optional<Milk> getMilk(Milktype milktype){
        //logic for gtting milk
        //if milk not found then
        Optional<Milk> maybeMilk = null;
        return maybeMilk;
} 
``` 
The above code is incorrect.

Correct code is to assign Optional.empty()

```java
    Optional<Milk> maybeMilk = Otional.empty(); // correct way
    return maybeMilk;
```

## Practice 2: correctly use _get()_
Don't use Optional.get() before ensuring that Optional does indeed have value. So:\
A. first check _presence of value_ using _ifPresent()_.\
B. Then use _get()_.\
**Note**: As much as possible don't use _get()_. 

## Practice 3: use of _orElse()_
If the value is absent, return the _**already constructed/computed default**_ value in _orElse()_.

So Avoid
```java
Milk standardMilk = new Milk(MilkType.SKIMMED);
if maybeMilk.isPresent)(){
    return maybeMilk.get();
}else{
    return standardMilk;
}
```

Prefer
```java
Milk standardMilk = new Milk(MilkType.SKIMMED);
maybeMilk.orElse(standardMilk);
```

Note that orElse() has performance impact as regardless of Optional has value or not, the orElse block will be evaluated.
If you have implemented a cache and implementation like below
```java
public class Cache{
    public static Map<String, Optional<User>> userCache;
}

public User getUserDetails(String userId){
    Optional<user> maybeUser = Cache.userCache.get(userId);
    maybeUser.orElse(userRepo.get(userId));
}
```
In case above the userRepo call will be made regardless of value present in cache or not.
It is always advisable to use _orElseGet()_ .
**Note**: map<String, Optional<T>> itself is a bad practice as we will see later in article. 

## Practice 4: Do not declare class fields as Optional
Optional is not serializable. Optional is not intended to be used as the property of a java bean or as a persistent type property.

So. **Don't use Optional as: **

1. field/property of a class
2. argument to to constructor
3. argument to setter method

It is debatable if the Optional can be passed as an argument to a method. 
Please see [this](https://dzone.com/articles/optional-method-parameters)  discussion and decide for yourself.

## Practice 5: Do not mix Optional and Collection

1. If returning a collection, do not bother about _Optional<Collection>_. If collection does not have any value, return empty collection.
2. Do not create a collection of optinal e.g. map<String, Optional<User>> is useless as in case of absence of value, the map will not have key.
It is better to use collection constructs like rather  than using Optionals.
```java
User defaultUser = new User(Status.UNINITIALIZED);
HashMap<String, User> userMap= ....//code to initialize
userMap.getOrDefault(defaultUser);

```

## Practice 6 : Make generous use of Optional stream like methods
See [Optionals](http://bootcamptech.com/functional-programming-in-java-2-introducing-optional/) for more details.


