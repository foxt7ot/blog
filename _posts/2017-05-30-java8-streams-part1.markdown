---
layout: post
title:  "Java8, Streams, Part 1"
date:   2017-05-30 14:07:36 +0530
categories: jekyll update
comments: true
disqus_identifier: afe45239-e271-4aac-9a0c-d55df426928f
tags:
- technical
- java8
- streams
---
Disclaimer: If I have made some mistake, please correct me via comments.
New changes which are introduced in Java 8 were added with an intent to write a better, efficient and more readable code.
New core libraries are a key part of that, so in this series of blogs, The most important core library changes are 
focused around the Collections API and its new addition: streams, I will try my best to shed some light on them.

***Stream***
The Stream interface contains a series of functions that we’ll explore throughout this series of blogs, each of which
 corresponds to a common operation that we ought to perform on a Collection.
 A common pattern for Java developers when working with collections is to iterate over a collection, operating on each
  element in turn. For example, if we have a list of all office employees, so obviously there will be repeated names present in it like let's say 'John'
  Now our task is to write a code and find out how many times employee named 'John' is present in the list, We would write a code in somewhat below manner:
  ```
  int count=0;
  for(Employee employee : employees){
    if(employee.getFirstName().equals("John")){
        count++;
    }
  }
  return count;
  ```
Above example contains a lot of boilerplate code that needs to be written every time when you want to iterate over a collection. It’s also hard to write a parallel version of this for loop. You would need to rewrite every for loop individually in order to make them operate in parallel.
 
Finally, the code here doesn’t fluently convey the intent of the programmer. The boilerplate for loop structure obscures meaning; to understand anything we must read though the body of the loop. For a single for loop, doing this isn’t too bad, but when you have a large code base full of them it becomes a burden (especially with nested loops)

Looking under the covers a little bit, the for loop is actually syntactic sugar that wraps up the iteration and hides it. It’s worth taking a moment to look at what’s going on under the hood here. The first step in this process is a call to the iterator method, which creates a new Iterator object in order to control the iteration process. We call this **External iteration**. The iteration then proceeds by explicitly calling the hasNext and next methods on this Iterator
Below code demonstrates the pattern of method call happen over here
```$xslt
int count = 0;
Iterator<Employee> iterator = employees.iterator();
while(iterator.hasNext()) {
    Employee employee = iterator.next();
    if(employee.getFirstName().equals("John")){
        count++;
    }
}
```
External iteration has some negative issues associated with it, too. First, it becomes hard to abstract away the different behavioral operations that we’ll encounter later in this chapter. It is also an approach that is inherently serial in nature. The big-picture issue here is that using a for loop conflates what you are doing with how you are doing it.

An alternative approach, **Internal iteration**, is shown below. The first thing to notice is the call to stream(), which performs a similar role to the call to iterator() in the previous example. Instead of returning an Iterator to control the iteration, it returns the equivalent interface in the internal iteration world: Stream.
```$xslt
long count = employees.stream()
                       .filter(employee -> employee.getFirstName().equals("John"))
                       .count();
//stream count method retuns long
```
We can actually break this example into two simpler operations:

* Finding all the employees whose first name is 'John"
* Counting a list of all those above employees who fits above criteria

Both of these operations correspond to a method on the Stream interface. In order to find names equal to 'John', we filter the Stream. Filtering in this case means “keep only objects that pass a test.” The test is defined by a function, which returns either true or false depending on whether the name is equivalent to 'John'. Because we’re practicing functional programming when using the Streams API, we aren’t changing the contents of the Collection; we’re just declaring what the contents of the Stream will be. The count() method counts how many objects are in a given Stream.

**_What’s Actually Going On_**

When I wrote the previous example, I broke it up into two simpler operations: filtering and counting. You may think that this is pretty wasteful—when I wrote the for loop in Example 3-1, there was only one loop. It looks like you would need two here, as there are two operations. In fact, the library has been cleverly designed so that it iterates over the list of artists only once.
In Java, when you call a method it traditionally corresponds to the computer actually doing something; for example, System.out.println("Hello World"); prints output to your terminal. Some of the methods on Stream work a little bit differently. They are normal Java methods, but the Stream object returned isn’t a new collection—it’s a recipe for creating a new collection. So just think for a second about what the code in Example 3-4 does. Don’t worry if you get stuck—I’ll explain in a bit!
```$xslt
employees.stream()
          .filter(employee -> employee.getFirstName().equals("John"));
```
_Just a filter no collect step_

It actually doesn’t do very much at all—the call to filter builds up a Stream recipe, but there’s nothing to force this recipe to be used. Methods such as filter that build up the Stream recipe but don’t force a new value to be generated at the end are referred to as lazy. Methods such as count that generate a final value out of the Stream sequence are called eager.
The easiest way of seeing that is if we add in a println statement as part of the filter in order to print out the names. 
```$xslt
employees.stream()
          .filter(employee -> {
              System.out.println(employee.getLastName());
              return employee.getFirstName().equals("John");
          });
```
If we run this code, the program doesn’t print employee last name when it’s executed.

If we add the same printout to a stream that has a terminal step, such as the counting operation, then we will see the names of our artists printed out
```$xslt
long count = employees.stream()
                       .filter(employee -> {
                           System.out.println(employee.getLastName());
                           return employee.getFirstName().equals("John");
                       })
                       .count();
```
So if we ran above program it will print out all employees last names
<center><img src="/images/stream-output-1.png" /></center>
<p/>

It’s very easy to figure out whether an operation is eager or lazy: look at what it returns. If it gives you back a Stream, it’s lazy; if it gives you back another value or void, then it’s eager. This makes sense because the preferred way of using these methods is to form a sequence of lazy operations chained together and then to have a single eager operation at the end that generates your result. This is how our counting example operates, but it’s the simplest case: only two operations.

This whole approach is somewhat similar to the familiar builder pattern. In the builder pattern, there are a sequence of calls that set up properties or configuration, followed by a single call to a build method. The object being created isn’t created until the call to build occurs.
I’m sure you’re asking, “Why would we want to have the differentiator between lazy and eager options?” By waiting until we know more about what result and operations are needed, we can perform the computations more efficiently. A good example is finding the first number that is > 10. We don’t need to evaluate all the elements to figure this out—only enough to find our first match. It also means that we can string together lots of different operations over our collection and iterate over the collection only once.

To keep things memorable, I want to conclude first part till over here, in the next blog we are going to see common streams operations

**Common Stream Operations**

At this point, it’s worth just having a look back at some common Stream operations in order to get more of a feel of what’s available in the API. As I will cover only a few important examples, I recommend looking at the Javadoc for the new API to see what else is available.

**COLLECT(TOLIST())**
 

[github]: https://github.com/gh-pages
[jekyll]: https://jekyllrb.com/
