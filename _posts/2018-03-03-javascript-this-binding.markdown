---
layout: post
title:  "JavaScript this binding"
date:   2018-03-03 12:30:00 +0200
categories: JavaScript
---

Having done JavaScript for about 4 years, I have found that there are certain things about the language that I still did not understand. As we all know, JavaScript is an object-oriented programming language, anyone coming from other OOP languages generally would not struggle writing JavaScript. The problem comes with most of us when we take certain constructs from other OOP languages and assume that they work the same way in JavaScript.

Take a look at this code snippet written in Java  

{% highlight java %}
/*1*/  public class Foo {
/*2*/    public String name;
/*3*/    public Foo(String name) {
/*4*/      this.name = name;
/*5*/    }
/*6*/  }
/*7*/ Foo f = new Foo("Kenny");
/*8*/ f.name; // Kenny
{% endhighlight %}

In the above snippet, a new object is created and the `this` keyword, whenever used inside of the methods in `Foo`, will **always** refer to an instance of type `Foo` created on line 7, this is an unchangeable fact. The this keyword cannot reference or be bound to anything else other than an object instance created with the `new` keyword. This is also true in other OOP languages other than Java. 

The `this` keyword is one of many JavaScript constructs I struggled understanding because of my assumptions/expectations on how it should work. In this blog post, my aim is to clarify how the JavaScript `this` keyword is bound. 

At a high level, the `this` binding can happen in one of 4 ways:

- Default binding
- Implicit binding
- Explicit binding
- New binding

# Default binding

{% highlight javascript %}
  function sayHello(name) {
    console.log(this.name)
  }
  sayHello('Kenny');
{% endhighlight %}

It is easy to think that the output from `console.log` would be Kenny, but this is not the case. The confusion lies in thinking that the function parameter `name` can be accessed through the `this` context. The function parameter `name` is lexically scoped to function `sayHello`, whereas the `this` keyword is not lexically scoped. The `this` keyword uses "dynamic scoping", i.e. it's scope is determined on where and how the function call (sayHello in our case) is made.

In the above snippet, `sayHello('Kenny')` is called in the global scope, therefore JavaScript will look for `name` in the global scope. This is called default binding. Since we do not have `name` defined anywhere in the global scope, `this.name` holds the value `undefined`, the property `name` is added to the global scope and set to the value `undefined`. To prevent JavaScript from creating this global property, you can add 'use strict', this will cause a `TypeError` exception to be thrown when `name` is not found in the global scope instead of creating one.

The below ascii cast demonstrates default binding:

{::nomarkdown}<center><script type="text/javascript" src="https://asciinema.org/a/MwPyQ7zT7CYMn2jUPmvo4BCM3.js" id="asciicast-MwPyQ7zT7CYMn2jUPmvo4BCM3" async=""></script></center>{:/}
# Implict binding

{% highlight javascript %}
  var greeter = {
    name: 'Kenny',
    sayHello: function() {
    console.log(this.name)
  }
  greeter.sayHello();  // As we mentioned before passing name here will not make a difference so I left it out
  }
{% endhighlight %}

With implicit binding, the `this` keyword does not bind to the global scope, it infers its binding from the object with the function reference (greeter in this example). The object `greeter` makes a call to `sayHello()` using its function reference, the `this` keyword then references the `greeter` object, which is why we can access the property `name`.  Were this not the case, JavaScript would have attempted default binding. 

{::nomarkdown}<center><script type="text/javascript" src="https://asciinema.org/a/QlW89qFhfSLeovn8DSawEMPyV.js" id="asciicast-QlW89qFhfSLeovn8DSawEMPyV" async=""></script></center>{:/}

What if this is not the behaviour you want? What if you want to resolve the `name` from another property in the global scope?

# Explicit binding

{% highlight javascript %}
  var greeter = {
    name: 'Kenny',
    sayHello: function() {
    console.log(this.name)
  }
  var globalGreeter = { name: 'John' }
  greeter.sayHello.bind(globalGreeter)();  // As we mentioned before passing name here will not make a difference so I left it out
  }

{% endhighlight %}

Explicit binding allows the programmer to enforce at compile time, what the `this` binding is. You can do this by using the `.call()`, `.apply()` or the `.bind()` methods. Each of these methods take an object which can be used as the `this` binding. In the above snippet, we **explicitly** set the this reference to `globalGreeter`, so that when `sayHello` is called, it does not use implict binding and print 'Kenny'.

{::nomarkdown}<center><script type="text/javascript" src="https://asciinema.org/a/p2o2EIvCaeObyCGm8bss9AQwW.js" id="asciicast-p2o2EIvCaeObyCGm8bss9AQwW" async=""></script></center>{:/}
# New binding

The last form of binding we can perform is the new binding. 

{% highlight javascript %}
  function sayHello(name) {
    this.name = name;
  }
  var greet = new sayHello('Kenny');
  console.log(greet.name);
{% endhighlight %}

The `new` keyword creates a new object that uses **itself** as a this binding, i.e. when `this` reference is called within `sayHello`, it refers to the `sayHello` object instance, much like other OOP languages.

{::nomarkdown}<center><script type="text/javascript" src="https://asciinema.org/a/ptwOx7REMWrwBAeNyw4cHiCjN.js" id="asciicast-ptwOx7REMWrwBAeNyw4cHiCjN" async=""></script></center>{:/}

This is the behavior most programmers expect their code to have, but evidently, we need to be more cautious about making assumptions on how `this` is bound. Another option is to go around "dynamic scoping" and access `this` in the lexical scope. 
Ever seen this type of code anywhere?

{% highlight javascript %}
  function a() {
    var self = this;
    function b() {
      self.doSomethingCool(); // self is found in function a's scope
    }
  }
{% endhighlight %}
 
the variable `self` can now be used in a lexical fashion, all rules about lexical scoping will now apply and you can ignore everything about dynamic scope. This is also what es6 `() => {}` arrow functions offer you, it is a bit more than syntactic sugar. And that's it, I hope you learnt something.

I find JavaScript to be quite a fascinating and really misunderstood programming language, I am constantly learning new things about the language. A really good source of knowledge for me has been Kyle Simpson's [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS/) series of books, I urge you to give it a go. You might be suprised by how little we know about the workings of JavaScript, I definitely was. 
