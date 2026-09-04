
# 🧠 JavaScript The Hard Parts: Asynchronicity  & The Event Loop



## 1. The Core JavaScript Engine (The Baseline)
JavaScript executes code using a strict set of rules. It is **single-threaded** (executes one command at a time) and **synchronously executed** (runs each line in the exact order it appears).

The core JS engine consists of three main parts:
 
1. **Thread of Execution:** Parses and executes the code line by line.
    
2. **Memory (Variable Environment):** Where variables and functions are stored.
    
3. **Call Stack:** A stack data structure that tracks the current execution context. Whatever function is at the top of the call stack is the _only_ thing JavaScript is currently processing.
    

**The Problem:** Because JS is single-threaded, slow operations (like `const tweets = getTweets("http://...")` taking 350ms) will block the thread of execution. No other code can run. The UI freezes.
## 2. Web Browser APIs (The Facade)

JavaScript alone cannot make network requests, track time, or interact with the DOM. It delegates these tasks to the environment (the Web Browser).

- `setTimeout` is **not** JavaScript.
    
- `fetch` is **not** JavaScript.
    
- `document.querySelector` is **not** JavaScript.
    

These are **facade functions**. When you call `setTimeout(printHello, 1000)`, JavaScript is simply handing a message to the Web Browser's internal timer feature (written in C++). JS then instantly moves on to the next line of code.

## 3. The Queues & The Event Loop

When a Web Browser finishes a background task (like a timer hitting 0ms), it cannot arbitrarily throw the callback function (`printHello`) back into the JS Call Stack. That would interrupt running code and cause chaos. It must wait its turn.

This introduces two distinct waiting areas:

1. **The Callback Queue (Task Queue):** Where standard Web API callbacks (like `setTimeout` or DOM events) wait.
    
2. **The Microtask Queue:** Where Promise-deferred functions wait. **This queue has absolute priority over the Callback Queue.**
    

### The Event Loop (The Gatekeeper)

The Event Loop is a continuous checking mechanism with a strictly defined set of rules. It only asks one question: **"Is the Call Stack entirely empty, AND has all global synchronous code finished executing?"**

- If **NO**: Do nothing. Let JS keep running.
    
- If **YES**: Dequeue the oldest function from the Microtask Queue and push it to the Call Stack. If the Microtask Queue is empty, it checks the Callback Queue.
    

This is why `setTimeout(printHello, 0)` followed by `console.log("Me first!")` logs "Me first!" immediately. `printHello` was sent to the Web API, instantly moved to the Callback Queue, but the Event Loop **will not** move it to the Call Stack until the global `console.log` has finished.

## 4. Promises (The ES6 Paradigm Shift)

Prior to ES6, we relied on passing callback functions directly into Web APIs (Callback Hell). ES6 introduced Promises, which use a **two-pronged facade function** (like `fetch`).

When `fetch` is called, it does two things simultaneously:

1. **In the Browser:** It initiates background work (an XHR network request).
    
2. **In JavaScript:** It immediately returns a placeholder object (the Promise) into JS memory.
    

This Promise object has hidden properties:

- `[[PromiseStatus]]`: Starts as "pending", changes to "resolved" or "rejected".
    
- `[[PromiseValue]]`: The data returned from the background task.
    
- `onFulfilled`: An array of functions to trigger when the value is updated. We push functions into this array using the `.then()` method.
    

When the background task completes, the browser updates the Promise object. Any functions waiting in the `onFulfilled` array are then pushed to the **Microtask Queue** to be executed by the Event Loop.


When will a function from the callback queue be allowed to execute?
After all synchronous global code has finished executing

### Important Points:

JavaScript is strictly a **single-threaded, synchronous language**. It possesses exactly one **Thread of Execution** and one **Call Stack**. It executes code line-by-line, top-to-bottom. It physically cannot perform multitasking. Slow synchronous operations (like massive `for` loops) will completely block the main thread, freezing the entire application.

The ECMAScript specification (raw JavaScript) has no concept of time, networks, or DOM manipulation. Functions like `setTimeout`, `fetch`, and `document.querySelector` are **Web APIs** provided by the runtime environment (the browser or Node.js). JavaScript interacts with these external tools via **facade functions**—commands that look like JS but are actually triggers for background C++ browser features.

 When `setTimeout(printHello, 0)` is called, JS hands the timer task to the Web API and instantly pops `setTimeout` off the **Call Stack**. 2. `printHello` does NOT go to the Call Stack. It is held by the Web API. 3. When the timer finishes (even at 0ms), `printHello` is pushed into the **Callback Queue** (or Task Queue). 4. It waits there. The **Event Loop** is a mechanism that constantly monitors the Call Stack. 5. **Only** when the Call Stack is completely empty, and all global execution is finished, will the Event Loop dequeue `printHello` from the Callback Queue and push it onto the Call Stack to be executed.

```js
function display(data){console.log(data)}

 function printHello(){console.log("Hello");}
 
 function blockFor300ms(){} // blocks js thread for 300ms 
 
setTimeout(printHello, 0);

const futureData = fetch('https://twitter.com/will/tweets/1')
futureData.then(display)
 
 blockFor300ms()
 
 console.log("Me first!");
```

The browser only receives a reference to manage the event/timer. The function itself, and its "backpack" (technically known as the **Closure** or the `[[Environment]]` hidden property), remains strictly within JavaScript's heap memory. Closures allow a function to remember the variables in its lexical scope regardless of _where_ or _when_ it is eventually executed by the Call Stack.