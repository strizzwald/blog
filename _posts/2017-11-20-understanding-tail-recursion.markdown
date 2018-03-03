---
layout: post
title:  "Understanding Tail Recursion"
date:   2017-11-20 21:42:28 +0200
categories: Computer Science
---

In this blog post, I would like to shed some light on tail recursion. To understand what tail recursion is, we need to understand what recursion or what a recursive call is.

A recursive call happens when a function makes a call to itself. Generally, there are two things required when creating recursive functions: 

1. A recursive call. This is the call to the function
2. A base case. This is some state of a variable (usually local variable) that determines when a recursive call returns

{% highlight javascript %}
  // factorial.js
  function factorial(n) {
    if(n === 0) { // Base case
      return 1
    }
    return n * factorial(n - 1) // Recursive call
  }
{% endhighlight %}

The above example is a normal recursive function, the same result can be achieved iteratively with the exact same run-time complexity O(n). For large numbers, the above approach uses way more memory, and this is just a side effect of how such recursive calls are represented in memory. Below are simplified stack frames for `factorial(3)`:

**Call 1**
  
  - factorial(3)
  - stack
    - n = 3

**Call 2**
  - factorial(2)
  - stack
    - n = 3 
    - n = 2

**Call 3**
  - factorial(1)
  - stack
    - n = 3
    - n = 2
    - n = 1

**Call 4**
  - factorial(0)
  - stack
    - n = 3
    - n = 2
    - n = 1
    - n = 0

In essence, each recursive call causes a stack frame creation(i.e. memory allocation). For trivial problems this may not be a problem. But for other scenarios like computing a [fibonacci(n)](https://stackoverflow.com/questions/13826810/fast-fibonacci-recursion) sequence, in cases where some computations are repeated the number of stack frames pushed on to the stack can become greater than n (unless you use __memoization__). 

So how can we solve this and without moving to an iterative approach? We can use __tail recursion__. Essentially, the problem with the above is not with the recursive calls but rather how each call creates a new stack frame. We would like to use recursion without creating a huge memory footprint on the stack. Tail recursion grants us this ability. 

We say that a function is using tail recursion, if its last line of execution is a recursive call. A function using tail recursion will perform all its calculations before making its recursive call, this means that each recursive call does not create a new stack frame. 

{% highlight javascript %}
  // factorial.js
  function factorial(n, i=1) {
    if(n === 0) { // Base case
      return i 
    }
    return factorial(n - 1, i * n) // Recursive call
  }
{% endhighlight %}

The above function will only ever need one stack frame per recursive call, this is because each recursive call pushes a frame and returns immediately (pops the stack frame), this is not the same with non-tail recursive functions which push frames on each call, leaving evaluation to be the last step. 

A key point to note here is that for tail recursion, when the base case is reached there is no need to pop frames from the stack (besides the current frame ofcourse), by the time we reach the base case we can safetly return an answer, this is because each recursive call made its calculations prior to making another recursive call. 

In summary, tail recursion can save you space on the stack. It can also help prevent stack overflow errors. This is just one approach of using less memory. Another is memoization, which is a topic for another blog post. 

