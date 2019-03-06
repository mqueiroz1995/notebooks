# Dependency Injection <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Shorter Introduction](#shorter-introduction)
- [Longer Introduction](#longer-introduction)
- [Dependency Injection Frameworks](#dependency-injection-frameworks)
- [When to use Dependency Injection?](#when-to-use-dependency-injection)
- [References](#references)

## Shorter Introduction

>"'Dependency Injection' is a 25-dollar term for a 5-cent concept. That's not to say that it's a bad term... and it's a good tool. But the top articles on Google focus on bells and whistles at the expense of the basic concept."
><br> [Dependency Injection Demystified](https://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html)

Dependency injection is an architecture pattern that by decouples the usage of an object from its creation.

## Longer Introduction

> Extracted from: [What is Dependency Injection? - Adam Warski, SoftwareMill](https://blog.softwaremill.com/what-is-dependency-injection-8c9e7805502f)

The classes and objects that we create and use in our applications tend to fall in two categories:

* Service objects. These are “bags of methods” or “sets of functions”, and are used to modularize the code base. Good examples of service objects might be UserRepository, EmailSender, ProfileManager, etc.
* Data objects, either pure data or data combined with behavior (“proper” OO). Examples include Product, Invoice, User, …
Both types of objects are created at different times in the application and used in different ways.

Service objects tend to be created once at the start of the application. They don’t change during the execution of the program. In other words, service objects are the modules from which your application logic is composed. Services might depend on one another, forming a **dependency graph**. Moreover, specific implementations of services might be interchangeable, depending on configuration, environment, command-line options, etc.

Data objects, on the other hand, are created dynamically, either in response to user interaction, API invocation, scheduled tasks, etc. They usually have a short, local lifespan. They carry and manipulate the data that the application processes. They might combine data and behavior, or be pure, “thin”, data structures.

**Dependency Injection is a architecture pattern that decouples the usage of those services from it's creation. Using DI, instead of having hard dependencies (dependencies instantiated inside the class, for example) we will have soft dependencies by passing them to the class as a constructor parameter or a setter, for example.**

## Dependency Injection Frameworks

As we saw, the dependencies between classes in our application will form a dependency graph, and this happens even if we are not using DI.

Let's see an example:

```java
public class ViewModel {
    private Repository repository;
    ...
}

public class Repository {
    private NetworkService networkService;
    private Database database;
    ...
}

public class NetworkService {
    ...
}

public class Database {
    ...
}
```

In this example, independently of the way we assign the dependencies, our `ViewModel` depends on the `Repository` that depends on the `NetworkService` and `Database`, so our graph would be 

:pushpin: TODO: CREATE GRAPH AND ADD IMAGE. 

If we used DI without a framework, we would have to chose a place to build all this dependencies, for example our Fragment or Activity, and then we would have to instantiate and wire all of them. 

Of course we could also implement more sophisticated techniques, like implementing a Service Locator or our own Dependency Injection framework. But why should we reinvent the well? That's exactly the reason the community developed and adopted a few frameworks.

## When to use Dependency Injection?
 
The biggest benefit of using DI is that it improves testability. By injecting the dependencies in the class you can easily replace the dependency implementation used for real world project with a mocked dependency.

So instead of using your `NetworkService` to make a real HTTP request that will hit a real server and return after sometime you can simply inject a `MockedNetworkService` that will fake the result and return instantaneously.

When we talk about reducing coupling, if *implementing DI without the usage of frameworks* it is a nearly zero-sum game. Of course our classes won't need to know about the creation of it's dependencies, but we still have to choose one point to create the dependency graph and inject them. This is usually delegated to a high-level class that will have to know about a lot of lower level dependencies.

## References

* https://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html
* https://blog.softwaremill.com/what-is-dependency-injection-8c9e7805502f
* https://martinfowler.com/articles/injection.html
* https://stackify.com/dependency-injection/
