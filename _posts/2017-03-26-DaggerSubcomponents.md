---
layout: single
title:  "Dagger 2 - encapsulation with subcomponents"
date:   2017-03-25 01:23:45
categories: java
tags: [java, dagger2, subcomponent, di, dependency injection]
permalink: /:categories/:year/:month/:day/:title.html
---

One of the purpose of subcomponents is to encapsulate different parts from each other.

In this article I can show you how it can be achieved easily.

## With component
Imagine that you have two flows which are different and shouldn't share anything between each other.

Let's define two classes Main Application and Special Application.

Main Application should return response from a server:

```java
public class MainApplication {
    @Inject
    MainService mainService;

    public String getResult() {
        return mainService.getResponse();
    }

    ...
}
```
And Special Application should return...surprise surprise...special things.
```java
public class SpecialApplication {
    @Inject
    SpecialService specialService;

    public String getSpecialResult() {
        return specialService.getSpecialThings();
    }

    ...
}
```

To make this example works we also need to create a component for injecting our two services.

But the problem is that in both applications you can inject MainService and SpecialService. It means that from MainApplication you can inject SpecialService but SpecialService was designed only for SpecialApplication.

What we need here is explicitly define that MainApplication can use only MainService and SpecialApplication can use only SpecialService.

## With subcomponent

First let's create a parent component with two methods for installing subcomponents:

```java
@Component
@Singleton
public interface ServiceComponent {
    MainServiceComponent plus(MainServiceModule module);
    SpecialServiceComponent plus(SpecialModule module);
}
```

And then create two subcomponents:

```java
@Subcomponent
@ServiceScope
public interface MainServiceComponent {
    void inject(MainApplication mainApplication);
}

@Subcomponent
@ServiceScope
public interface SpecialServiceComponent {
    void inject(SpecialApplication specialApplication);
}
```
The inject method is exactly the place where we define from which class it can be injected.

## Custom scope

In dagger child subcomponents can't have the same scope as a parent. For that purpose we need to define our custom scope:

```java
@Scope
@Documented
@Retention(RetentionPolicy.RUNTIME)
public @interface ServiceScope {
}
```

## Wire everything together

To be able to inject MainService we need to build dependency graph:

```java
public class MainApplication {
    @Inject
    MainService mainService;

    public MainApplication() {
        DaggerServiceComponent.create().plus(new MainServiceModule()).inject(this);
    }

    ...
}
```

Because we previously defined in MainServiceComponent that it can be injected only from MainApplication now we 100% sure that it uses in the right place.

## Conclusion

With subcomponents you can easily encapsulate specific parts which will be used in place where they designed for.

Full example available [here](https://github.com/rustamgaifullin/dagger-example-java)
