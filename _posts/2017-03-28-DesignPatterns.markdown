---
layout: post
comments: true
title:  "Creational Design Patterns"
date:   2017-03-28 09:07:22
description: "Creational design patterns with java examples"
categories: jekyll update
---
Design patterns are reusable solutions to often occurring problems in software engineering. As any problem has its own individual facets it's important to understand that these design patterns cannot be used thoughtlessly. Therefore design patterns are more like templates or best practices that help forming a nice fitting solution. 

In this blog post I want to list some of the more popular **creational patterns** (patterns about object creation mechanisms) with examples in java code. I don't want to get very deep into each pattern, but rather give you a quick overview of what each pattern does and when you want to use it.

## Singleton

If you need to make sure that there is only one instance of a class in your whole application, the singleton pattern might by helpful. Especially if you want to access this single instance from various parts throughout the system. 

{% highlight java %}
public class Singleton {
    private static Singleton instance = null;
    
    // Private Constructor to prevent instantiation.
    private Singleton() {}

    public static Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
{% endhighlight %}

As you can see in the java example the class that is implemented as a singleton needs to have a private constructor to prevent other classes from instantiating this class. Instead other classes can call the static `getInstance()` Method to get the current instance of the singleton class. A complete instantiation would look like:

{% highlight java %}
public class AnyClass {
    // (...)
    public void anyMethod() {
        // (...)
        Singleton singleton = Singleton.getInstance();
        singleton.doSomething();
        // (...)
    }
}
{% endhighlight %}

## Factory

Sometimes it's useful to create an object based on some criteria rather than configuring each individual attribute yourself. For example if you want to get a `Smartphone` object and use the `doCall()` Method on that object. Normally you would need to call the constructor giving the specs for that smartphone. With using the factory pattern you could instead instantiate the object giving in a criterion (`CHEAP`, `EXPENSIVE`).

In java code calling the `SmartphoneFactory` and using the `doCall()` method would look like this:

{% highlight java %}
public class AnyClass {
    // (...)
    public void anyMethod() {
        Smartphone smartphone = SmartphoneFactory
                .buildSmartphone(SmartphoneType.LARGE);
        smartphone.doCall();
    }
}
{% endhighlight %}

`Smartphone` is an abstract class that gets extended by classes like `Samsung Galaxy S7` or `Lenovo Moto G5`. These concrete smartphone classes get build by the `SmartphoneFactory` depending on the criterion passed in.

{% highlight java %}
public class SmartphoneFactory {
    public static Smartphone buildSmartphone(SmartphoneType type) {
        switch(type) {
            case CHEAP:
                return new LenovoMotoG5("16GB", "2GB RAM", (...));
            case EXPENSIVE:
                return new SamsungGalaxyS7("32GB", 
                        "4GB RAM", (...));
            default:
                return null; //unkown type
        }
    }
}
{% endhighlight %}

As you have seen in this small example the creation of the concrete smartphone implementation is completely encapsulated from `AnyClass`. Instead the class has let the `SmartphoneFactory` build the concrete object.

## Builder

Contructors can get pretty big for classes with a huge amount of attributes. This makes it very difficult to instantiate the object without the help of the auto completion of your IDE. Moreover it's often hard to tell which parameter stands for which attribute in the class. Consider this short example:

{% highlight java %}
Smartphone smartphone = new Smartphone(
                            "Samsung", "Galaxy S7", "4GB", "32GB");
{% endhighlight %}

Without looking into the `Smartphone` class it's hard to tell if 4GB is the RAM or the Memory. For situations like this the builder pattern could be used to make the code easier to read and the classes easier to instantiate. With this pattern implemented the instantiation would look like this:

{% highlight java %}
Smartphone smartphone = new SmartphoneBuilder("Galaxy S7")
        .setBrand("Samsung")
        .setRam("4GB")
        .setMemory("32GB")
        .build();
{% endhighlight %}

The `SmartphoneBuilder` is a static inner class within the `Smartphone` class. Note that the builder class takes all the mandatory attributes of `Smartphone` as parameter in the constructor. The final Smartphone gets instantiated in the `build()` method.

{% highlight java %}
public class Smartphone {
    private String name; // mandatory
    private String brand; // optional
    private String ram; // optional
    private String memory; // optional

    private Smartphone(SmartphoneBuilder builder) {
        this.name = builder.name;
        this.brand = builder.brand;
        this.ram = builder.ram;
        this.memory = builder.memory;
    }

    // Getters for name, brand, ram, memory

    public static class SmartphoneBuilder {
        private String name;
        private String brand;
        private String ram;
        private String memory;
        
        public SmartphoneBuilder(String name) {
            this.name = name;
        }
        
        public SmartphoneBuilder setBrand(String brand) {
            this.brand = brand;
            return this;
        }
        
        public SmartphoneBuilder setRam(String ram) {
            this.ram = ram;
            return this;
        }
        
        public SmartphoneBuilder setMemory(String memory) {
            this.memory = memory;
            return this;
        }
        
        public Smartphone build() {
            return new Smartphone(this);
        }
    }
}
{% endhighlight %}

To sum it up, I've shown you three creational design pattern: 
1. The **Singleton** for situations in which you want to make sure, only one single instance is created throughout the whole application.  
2. The **Factory** for situations in which you want to get preconfigured instances based on some criteria.
3. The **Builder** for classes that would otherwise need a huge list of parameters in the constructor.
