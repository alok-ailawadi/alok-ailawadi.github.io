# Java stream - Simplified part 1 .

Some important points about Java streams

1. Stream is a ***sequence*** of elements supporting sequential or parallel **aggregations**.
2. Stream operations are composed into a **stream pipeline**. 
3. Stream pipeline consists of a :
    * Stream source (array, collection, generator function, I/O channel etc.)
    * Zero or more *intermediate* operations.
    * *Terminal* operation.
4. Computations are *lazy*. Computation on source is performed only when the the *terminal* operation is inititiaed.
5. *Intermediate* operations transform a stream to another stream.
6. The *terminal* operation may produce side-effects (count(), forEach(consumer))
7. Streams are ***lazy*** computation of source data is only performed when the the terminal operation is initiated.
8. The source elements are comsumef only as needed.

# Stream Vs Collection
They both look similar but have different goals.

Collections- are primmarily concerned about *efficient management* and *access* to the elements in a collection.

Streams- deal with *source*
1. You can't directly access and manipulate the elements like in collection.
2. It is all about *declaratively* describing the source and computational operations that will be performed in aggregate on that source.
3. A stream pipeline can be viewed as a *query* on the stream source.

# Stream operations 
Most stream operations accept parameters that *describe user-defined **behaviour***.
The parameter to stream operation must be :
- non-interfering (should not change the source elements)
- stateless (the result should not depend on anything that might have changed due to stream pipeline operations).

in short the parameter to stream operations are ***functions***.
And in java functions are represented as instances of *Functional instances* such as ***Function, Consumer, Producer, Predicate***.  
 

e.g. *filter()* operation takes an instance of *Predicate*.  
*map()* takes on instance of *Producer*.  
*forEach()* takes an instance of *Consumer*.  
So stream operations can accept either ***lambda expression*** or ***method reference*** as the target types of those Functional interfaces.

