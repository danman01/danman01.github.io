---
layout: post
title: "Asynchronous Programming in JavaScript"
date: 2020-12-30 11:17:28 -0400
comments: true
categories: 
tags: 
---

or async in js for short

### What is it?

![](https://miro.medium.com/max/1220/1*lFtbB3bq_e7yvhrkakUTEQ.jpeg)


TLDR; A coding style where you have chunks of code that do not block execution of other chunks of code. Async code runs later, allowing other code to happen now.
Or in plain English: Let's say you are making breakfast. You want to have toast with avocado and coffee with warmed oat milk, because you work hard dammit. You do the following steps:

0. measure coffee (get a cup and plate ready and all that, too)
1. Put coffee in coffee maker with water and all that and turn it on
2. Cut bread
3. Put bread in toaster
4. Slice avocado
5. Wait…or find more things to do with your unexpected spare time like read the paper.
6. Toast is ready! You take it out and apply avocado
7. Measure and start heating some milk
8. Wait…or with your abundance of time afforded by the modern marvels of the kitchen of tomorrow, find even more things to do, like check Reddit.
9. Coffee is ready! You pour a cup and top it with some steamy leche
10. Eat breakfast.

Can you pick out the items that are synchronous vs asynchronous? You are the worker. You have to manually do many of the above steps. But sometimes you get to hand off work to someone (thing) else. When that side job is complete, it alerts you, and you then take their finished product back into your breakfast production line.

![](https://miro.medium.com/max/960/1*QR2DOqIErCkdIKKMPL6MlA.gif)
{:.image-caption}
*robots helping us - [more](https://cheezburger.com/9949957/this-woman-makes-hilarious-shtty-robots-8-gifs)*

The same is true for writing code in JavaScript. We can at times choose to hand off code to something else, let's say an HTTP request to an API, while the rest of our program continues to run. When the results are ready from the API and the response is handed back to our program, we handle it at that moment, and then continue running any remaining parts of our program. Nothing is blocked. Breakfast is served without a hitch.

To best define async code, we have to know what synchronous code is.
Most lines of code (a.k.a expressions) happen one after the other. You have to wait for thing A to end before thing B can start:
```javascript
console.log('first thing');
console.log('2nd thing');
console.log('3rd thing');
```

You can guess the output. This is **synchronous** code. Thing A happens, then B, then C, just like we wrote it. Code executes from top to bottom (for the most part…not getting into hoisting and all of that…save it for another day). So if thing A took 5 minutes, we would wait 5 minutes, and our application would be blocked. Who likes waiting on the phone for an answer? I don't. That's why some smart folks created async programming. We don't have to wait anymore*!

However, the start of one event doesn't necessarily always follow the ending of another event. Instead, events can happen simultaneously, if you code them in such a way.

When one thing completes, it could trigger something else to happen. In JS we could say: my async function executed, and then triggered a callback function that used the response. More on **callbacks** later.

### Why Async programming?

Why start here? Because async programming is at the root of many of the other complexities of JavaScript, such as Promises, async / await, callbacks, and Reactive programming using RXJS. Building non-trivial applications in general will require using async programming (but not necessarily understanding it). Gaining a deeper understanding will lead to less frustration, faster bug fixes, and overall happier coding.

Why use async programming? It would be really sucky if your application couldn't do anything else while waiting for one request / process to finish. It would show a big "WAITING…" message to the user…and then finally would allow the user to do another action. That's how things used to happen. Now we can do things at the same time (well, milliseconds apart) as other actions, but we have to use specific tools and the async programming style to do that.

Just like the toaster in our breakfast example above allowed us to have extra time for more _work_ (a.k.a. checking Reddit), async programming allows our apps to process more lines of code without getting blocked.

### Examples of Async JS:

#### Interacting with a Server:

- Sending a request to a server (using an HTTP Client library like jQuery Ajax). The server processes…takes its time…and returns a response. In the meantime, the user can continue using our application. When the response comes back, it notifies the user (success or failure)
```javascript
$.post( "ajax/test.html", function( response ) {
  $( ".result" ).html( response );
});
```
In the above code example, the `function( data ) { // do something here }` is the callback function. Its job is to run when (or if) we get back a response from the async call (in this case, the post to the ajax/test.html endpoint). When the response is handed back to the application from the server call, our application handles it at that time, running the code inside the callback function and making use of the response that the server gave us.

More: https://api.jquery.com/jquery.post/

#### File system operations in Node.js:

Retrieving something from the filesystem (if using node.js: fs.readFile) . Same as a server request - the filesystem might take its sweet time. We can code this in such a way as to not block the rest of the app from running:

```javascript
fs.readFile('/etc/passwd', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

In the above example, the function fs.readFile takes a callback as the 2nd argument. This is where you would asynchronously handle the result of reading the file. Here, (we are using the fat arrow function syntax), our callback function receives two arguments, err and data, which you have access to inside your callback function body, as the documentation states.

#### setTimeout:

Using setTimeout to wait a certain amount of time before running our callback function

The setTimeout function is a classic way to have something happen in the future, while allowing the rest of your application to continue running, unblocked:

```javascript
console.log('first thing');
function later() { console.log('2nd thing'); }
setTimeout( later, 1000 );
console.log('3rd thing');
```

What results do you get when you run the above? Try it out!

```javascript
// ...in chrome dev tools...
first thing
3rd thing
2nd thing
```

So: thing A, thing C, and then thing B. The order the statements logged to the console was not the order of the lines of code we wrote. How did we achieve that again? We did it by using a combination of tools at our disposal: a function we wrote, which we creatively named later, and the built-in setTimeout function which allowed us to specify: run this function in 1000 milliseconds.
The function we wrote, later, is called a callback function. Its purpose is to run when another event tells is to run. A callback waits for instructions to run. It doesn't follow the order of the code statements as we wrote them. In our case, the setTimeout function waited 1000 milliseconds, and then said, "HEY, later, it's time to run. Whatever you are going to do, I don't care, just do it NOW". Pushy…but effective.

**Beginners**: take note of this one tricky thing about callbacks! The key to getting a callback (which is the term for a function that is passed in to another function) to behave correctly is to pass a reference to the function, instead of invoking it. That means, setTimeout takes the function argument (in our example, later). It does not take the result of the function argument ( which would be later() with parenthesis). Every.character.matters!

Except semicolons. Those don't matter and are just fuel for Linter Wars. So remember: Pass a reference to the function to the callback. Do not invoke it!

```javascript
setTimeout( later, 1000 ); 
// 1st arg later is the callback function
// 2nd arg 1000 is the time to wait before running the callback
```

At the same time, other events are starting and completing, and callbacks are handling responses. This allows our apps to be snappy, not holding back the users in any way. But it also introduces complexity and requires special knowledge to program in an async style.

#### Promises (future post!)

- Using promises, we can handle asynchronous operations with ease, achieve a sequential chain of operations (even with async data), without holding up the rest of our program. It's a safe way to deal with asynchronous values and handle them in the order we want, in a somewhat clean way. It's great at handling errors along the way, too.
- Until my post is ready, you can of course browse the excellent MDN documentation to read up on Promises: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise - and that's exactly what I will be referencing.
- Did you know? Incubus has a song called Promises, Promises, and it's about, you guessed it…asynchronous programming! In my post, I'll be sure to share my affection for Brandon Boyd.

#### Observables (future post!)

- Using RXJS Observables allows us to consume data from a producer. Instead of running a function and getting a result, when using Observables we would subscribe to the Observable, and when data is ready for us, it would be pushed to us. It's actually pretty cool, but takes a while to wrap your head around if you are new to the whole idea of Push / Pull systems (see link below). Start with promises, perhaps, then come back to Observables.

- The data can be consumed in a synchronous manner or an asynchronous manner, depending on when the producer gives it back to the consumer. The consumer doesn't know when the data will arrive, so the subscribe callback functions are coded to be handled asynchronously.
Multiple values can be consumed from the producer when using Observables, whereas Promises are for handling just a single value. I found it helpful to read this page of official documentation to get the gist of what is happening with Observables: https://rxjs-dev.firebaseapp.com/guide/observable

### Async / Await (future post!)

- Using async / await keywords to write asynchronous code. Essentially cleaner syntax for writing promises.

- In summary from MDN : "The async and await keywords enable asynchronous, promise-based behavior to be written in a cleaner style, avoiding the need to explicitly configure promise chains."

- And in the description:
" Await expressions suspend progress through an async function, yielding control and subsequently resuming progress only when an awaited promise-based asynchronous operation is either fulfilled or rejected. The resolved value of the promise is treated as the return value of the await expression. Use of async / await enables the use of ordinary try / catch blocks around asynchronous code."

### How does asynchronous programming actually work in JavaScript?

How do we achieve async programming in JS? It's a single threaded language. So we can't spawn more threads to do these other tasks in parallel. Instead we use this thing called the Event Loop.

The Event Loop acts like a Queue (first in / first out) that continues to process items in the queue until those items run out. Read more here: https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
When we write code that takes a callback, like an Ajax call that sends data to a server and gets a response back, we are placing two things in the **Event Loop**:
1. the Ajax call - this is placed immediately when the JS engine encounters the code, in normal top-to-bottom execution of our code.
2. The callback is placed back into the Event Loop once the response is received. This happens at some point in the future.

Getting the callback back into the Event Loop happens in conjunction with the hosting environment (most of the time, the browser), that JavaScript is running inside. When the browser gets a response back, it triggers the callback function.

Again, from You Don't Know JS (can you tell where I'm getting my info from? :P ):

> One common "thread" of all these environments is that they have a mechanism in them that handles executing multiple chunks of your program over time, at each moment invoking the JS engine, called the "event loop."

> In other words, the JS engine has had no innate sense of time, but has instead been an on-demand execution environment for any arbitrary snippet of JS. It's the surrounding environment that has always scheduled "events" (JS code executions).
> So, for example, when your JS program makes an Ajax request to fetch some data from a server, you set up the "response" code in a function (commonly called a "callback"), and the JS engine tells the hosting environment, "Hey, I'm going to suspend execution for now, but whenever you finish with that network request, and you have some data, please call this function back."

What is setTimeout really doing, in relation to the Event Loop? I'm glad you asked a question that MDN answered with such great detail!
"The function setTimeout is called with 2 arguments: a message to add to the queue, and a time value (optional; defaults to 0). The time value represents the (minimum) delay after which the message will actually be pushed into the queue. If there is no other message in the queue, and the stack is empty, the message is processed right after the delay. However, if there are messages, the setTimeout message will have to wait for other messages to be processed. For this reason, the second argument indicates a minimum time-not a guaranteed time."

Isn't that interesting? The 2nd argument to setTimeout isn't an exact, guaranteed time when the callback will run. Instead it is a minimum time. When that time arrives, our callback is pushed onto the end of the queue. So, messages in the queue still have to get processed before our setTimeout callback can get processed.

### Q&A:

**Q**. What did I learn while writing this?
**A1**. That async and parallel are not the same thing - parallel computing (working with more than on process or thread) is about things being able to occur simultaneously, says You Don't Know JS. So since JS is a single threaded environment, it relies on the Event Loop to put things into a single queue. A callback and other "async" operations simply put a result back into the Event Loop when they are finished executing.

**A2**. That async JS isn't actually always "non-blocking" - each process that is in the Event Loop still has to execute before the next process can run. So a long running process, for example, writing a million lines to a file, will block the next process. It is up to you, the developer, to write code in such a way that it cooperates with other concurrent code. Consider this section and example from You Don't Know JS that uses batching and setTimeout to allow small pieces of a large process to run, effectively "not blocking" the other parts of the app.

**A**. The Job Queue, introduced in ES6, which allows other "jobs" / processes to run at the end of the same queue. More on this in Promises…

**Q**. What bugs could pop up in async code?
**A**. Race conditions - when nondeterministic code (two possible outcomes from the same code) make your program give back results you didn't expect, or are hard to track down. How to fix? Use the debugger, and add breakpoints to your code in question. Step line by line through your code as it executes.

Incorrect (not-well understood ) order of execution of code. Again, add breakpoints / console.logs to see what's actually happening.

**Q**. Where can I find youtube videos on this topic of Asynchronous Programming in JavaScript?
**A**. here [event loop], here [crash course for absolute beginners, it says] and here [sync vs async explained] are a few top results, my good lad.

**Q**. I want to dig deeper.
**A**: This is most advisable! Check out You Don't Know JS section on async. And this MDN Tutorial on Asynchronous programming in Javascript is great.

**Q**. I read all the way to the end. Now what? 
**A**. Bless you. Here's a "DAD JOKE" to use in your next conference call while waiting for everyone to join:
"What do you call a bundle of hay in a church? Christian Bale."

_Originally posted on [medium](https://best-username.medium.com/asynchronous-programming-in-javascript-84b11fc637f1) on 12-30-2020_