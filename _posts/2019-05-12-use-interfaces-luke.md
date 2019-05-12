---
layout: post
title: Use interfaces, Luke
description: Explaining why interfaces should be preferred to using specific implementation
---

In this blog post, I want to explain why interfaces so important when you want to make your application well-structured, maintainable and testable.

But before, let me refresh some terms.

## What is a module?

A module is a part of a program. That's it. 

Though, every programming language defines its own meaning of this word. In Java world, a module is either a [package](https://docs.oracle.com/javase/specs/jls/se7/html/jls-7.html) or Maven module. But in this post, I treat module as an abstract thing which might have a different size depending on the level of abstractions. 
It might be a set of applications or services which belong to the same domain area, as well as a specific class. It all depends on the level of abstractions. That's why I don't want to use the word "class" and prefer using the word "component" in the further definitions of **coupling** and **cohesion**.

## What is coupling?

**Coupling** defines how two separate modules are dependent on each other. How many components of one module uses components from another one. And the more such dependencies exist - the more concrete is the whole app. 
You face this problem when you start refactoring one module because many changes inside of the module will trigger cascade changes in another - separate - modules. And that's bad. Every module should be as independent of others as it's possible. **The lower coupling - the better**.

## What is cohesion?

**Cohesion** has the opposite meaning of the coupling. 
Cohesion defines how strongly components of a single module belong to each other. 
The module may not include components which belong to totally different domains. Instead, all components of the module must belong to as little subject area as it's possible. **The higher cohesion - the better**.

------

So, the ideal code must have low coupling and high cohesion. How to reach that? That's where interfaces come into play. The goal of interfaces is to decrease the coupling! Let's see how they do it in a specific example.

## How interfaces help to reach "low coupling & high cohesion"

Let's look at a library called [finapi-java-client](https://github.com/proshin-roman/finapi-java-client). It contains many classes and one of them is [FpEndpoint](https://github.com/proshin-roman/finapi-java-client/blob/v0.1.70/src/main/java/org/proshin/finapi/endpoint/FpEndpoint.java) - basically, it's an abstraction over the Apache [HttpClient](https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/client/HttpClient.html). Let's see what benefits it gives using this interface.

If `FpEndpoint` interface didn't exist, the Apache `HttpClient` class has to be used directly. Ok, it's already an interface, however, once we decide to use another HTTP client implementation - we are in trouble because all usages of the HttpClient must be adjusted. And what if it's hundreds or even thousands of files? 

At the same time, classes which use this HTTP client shouldn't be interested in what specific implementation or library they use. They just want to somehow execute HTTP requests. The whole this problem can be named as **coupled components**, or **high coupling**, which means two separate modules - business logic and HTTP layer - have high coupling and changes in one part triggers cascade changes in another part. 

And then we bring this `FpEndpoint` interface, which is basically a contract between two sides. The first side - all classes which want to make HTTP requests using the interface. And another one - all possible implementations of this interface. 

The interface as a contact defines: "Give me this structure as an input, I will somehow do the HTTP request and return you that structure as a response". That's it! That's how we decouple our modules. 

Having an interface in between, you can, for example, change the HTTP library as often as you want, or even use different implementations depending on some circumstances. And this is what we call **low coupling**.

Now about **cohesion**. Because our `FpEndpoint` interface defines a contract between two parts, it can restrict the access to the functionality of the underlying HTTP client. Which means clients of the interface simply cannot, for example, modify timeouts of the client. In case they know something about such requirements to the HTTP connection, they have to force changes in the interfaces. And then again - they don't have explicit access to the implementation. They can only tell somehow to the implementation that something should be done on a lower level. And the implementation takes care of it. It means all our implementation-specific classes are grouped in the same places, in the same module. And they all are about the specific domain - HTTP layer.

> To be honest, this `FpEndpoint` interface should be refactored yet because it provides too many methods for doing almost the same job - make an HTTP request. Ideally, it should have exactly one method called **request** or **execute** and all the details about the HTTP request should be defined via the input interface.

------

I hope, after reading this article you prefer using an interface rather than an implementation.

As well, you will find more explanations on these links:

- Yegor Bugayenko *Elegant Objects* Amazon, 2017; Section 2.3
- Joshua Bloch *Effective Java 3rd Edition* Pearson Education Inc. 2018; Section 20
