# Observer <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Introduction](#introduction)
- [TODO: Encapsulation](#todo-encapsulation)
- [TODO: Abstraction](#todo-abstraction)
- [TODO: Inheritance](#todo-inheritance)
- [Polymorphism](#polymorphism)
  - [Overrinding](#overrinding)
  - [Overloading](#overloading)
  - [TODO: Generic](#todo-generic)
  - [TODO: Late Binding](#todo-late-binding)
  - [TODO: Polytypism](#todo-polytypism)
  - [Implementation Aspects](#implementation-aspects)
- [References](#references)

## Introduction

Object-oriented programming (OOP) is a programming language model organized around objects rather than "actions" and data rather than logic. Historically, a program has been viewed as a logical procedure that takes input data, processes it, and produces output data.

The programming challenge was seen as how to write the logic, not how to define the data. Object-oriented programming takes the view that what we really care about are the objects we want to manipulate rather than the logic required to manipulate them.

## TODO: Encapsulation

## TODO: Abstraction

## TODO: Inheritance

## Polymorphism

Polymorphism describes the concept that objects of different types can be accessed through the same interface. Each type can provide its own, independent implementation of this interface. It is one of the core concepts of object-oriented programming (OOP).

Polymorphism allows the expression of some sort of contract, with potentially many types implementing that contract in different ways, each according to their own purpose. Code using that contract should not have to care about which implementation is involved, only that the contract will be obeyed.

While Pollymorphism is an OOP concept, several languages have facilities such as Method Overriding, Method Overloading, and Operator Overloading. 

### Overrinding

Method Overriding is when a method defined in a superclass or interface is re-defined by one of its subclasses, thus modifying/replacing the behavior the superclass provides. The decision to call an implementation or another is **dynamically** taken at runtime, depending on the object the operation is called from. Notice the signature of the method remains the same when overriding.

Also known as *Subtyping Polymorphism*.

### Overloading

Method Overloading is the action of defining multiple methods with the same name, but with different parameters. It can be seen as **static polymorphism**. The decision to call an implementation or another is taken at coding time.

Operator Overloading is the ability of a certain language-dependant operator to behave differently based on the type of its operands (for instance, + could mean concatenation with Strings and addition with numeric operands).

### TODO: Generic

Also known as *Parametric Polymorphism*.

### TODO: Late Binding

Also known as *Duck Typing*.

### TODO: Polytypism

### Implementation Aspects

Polymorphism can be distinguished by when the implementation is selected: statically (at compile time) or dynamically (at run time).

Static polymorphism executes faster, because there is no dynamic dispatch overhead, but requires additional compiler support. Further, static polymorphism allows greater static analysis by compilers (notably for optimization), source code analysis tools, and human readers (programmers). Static polymorphism typically occurs in *Overloading* and *Parametric Polymorphism*. It is possible to achieve static polymorphism with subtyping through more sophisticated use of [template metaprogramming](https://en.wikipedia.org/wiki/Template_metaprogramming), namely the [curiously recurring template pattern](https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern).

Dynamic polymorphism is more flexible but slower — for example, dynamic polymorphism allows *Late Binding*, and a dynamically linked library may operate on objects without knowing their full type. Dynamic polymorphism is usual for subtype polymorphism.

## References

* https://searchmicroservices.techtarget.com/definition/object-oriented-programming-OOP
* https://stackify.com/oop-concept-polymorphism/
* https://stackoverflow.com/a/12894211
* https://stackoverflow.com/a/409982
* https://en.wikipedia.org/wiki/Polymorphism_(computer_science)