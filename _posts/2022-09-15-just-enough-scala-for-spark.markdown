---
layout: post
title: Just Enough Scala
tags: [frontpage, jekyll, blog]
image: '/images/posts/7.jpg'
---



My notes from the course https://github.com/deanwampler/JustEnoughScalaForSpark

1. Methods and Functions are different because Scala is both OOO and Functional



* Immutable vs Mutable. val is immutable. var is mutable. Highly recommend sticking with val.
* Types in scala are from Java JDK. Scala compiles to JVM byte code so I can use any Java library.

* So file is used from `import java.io.File` and I can call `new File("/directory")`
* If is an expression not statement in Scala. if condition return true or false and saves the results to a variable called `success`
* for loop - so list is Seq() and the syntax is like
* `for {x <- X}`
* so `yield` after the loop is done if the `if` statement is true
* idiom `collection.foreach(println)`
I have a few ways to write this
```
plays.foreach(println)

plays.foreach(str => println(str))

plays.foreach((str: String) => println(str))

plays.foreach(println(_))

plays.foreach {
  (str: String) => println(str)
}


* map vs flatMap

map returns 1 new record for each record in my collection

flatMap returns a collection of records for each record in my collection. These
collections are then flatten into 1 RDD.


*reduceByKey is like tabu
It takes two arguments. First argument is the break and the second argument is the integer. so
if i want to count i go
`collection.reduceByKey((first, second) => first + second)

*groupByKey is also tabu but retains the groups
Default uses the first tuple as the key

*equal to list is `column.isin(Seq():_*)` isin takes vararg not a List

*Scala Vector is cool because most operations are O(1)

*RDD.mapValues is good when I'm just changing the values part of the tuple so I pass in values and
I get a new (key,value) tuple


*Spark Dataframe API is convenient

