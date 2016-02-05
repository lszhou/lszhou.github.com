---
layout: post
title: Asynchrony and Multithreading
---

There are already many discussions shown up in Stack Overflow about the difference between asynchronous programming and multithreaded programming.
I picked some nice explainations as follows, thanks a lot in advance for the relevant users.

by **[Elf Sternberg](http://stackoverflow.com/users/166838/elf-sternberg)**
> Understanding the difference between asynchronous programming and thread-based programming is critical to your success as a programmer.

> In a traditional, non-threaded environment, when a function must wait on an external event (such as a network event, a keyboard or mouse event, or even a clock event), the program must wait until that event happens.

> In a multi-threaded environment, many individual threads of programming are running at the same time. (Depending upon the number of CPUs and the support of the operating system, this may be literally true, or it may be an illusion created by sophisticated scheduling algorithms). For this reason, multi-threaded environments are difficult and involve issues of threads locking each other's memory to prevent them from overrunning one another.

> In an asychronous environment, a single process thread runs all the time, but it may, for event-driven reasons (and that is the key), switch from one function to another. When an event happens, and when the currently running process hits a point at which it must wait for another event, the javascript core then scans its list of events and delivers the next one, in a (formally) indeterminate (but probably deterministic) order, to the event manager.

> For this reason, event-driven, asynchronous programming avoids many of the pitfalls of traditional, multi-threaded programming, such as memory contention issues. There may still be race conditions, as the order in which events are handled is not up to you, but they're rare and easier to manage. On the other hand, because the event handler does not deliver events until the currently running function hits an idle spot, some functions can starve the rest of the programming. This happens in Node.js, for example, when people foolishly do lots of heavy math in the server-- that's best shoved into a little server that node then "waits" to deliver the answer. Node.js is a great little switchboard for events, but anything that takes longer than 100 milliseconds should be handled in a client/server way.

> In the browser environment, DOM events are treated as automatic event points (they have to be, modifying the DOM delivers a lot of events), but even there badly-written Javascript can starve the core, which is why both Firefox and Chrome have these "This script is has stopped responding" interrupt handlers.

by **[RyanWilcox](http://stackoverflow.com/users/224334/ryanwilcox)**

> Say it with me now: async programming does not necessarily mean multi-threaded.

> Javascript is a single-threaded runtime - you simply aren't able to create new threads in JS because the language/runtime doesn't support it.

> Frank says it correctly (although obtusely) In English: there's a main event loop that handles when things come into your app. So, "handle this HTTP request" will get added to the event queue, then handled by the event loop when appropriate.

> When you call an async operation (a mysql db query, for example), node.js sends "hey, execute this query" to mysql. Since this query will take some time (milliseconds), node.js performs the query using the MySQL async library - getting back to the event loop and doing something else there while waiting for mysql to get back to us. Like handling that HTTP request.

>  Edit: By contrast, node.js could simply wait around (doing nothing) for mysql to get back to it. This is called a synchronous call. Imagine a restaurant, where your waiter submits your order to the cook, then sits down and twiddles his/her thumbs while the chef cooks. In a restaurant, like in a node.js program, such behavior is foolish - you have other customers who are hungry and need to be served. Thus you want to be as asynchronous as possible to make sure one waiter (or node.js process) is serving as many people as they can.

>  Edit done: Node.js communicates with mysql using C libraries, so technically those C libraries could spawn off threads, but inside Javascript you can't do anything with threads.

---

Simple example:

My example sounds quite simple to demonstrate the above two concepts.

**\# single/multiple threads**

Assume you work for a project in big software development team managing codes via GitHub.
You and other developers could branch from the *master* branch at the same time working on different features.
These features are totally different and independent with each other maybe. You work together for different features simultaneously.

Unfortunately, one day your developer friend `Monkey` (Happy Chinese New Year of Monkey!) got sick and he has to stop his work. Then how about you?
You will feel sad for him but still keep working on your branch. Right, because your work/feature does not depend on his work.

This is just an real-world example for process crashes. You and your developer members act
as different processes or threads. One process crashes will not affect the others.

You may notice that, when you use your computer, the browser sometimes is dead but you can still move your mouse. Or
when you are in a video meeting, you lose the video but the audio is still on, because the browser and mouse, video and audio are
manipulated by different processes or threads.

**\# mutithreaded programming**

Ok, let go back to your development team. Your friend got sick and we still have to work. And assume your work is to *Reading 100 New Year Greeting Cards*.
how to program this? Absolutely, you can program one thread to read the card from card 1 to card 100. i.e, the instructions are sequential
and you read one card after another. But, wait, do we have any other better and more efficient solution?

Sure, you can create new threads in the current process and then you program two multiple threads, say two new thread, and ask thread1 to read card 1 to card 50, and ask thread2 to read from card 51 to card 100. This is just called: mutithreaded programming. You have two threads work for you which improves the efficiency. However, this is still **synchronous programming**. Because your instructions are still sequential.

More specifically, suppose you have a function `create()` which helps create a new thread, and your program looks like this:

```javascript
// create new threads
var thread1 = create();
var thread2 = create();
var thread3 = create();
var thread4 = create();
```
You will notice the codes are sequential. Assume current process has executed statement `var thread1 = create();` successfully but crashes immediately
after that, then how about the following threads? Or assume current process is now in the execution of `var thread1 = create();` but the function body
has a dead loop like this:

```javascript
function create(){
  //...
  while(1){
    console.log('hey!')
  }

  return newThread;
}
```
then how about the following threads?

Right, in the first case you will only get one new thread, and in the latter case, it is even worse, you get nothing. Just because you want to program
multithreadedly, but using synchronous programming. The following function call invocation depends on previous function call response.

**\# Asynchrony**

Let go back to your team and try to understand **asynchrony**. Suppose your team lead ask you to take Monkey's work as wel. So now you have to
work simultaneously for two features. What you will do? Generally, we will switch between the two features. Or for example, if you got a problem in your feature and do not know how to solve the problem in a short time, then you will switch to work on Monkey's feature in order to save time. Then when you
come with some idea to solve your problem, you will switch back to you feature, right?

This is called **Asynchrony**. My execution does not depends on your execution. So another example:

```javascript

var thread1 = create(//wait 1 hour for I/O or internet);
var thread2 = create(// cost 1 second);

```

In the above example, if the creation of thread1 will cost 1 hour, then I definitely want to witch to create thread2 first. Then when I/O is ready,
I will have the thread1 created. the stupid scenario, waiting for 1h until I/O or internet is ready, then create thread1 and then thread2 sequentially, should be definitely avoided. This is especially important in web application development.
