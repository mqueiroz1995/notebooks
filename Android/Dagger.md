# Dagger <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Introduction](#introduction)
- [Types of Dependency Injection](#types-of-dependency-injection)
    - [Field Injection](#field-injection)
    - [Constructor Injection](#constructor-injection)
- [Building the Dependency Graph](#building-the-dependency-graph)
- [More on Dagger](#more-on-dagger)
    - [Scopes](#scopes)
    - [Component Dependencies](#component-dependencies)
    - [TODO: Subcomponents](#todo-subcomponents)
    - [TODO: Multibindings](#todo-multibindings)
    - [TODO: Qualifiers](#todo-qualifiers)
    - [TODO: Producers](#todo-producers)
    - [TODO: Component Builder](#todo-component-builder)
    - [TODO: Dagger 2 Android's Best Practices](#todo-dagger-2-androids-best-practices)
- [References](#references)

## Introduction

>The best classes in any application are the ones that do stuff: the `BarcodeDecoder`, the `KoopaPhysicsEngine`, and the `AudioStreamer`. These classes have dependencies; perhaps a `BarcodeCameraFinder`, `DefaultPhysicsEngine`, and an `HttpStreamer`.
>
>To contrast, the worst classes in any application are the ones that take up space without doing much at all: the `BarcodeDecoderFactory`, the `CameraServiceLoader`, and the `MutableContextWrapper`. These classes are the clumsy duct tape that wires the interesting stuff together.
>
>Dagger is a replacement for these FactoryFactory classes that implements the dependency injection design pattern without the burden of writing the boilerplate. It allows you to focus on the interesting classes. Declare dependencies, specify how to satisfy them, and ship your app.
><br><br>[Dagger User's Guide](https://google.github.io/dagger/users-guide)

In the Dependency Injection Notebook :pushpin: TODO: ![dependency injection](../Computer Science/DependencyInjection.md) we could understand what is this architectural design pattern and which benefits do we get by adopting it in our application.

Dagger is a Dependency Injection Framework that will help us with that. To use it we just need to communicate how it should build our application Dependency Graph and if we succeed on that Dagger will generate code that mimics the code we would have hand-written to ensure that dependency injection is as simple, traceable and performant as it can be.

## Types of Dependency Injection

:warning: Section's content extracted from [Foundations of Dagger - Cody Engel](https://proandroiddev.com/foundations-of-dagger-2-for-android-5ea1c14bceb1)

### Field Injection

You should use this method whenever you don’t control the instantiation of the object. In an Android app this typically means using it for framework classes such as views, activities, fragments, or the application class.

 ``` kotlin
 class UserFragment : Fragment() {
    @Inject
    lateinit var viewModelFactory: ViewModelProvider.Factory
    @Inject
    lateinit var appExecutors: AppExecutors
 ```

Looking at the class itself we’ll see that our `ViewModelProvider.Factory` is a `lateinit var` with the `@Inject` annotation. In Kotlin you need to mark these as `lateinit` since they will be initialized by Dagger at a later point in time. We also mark them with the Inject annotation. This signals to Dagger 2 that it should find an instance of that dependency (or create one if it doesn’t find one) and provide it to us. 

### Constructor Injection

This is the preferred way to provide dependencies not only in Dagger 2 but in general as well. Let’s take a look at the UserRepository.kt as it is an excellent example of constructor injection.

```kotlin
class UserRepository @Inject constructor(
    private val appExecutors: AppExecutors,
    private val userDao: UserDao,
    private val githubService: GithubService
)
```

To use constructor injection we just need to annotate the class constructor with the `@Inject` annotation. This will tell Dagger that it should find instances of AppExecutors, UserDao, and GithubService somewhere in it’s graph or create new instances of them. **Not only that but it will also add `UserRepository` to the dependency graph.** With UserRepository added to our graph it means we can have it injected somewhere else.

## Building the Dependency Graph

So if Dagger basics is about communicating to the framework how we should build our dependency graph, how do we do that? The first piece was already explained in the previous section: by using the `@Inject` annotation in a constructor Dagger will not only inject the required dependencies but also add the class to the dependency graph.

What if we can't access the code of the class to add the annotation? For example, if we are using Retrofit we have to use the builder provided by the framework.

For that we have Dagger Modules. To tell Dagger that one class is a module we should simply annotate the class with `@Module`. Inside modules we will create methods to provide dependencies, which will be annotated with `@Provides`. With that the framework will add the dependencies provided in the module to the Dependency Graph.

```kotlin
@Module
class AppModule {
    private const val BASE_URL = "..."

    @Provides
    fun provideNetworkService(): NetworkService {
        return Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .addCallAdapterFactory(RxJava2CallAdapterFactory())
            .build()
            .create(NetworkService::class.java)
    }
}
```

However it’s not enough to just create many different modules and expect Dagger to just work. In order to put everything together you need to create components which are the glue that holds everything together.

The component will be the entry point of your graph. It's an interface annotated with `@Component`, which receives as parameter an array with all the modules required to build the graph for the component.

```kotlin
@Component(
    modules = [
        AndroidInjectionModule::class,
        AppModule::class,
        MainActivityModule::class]
)
interface AppComponent {
    void inject(activity : MainActivity)
}
```

Defining the component we defined all the nodes of our graph and wired all of them together. After that, when building the project, Dagger will provide (by generating code) a implementation of the interface doing the hard work of injecting all the dependencies for us.

So the only remaining task is using the implementation generated for us in our entry point.

```kotlin
class MainActivity : Activity {
    
    ...

    override fun onCreate(savedInstanceState: Bundle) {
        super.onCreate(savedInstanceState)

        DaggerAppComponent.builder()
            .application(githubApp)
            .build()
            .inject(this)
    }


}
```

## More on Dagger

### Scopes

Dagger 2 provides `@Scope` as a mechanism to handle scoping. Scoping allows you to “preserve” the object instance and provide it as a “local singleton” for the duration of the scoped component.

`@Singleton` is one of the predefined scopes. It provides your dependencies as a singleton **as long as you are using the same component**. 

This is the reason why `@Singleton` components should be instantiated and maintained in the Application class. The singleton application component should persist throughout the application lifecycle, and the app should refer to this component to uphold that “single instance” requirement.

In addition to the predefined `@Singleton` scope, we can define our own scopes in the following manner (this code should be in a file named CustomScope.java):

```java
@Scope
@Retention(RetentionPolicy.RUNTIME)
public @interface CustomScope { }
```

The generated code for a component instantiated using `@CustomScope` is the same to a component instantiated using `@Singleton`.

If we do not specify any Scope annotation, the Component will create a new instance every time the dependency is injected, whereas if we do specify a Scope Component can retain the instance for future use.

### Component Dependencies

### TODO: Subcomponents

>Subcomponents are components that inherit and extend the object graph of a parent component. You can use them to partition your application’s object graph into subgraphs either to encapsulate different parts of your application from each other or to use more than one scope within a component.
>
>An object bound in a subcomponent can depend on any object that is bound in its parent component or any ancestor component, in addition to objects that are bound in its own modules. On the other hand, objects bound in parent components can’t depend on those bound in subcomponents; nor can objects bound in one subcomponent depend on objects bound in sibling subcomponents.
>
>In other words, the object graph of a subcomponent’s parent component is a subgraph of the object graph of the subcomponent itself.
><br><br>[Dagger 2 Documentation - Subcomponents](https://google.github.io/dagger/subcomponents)

### TODO: Multibindings

### TODO: Qualifiers

### TODO: Producers

### TODO: Component Builder

### TODO: Dagger 2 Android's Best Practices

http://eng.moldedbits.com/android,/dependency/injection/2018/01/09/dagger2-android-subcomponents.html

## References

* https://google.github.io/dagger/
* [Dagger 2 Decomposed and Demystified for Android](https://proandroiddev.com/dagger-2-decomposed-and-demystified-for-android-6a8ff6ad59c0)
* [Dagger 2 Android : Defeat the Dahaka](https://proandroiddev.com/dagger-2-android-defeat-the-dahaka-b1c542233efc)
* [Dagger 2 Scopes Demystified](https://www.techyourchance.com/dagger-2-scopes-demystified/)
