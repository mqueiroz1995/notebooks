# Dependency Injection <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Shorter Introduction](#shorter-introduction)
- [Longer Introduction](#longer-introduction)
- [Types of Dependency Injection](#types-of-dependency-injection)
- [References](#references)

## Shorter Introduction

>"'Dependency Injection' is a 25-dollar term for a 5-cent concept. That's not to say that it's a bad term... and it's a good tool. But the top articles on Google focus on bells and whistles at the expense of the basic concept. I figured I should say something, well, simpler." <br><br> - James Shore

Dependency injection is a programming technique that makes a class independent of its dependencies. It achieves that by decoupling the usage of an object from its creation.

The goal is to improve the reusability of your code. By doing that, you reduce the frequency with which you need to change a class. That enables you to replace dependencies without changing the class that uses them, which is really helpful when testing your application. It also reduces the risk that you have to change a class just because one of its dependencies changed.

## Longer Introduction

> Extracted from: [What is Dependency Injection? - Adam Warski, SoftwareMill](https://blog.softwaremill.com/what-is-dependency-injection-8c9e7805502f)

The classes and objects that we create and use in our applications tend to fall in two categories:

* Service objects. These are “bags of methods” or “sets of functions”, and are used to modularize the code base. Good examples of service objects might be UserRepository, EmailSender, ProfileManager, etc.
* Data objects, either pure data or data combined with behavior (“proper” OO). Examples include Product, Invoice, UserVisitor, …
Both types of objects are created at different times in the application and used in different ways.

Service objects tend to be created once at the start of the application. They don’t change during the execution of the program. In other words, service objects are the modules from which your application logic is composed. Services might depend on one another, forming a dependency graph. Moreover, specific implementations of services might be interchangeable, depending on configuration, environment, command-line options, etc.

The crucial property of the service/module graph is that it is created statically. Only when the graph of services is wired, the application is usually ready to serve user requests. Hence the service objects/modules are static and global, as well as typically stateless. They only contain methods, without any (mutable) data, except maybe for configuration and pre-computed values. There are exceptions as well, such as cache services.

Data objects, on the other hand, are created dynamically, either in response to user interaction, API invocation, scheduled tasks, etc. They usually have a short, local lifespan. They carry and manipulate the data that the application processes. They might combine data and behavior, or be pure, “thin”, data structures.

**Dependency Injection is the process of creating the static, stateless graph of service objects, where each service is parametrised by its dependencies.**

## Types of Dependency Injection

There are three types of dependency injection:

- **constructor injection:** the dependencies are provided through a class constructor.
- **setter injection:** the client exposes a setter method that the injector uses to inject the dependency.
- **interface injection:** 

:pushpin: TODO: Add examples to each type of DI

## References

* https://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html
* https://blog.softwaremill.com/what-is-dependency-injection-8c9e7805502f
* https://martinfowler.com/articles/injection.html
* https://stackify.com/dependency-injection/
