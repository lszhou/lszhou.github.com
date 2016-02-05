---
layout: post
title: Asynchrony and Multithreading
---

There are many discussions show up in Stackover Flow about the difference between asynchronous programming and multithreaded programming.
I picked some nice explainations as follows, thanks a lot in advance first for the relevant users.

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
