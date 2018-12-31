---
layout: post
title:  "Java8, Streams, Part 1"
date:   2017-06-15 16:00:36 +0530
categories: jekyll update
comments: true
disqus_identifier: afe45239-e271-4aac-9a0c-d55df426928f
tags:
- technical
- java8
- streams
---
**Common Stream Operations**

At this point, it’s worth just having a look at some common Stream operations in order to get more of a feel of what’s available in the API. As I will cover only a few important examples, I recommend looking at the Javadoc for the new API to see what else is available.

**COLLECT(TOLIST())**
>> collect(toList()) is an eager operation that generates a list from the values in a Stream.

The values in the Stream that are operated on are derived from the initial values and the recipe produced by the sequence of Stream calls. In fact, collect is a very general and powerful construct, and we’ll look into its other uses in some different blog. Here’s an example of this operation:
 ```$xslt
List<String> collected = Stream.of("Jone", "Jane", "Irham", "Suresh")  
                               .collect(Collectors.toList()); 
```

This example shows how collect(toList()) can be used to build a result list out of a Stream. It’s important to remember, as discussed in the previous section, that because many Stream functions are lazy, you do need to use an eager operation such as collect at the end of a sequence of chained method calls.

This example also shows the general format for all the examples in this section. It starts by taking a Stream from a List

**MAP**
>> If you’ve got a function that converts a value of one type into another, map lets you apply this function to a stream of values, producing another stream of the new values.

If you look back, you will notice fairly soon that you've been doing some kind of map operation for years already. Eg: You have collection which are in lowercase and you want to change all collection case to uppercase
And of course you mind have ended up writing trivial code like below

```$xslt
List<String> collected = new ArrayList<>();
for (String string : asList("Jone", "Jane", "Irham", "Suresh") {
    String uppercaseString = string.toUpperCase();
    collected.add(uppercaseString);
}
```
Above example can be converted with help of stream map function with much less code
```$xslt
List<String> collected = Stream.of("Jone", "Jane", "Irham", "Suresh")
                               .map(String::toUpperCase)  
                               .collect(toList());
```
**Filter**
>>Any time you’re looping over some data and checking each element, you might want to think about using the new filter method on Stream.
We have already seen a filter example, request you to have a look at part 1 of this blog.

**FlatMap**
>>flatMap lets you replace a value with a Stream and concatenates all the streams together.

Above we have already seen a map operation, which replaces values in a stream with new value. Sometimes you want a variant of map in which you produce a new Stream object as the replacement. Frequently you don’t want to end up with a stream of streams, though, and this is where flatMap comes in handy.
Let’s look at a simple example. We’ve got a Stream of lists of numbers, and we want all the numbers from these in sequences.
```$xslt
List<Integer> together = Stream.of(asList(1, 2), asList(3, 4))
                               .flatMap(numbers -> numbers.stream())
                               .collect(toList());
```
In each case, we replace the List with a Stream using the stream method, and flatMap does the rest. Its associated functional interface is the same as map’s—the Function—but its return type is restricted to streams and not any value.

**Max and Min**

A pretty common operation that we might want to perform on streams is finding the maximum or minimum element. Fortunately, this case is very well covered by the max and min operations that are provided by the Streams API
```$xslt
employees.stream()
         .min(Comparator.comparing(fName->fName.getLastName().length()))
         .get();
```
In order to inform the Stream that we’re using the length of the string, we give it a Comparator. Conveniently, Java 8 has added a static method called comparing that lets us build a comparator using keys. Previously, we always encountered an ugly pattern in which we had to write code that got a field out of both the objects being compared, then compare these field values. Now, to get the same element out of both elements being compared, we just provide a getter function for the value. In this case we’ll use length, which is a getter function in disguise.

>> It’s worth reflecting on the comparing method for a moment. This is actually a function that takes a function and returns a function. Pretty meta, I know, but also incredibly useful. At any point in the past, this method could have been added to the Java standard library, but the poor readability and verbosity issues surrounding anonymous inner classes would have made it impractical. Now, with lambda expressions, it’s convenient and concise.

It’s now possible for max to be called on an empty Stream so that it returns what’s known as an Optional value. An Optional value is a bit like an alien: it represents a value that may exist, or may not. If our Stream is empty, then it won’t exist; if it’s not empty, then it will. Let’s not worry about the details of Optional for the moment, since we’ll be discussing it in detail in another blog. The only thing to remember is that we can pull out the value by calling the get method.

**Reduce**

Use the reduce operation when you’ve got a collection of values and you want to generate a single result. In earlier examples, we used the count, min, and max methods, which are all in the standard library because they are common use cases. All of these are forms of reduction.

Let’s demonstrate the reduce operation by adding up streams of numbers. We start with a count of 0—the count of an empty Stream—and fold together each element with an accumulator, adding the element to the accumulator at every step. When we reach the final Stream element, our accumulator has the sum of all the elements.

```$xslt
int count = Stream.of(1, 2, 3)
                  .reduce(0, (acc, element) -> acc + element);
System.out.println(count); //count=6
```

The lambda expression, known as a reducer, performs the summing and takes two arguments. acc is the accumulator and holds the current sum. It is also passed in the current element in the Stream.

The lambda expression returns the new acc value, which is the previous acc added to the current element. The type of the reducer is a BinaryOperator

Let’s have a look at the equivalent imperative below Java code, so we can see how the functional and imperative versions match up.

```$xslt
int acc = 0;
for (Integer element : asList(1, 2, 3)) {
    acc = acc + element;
}
```
In the imperative version, we can see that the accumulator is a variable we update on every loop iteration. We also update it by adding the element. The loop is external to the collection and all updates to the variable are managed manually.
