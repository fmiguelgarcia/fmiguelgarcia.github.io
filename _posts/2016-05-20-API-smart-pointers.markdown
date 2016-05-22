---
layout: single
title:  "API, threads, and smart pointers"
date:   2016-05-20 16:19:48 +0100
categories: C++  
---
{% include toc %}

# Problem #
In this first entry, I would like start making a question on many minds: _“Should I use a raw pointer, a reference, a smart pointer or just a simple local variable on the stack?”_, and answer depends on what you want to say to whom will use your API or your code in the future.

There is a huge documentation about all smart pointers(SP) but anyone underlines the semantic beyond each of ones or how you can use to get better APIs.

# Solution #
<table>
 <tr>
  <th>Tool</th>
  <th>Scope</th>
  <th>Multi-thread</th>
  <th>Synchronization needed</th>
 </tr>
 <tr>
  <td><code>T</code></td>
  <td>Local scope</td>
  <td>Safe</td>
  <td>No</td>
 </tr>
 <tr>
  <td><code>const T</code></td>
  <td>Local scope</td>
  <td>Safe</td>
  <td>No</td>
 </tr>
 <tr>
  <td><code>T&amp;</code></td>
  <td>Multiple scopes</td>
  <td>Unsafe: Can't guarantee that variable is still alive between threads</td>
  <td>Yes</td>
 </tr>
 <tr>
  <td><code>T&#42;</code></td>
  <td>Multiple scopes</td>
  <td>Unsafe: Can't guarantee that variable is still alive between threads</td>
  <td>Yes</td>
 </tr>
 <tr>
  <td><code>T&#42; const</code></td>
  <td>Multiple scopes</td>
  <td>Unsafe: Can't guarantee that variable is still alive between threads</td>
  <td>No, it is lovely <code>const</code></td>
 </tr>
 <tr>
  <td><code>std::unique_ptr&lt;T&gt;</code></td>
  <td>Local scope</td>
  <td>Safe: It guarantees that variable is alive between threads</td>
  <td>No</td>
 </tr>
 <tr>
  <td><code>std::shared_ptr&lt;T&gt;</code></td>
  <td>Multiple scopes</td> 
  <td>Safe: It guarantees that variable is alive</td>
  <td>Yes</td>
 </tr>
 <tr>
  <td><code>std::shared_ptr&lt;const T&gt;</code></td>
  <td>Multiple scopes</td>
  <td>Safe: It guarantees that variable is alive</td>
  <td>No, it is lovely <code>const</code></td>
 </tr>
 <tr>
  <td><code>std::weak_ptr&lt;T&gt;</code></td>
  <td>Multiple scopes, <strong>not</strong> extension</td>
  <td>Safe: It guarantees that variable is alive when you use <code>std::weak_ptr&lt;T&gt;::lock()</code></td>
  <td>Yes</td>
 </tr>
 <tr>
  <td><code>QSharedDataPointer&lt;T&gt;</code></td>
  <td>Multiple scopes for reading and <strong>local scope in writing</strong></td>
  <td>Safe: It guarantees that variable is alive</code></td>
  <td>No, but new copies when write</td>
 </tr>

</table>

# TL;DR or Why? #

## The wild: raw pointers ##

Raw pointers are **not bad per se**. One problem is that you could have memory leaks because you have forgotten some <code>delete</code> or a *unexpected exception* is thrown.
However, the big issue is that you have no information about **who is the owner** of the raw pointer, in other words, who bears the **responsibility of deleting it**. Documentation of your API is the only way to solve this and sometimes developers do not read or follow that one (do you remember if you must free the array returned by <code>strerror</code> function?).
Let’s look the following example:

{% highlight cpp linenos %}Foo* my_function( int* array, int array_size );{% endhighlight %}

Who is the returned pointer owner? Should I delete it after using it or is something internal? **Raw pointers have no information about multithreading therefore we must use synchronization tools like mutex, semaphores, conditions...**

## The lone ranger: std::unique_ptr ##

As you probably know, this smart pointer **assures memory will be freed when we go out of the scope**. Our previous function will be something like:

{% highlight cpp linenos %}std::unary_ptr<Foo> my_function( int* array, int array_size);{% endhighlight %}

At first glance, future developers of your API will know what will be the scoped of the returned object. Moreover, we also increase the exception guarantee to **“Basic exception safety”** (a.k.a. no-leak guarantee).

But there are still more, when we use <code>std::unary_ptr</code> we also **facilitate the transformation from single thread code to multi-threading because we are ensuring that the object is unique** (its semantic value). A handicap is clear: <code>std::unary_ptr</code> can NOT be stored in containers, neither share them. But go ahead to our next guest.

## As mum said “Sharing is good”: std::shared_ptr. ##

If you come from languages which use garbage collector, you will be very comfortable using <code>std::shared_ptr</code>. Perhaps you would think: _“why are not <code>std::shared_ptr</code> used everywhere?”_ or _“Will C++ be easier than Java?”_ Well, you know C++ guys: we don’t like a unique solution for everything. In fact, we **need to be aware of the extra cost related to the internal shared counter** and the double memory request( this last has been dampened by <code>std::make_shared</code>, but that is another history).

{% highlight cpp linenos %}
std::shared_ptr<Foo> my_function( const vector<T>& values );
{% endhighlight %}

**Asynchronous uses are not easier** than <code>std::unique_ptr</code> because we are sharing memory, so we will need to protect access using synchronous tools( mutex, semaphore, etc).
Return an internal <code>std::shared_ptr</code> member is not a good idea as you could think. Let’s assume we have the following code:

{% highlight cpp linenos %}
class X {
  public:
    Y y() const;
  private:
    Y m_y;
};
{% endhighlight %}

These first option has a big problem: the **“y()” function makes a copy from m_y each time we call it**. If <code>sizeof(Y)</code> is big (or copy operation is expensive), it will affect the performance. A second option is return a const reference, isn’t it?

{% highlight cpp linenos %}
class X {
  public:
    const Y& y() const;
  private:
    Y m_y;
};
{% endhighlight %}

That choice has two issues at least:
<ul>
	<li>It does <strong>NOT</strong> guarantee to avoid copies, because it depends on where we will store the result. In example:
{% highlight cpp linenos %}
X x1; 
const Y y1 = x1.y(); // copy operation.
{% endhighlight %}

instead of something like:

{% highlight cpp linenos %}
const Y & y1 = x1.y(); // No copy operation
{% endhighlight %}
	</li>
	<li> Secondly, and more important, you are creating a <strong>strong dependency between your class structure and your API</strong> ( Source and Binary compatibility in API creation will be a future post). Well, let’s return a raw pointer or a <code>shared_ptr</code>:

{% highlight cpp linenos %}
class X {
public:
  X(): m_y( make_shared<Y>())
  {}
  std::shared_ptr<Y> y() const;
private:
  std::shared_ptr<Y> m_y;
};
{% endhighlight %}
	</li>
</ul>

That solution allows us to return <code>m_y</code> in an efficient way, because NO deep-copy is realized. It just makes a light copy, maybe a couple of pointers. In this way, the <code>Y</code> copy complexity does NOT mind. <em>We get a constant order copy operation.</em>

It seems a good idea but unfortunately it does <strong>not</strong>. In this way, we **lost the encapsulation**: we can change the returned object from outside <code>X</code>, and of course, <code>X</code> object will not be notified. Java guys solve that using the <code>clone()</code> method when they want to avoid this situation. But we have the elegant <em>copy-on-write</em> pointer to deal with this kind of things.

## QSharedDataPointer: Cheap encapsulation or Copy-on-write ##

This SP uses one of the best C++ capabilities: the <code>const</code> modifier. Initially, that smart pointer makes <em>shadow copy</em> in each copy operation, in a similar way that <code>shared_ptr</code> does it. However, when we try to change the object pointed by our smart pointer and it is not unique, a <em>deep copy</em> will be make (a.k.a detach operation). We have the best of both worlds!!!

The best example of this smart pointer is <code>QsharedDataPointer</code> from <em>Qt framework</em>. In Qt documentation you will find examples and information about how it works. You can also find other kind of smart pointers in Qt, like <code>QExplicitlySharedDataPointer</code> which will also be a well worth reading.

I have to say that some kind of issues, regarding to returning a object, has been covered by the new <strong>Move semantic</strong> in <strong>C++ 11</strong>. Nevertheless, if your legacy project does not allow you to use the newest compilers, have a look to those types. About <em><strong>multi-threading</strong>, this smart pointer is an absolute winner: it is automatically shared when it is just reading, and making a local deep copy when it is written.</em>
 
## The voyeur: std::weak_ptr ##

Until now, we are looking for keep the object alive through different scopes (<code>shared_ptr</code>), or link its life to a specific scope(<code>unique_ptr</code>) or just the raw pointer’s wildlife (no exception safety). But, What do I need if I just want to know if object is alive but I do not want to affect its scope? Sorry Heisenberg(Uncertainty principle) but we can do that.

To solve that we have the <code>std::weak_ptr</code>. It allows us to make a reference to a <code>std::shared_ptr</code> without expanding its scope. It means, when last <code>std::shared_ptr</code> is gone, its internal raw pointer will be deleted, no matter if there are any associated <code>std::weak_ptr</code>.

This behaviour is specially useful when you just want to <em>monitor some data from another threads</em>, i.e. when you want to check the status of long tasks working with huge result set from the database.

# Conclusion #

The importance of smart pointer is not just that they produce a safe environment to rise exceptions, nor they solve the resource management (RAII), nor you can write less code (other lenguages use <code>finally</code> to recovery from some complex cleaning phases, it just a workaround due the lack of destructor). No, <strong>the big advantage is their semantic value</strong>. Another developer, at a glance to the smart pointer type, is aware of the variable scope, its asynchronous constraints or what the API author kept in mind. And all of this without documentation.

Yes, Smart pointers generate safer code than raw pointers. Moreover, they also facilitate the code maintainability, and that is one of the greatest values for this industry. Last statistics show that software average lifetime is just 10 years. How many lines of code could a developer team generate along 10 years? What is the cost to add a new feature in that legacy code? What is my time-to-market?. Well, software maintainability responses that questions, and it is translated in cost contention or the difference between the rival company beat us or not. It is not a minor subject.
The most important part: Your comments, your feels or your experiences

