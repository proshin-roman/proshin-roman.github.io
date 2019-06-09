---
layout: post
title: "Make it final, Luke!"
tags: [oop, java, final]
image: /img/posts/iron-man.jpg
description: Explain, why the "final" keyword is a "must have" in Java
---

Just a few days ago I was reviewing a pull request of one of my colleagues. He added a new class with multiple fields, but all they weren’t final. And I wrote a comment “make it final, please”. And that’s why I decided to write this post.

{% include image.html url="/img/posts/iron-man.jpg" description="Yeaaah, that’s how instances of a final class feel" %}

## Make class `final`

When you make a class `final`, nobody can extend it. Which means nobody can modify the data of the class (its internal state), neither break its internal logic by overriding methods of the class. So, you can use methods of the class as in constructors of the class, as in other methods with no risk that the called method can occasionally change its behavior.

Further reading here: *Effective Java, 3rd Edition*, Joshua Bloch - Item 17, point 2

## Make field `final`

Final fields can only be initialized once, and they cannot be changed then - they always point to the same object in memory. What does it give you? It makes the class immutable - once created, the state of the class cannot be changed. You can rely on it, you can use it in concurrent threads, etc. because nobody can change the state of the object in between consequent calls.

Further reading here: *Effective Java, 3rd Edition*, Joshua Bloch - Item 17, point 3

## Make method `final`

This is only required if the class itself is not `final`. Always prefer making the whole class `final`, and only in rare cases when you definitely know that the class may not be final - make `final` methods of the class, as many as you can. Ideally, all methods should be `final`. 

## Make method parameter `final`

Final method parameters are not about protecting from external “intruders”, but rather about preventing your own temptation. The temptation to overwrite the reference of the input parameter. That’s the classic example:

```java
class Foo {
  public final void bar(String name) {
    // one hundred lines...
    name = "Vasya was there";
    // ...one more hundred lines...
    System.out.println("My name is " + name); // surprise!
  }
}
```

If the input parameter not final, you never can assume that the parameter is still in its initial state.

Another thing that the fact that you need to overwrite your input parameter signals you that your code should be refactored. Just make the parameter `final` and refactor the method so that it still compiles.

## Make variable `final`

This case is very similar to the input parameters. Whenever you define a variable in the method - make it `final`. Non-final variables aren’t reliable, you can never be sure it still contains the original value.
And again - if the code of the method stops you from making a variable `final` - refactor it. It already smells.
A funny fact - some IDEs already understand how dangerous it might be to have a non-final variable in the code. And they highlight such variables differently comparing to final ones. Doesn’t it prove that final variables are better?

## Get into a habit to make everything final
If you agree with me and think the same way that `final` is a "must have" keyword in Java - you can immediately start using it. And your IDE can help you. I know that at least IntelliJ IDEA can add `final` keyword to parameters and variables which are generated automatically - by using the "Refactor" feature. You can enable this feature in "Settings" - just go to "Editor" -> "Code style" -> "Java" -> "Code generation" -> "Final modifier".

## Disadvantages?

Yes, there is a little disadvantage related to an issue that if you publish your final class in a library, users of the class cannot change its behavior. That's why it's preferred to [use interfaces](https://proshin.org/2019-05-12-use-interfaces-luke/). Usually, you should have an interface and many implementations of this interface, where every implementation is `final`. On the one hand, the interface gives flexibility as the user can always either use another implementation provided by you or simply use his own custom implementation. And on the other hand, your classes are protected because they are final.  

---

Do you have any thoughts about the topic - feel free to write them in comments. I appreciate any feedback!

Thanks for reading!
