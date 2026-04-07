# Async JavaScript (ES6+) — Promises, “Two‑Pronged Facade” Functions, Web APIs, Queues & Event Loop

These notes explain **how Promises work under the hood**, especially in the context of **`fetch()`** as a “two‑pronged facade function” that:
1) starts background browser work, and  
2) immediately returns a **Promise placeholder object** to JavaScript.

---

## 1) The Big Problem ES6+ Solves

JavaScript runs on a **single thread** (one call stack).  
If JS had to *wait* for slow operations (like network, timers, file reads), the whole page/app would freeze.

So the modern web relies on **asynchronous** behaviour:

- JS *starts* an operation (network, timer, etc.)
- JS continues running other code
- when the operation finishes, JS later runs your “continuation” code (callback / `.then()` handler)

This is how we build **non-blocking web apps**.

---

## 2) Key Idea: “Two‑Pronged Facade Functions”

### Definition (in this lecture’s context)
A **two‑pronged facade function** is a function (like `fetch`) that *simultaneously*:

1. **Initiates background work** in the browser (Web APIs / browser features), such as a network request
2. **Returns immediately** to JavaScript with a **Promise object** that represents the future result

So you get:
- background work happening outside the JS thread
- a JS object that lets you attach “what to do when it’s ready”

> [!important]
> This “two‑pronged” design is why Promises feel cleaner than older callback-only patterns.

---

## 3) Old School vs New School Async

### Old school (typical examples)
- XHR (`XMLHttpRequest`)
- callbacks with `setTimeout`, event listeners, etc.

### New school
- `fetch()` + **Promises**
- later: `async/await` (built on Promises)

#### Why “old school setTimeout-style” was confusing (core idea)
`setTimeout` triggers background timer work, **but doesn’t return a “tracked object”** representing that operation in JS memory.

So it can feel like:
- work is happening “somewhere else”
- your callback returns later, but the “link” between the async work and your JS state is harder to reason about

This is one reason Promises were a big improvement: they provide a **first-class placeholder value** you can pass around.

---

## 4) Promises: What They Are

### Promise (conceptual definition)
A **Promise** is a special JS object that is returned immediately and acts as a **placeholder** for data that will arrive later from the browser’s background work.

You can think of a Promise as:
- “I don’t have the value yet”
- “but I *will* have it later”
- “and here’s where you register what to do when I do”

---

## 5) The Core Example (`fetch`)

```js name=promise_fetch_basics.js
function display(data){
  console.log(data);
}

const futureData = fetch('https://twitter.com/will/tweets/1');
futureData.then(display);

console.log("Me first!");
```

### Step-by-step explanation (what happens)
1. `fetch(...)` is called
2. **Browser starts a network request** in the background (outside the JS thread)
3. `fetch` **returns immediately** with a **Promise object** stored in `futureData`
4. `futureData.then(display)` registers `display` as the function to run once the promise resolves
5. `console.log("Me first!")` runs immediately (because JS didn’t block)
6. Later, when the network data arrives, the Promise is fulfilled and `display(data)` is run automatically

---

## 6) Your Notes (Rephrased + Expanded)

### Your key point #1 (rephrased)
**ES6 introduced a cleaner async pattern** using Promises, where functions like `fetch` behave as “two‑pronged facade functions”.

### Your key point #2 (rephrased)
Older async patterns used tools like **XHR** and callback-driven Web APIs. Modern code commonly uses **`fetch()`**, which returns a Promise and is easier to compose and reason about.

### Your key point #3 (rephrased + clarified)
A two‑pronged facade function:
- triggers a **Web API / browser feature** to perform async work (e.g., network)
- returns a **Promise object in JS memory** immediately so your code can attach handlers to it

### Your key point #4 (rephrased)
`fetch()` is powerful because it delegates network communication to the browser’s networking stack, while JavaScript continues running without blocking.

---

## 7) What `.then()` Really Means

### Lecture idea
Any code we want to run “on the returned data” must be **saved on the promise object**.

- `.then()` stores your function (handler) inside the promise’s internal bookkeeping (often explained as `onFulfilled` handlers).
- When the Promise resolves, JS automatically schedules those handlers to run with the resolved value.

### Practical meaning
- You do **not** run `display` yourself at the right moment.
- The Promise mechanism + event loop ensures it runs once data is ready.

---

## 8) Under the Hood: Web APIs, Queues, Event Loop

### Main actors
- **Call Stack**: where JS runs functions *now*
- **Web APIs (Browser features)**: do background async work (timers, network)
- **Task Queue (Callback Queue / Macrotask Queue)**: holds callbacks like `setTimeout`
- **Microtask Queue**: holds Promise reaction jobs (`.then` callbacks)
- **Event Loop**: checks when to move queued work onto the call stack

---

## 9) Microtasks vs Tasks: The Rule That Matters

### Lecture rules (clean version)
When background work finishes:

- **Promise `.then` handlers** go to the **Microtask Queue**
- **Timer callbacks** (e.g., `setTimeout`) go to the **Task/Callback Queue**

Then:

> Functions are moved to the call stack **only when the call stack is empty** (and global code has completed).

Priority rule:

> [!important]
> **Microtask Queue has priority over Task/Callback Queue.**  
> JS will drain microtasks first before running a normal task.

---

## 10) Example with `setTimeout`, `fetch`, and blocking code

```js name=promises_vs_timeout_flow.js
function display(data){ console.log(data); }
function printHello(){ console.log("Hello"); }
function blockFor300ms(){ /* blocks js thread for 300ms */ }

setTimeout(printHello, 0);

const futureData = fetch('https://twitter.com/will/tweets/1');
futureData.then(display);

blockFor300ms();
console.log("Me first!");
```

### What this demonstrates
- `setTimeout(..., 0)` does **not** mean “run immediately”
- it means “run after current code finishes, when the stack is empty, and when the event loop schedules it”

Also:
- while `blockFor300ms()` runs, **nothing else can run** on the JS thread  
  (it blocks the call stack, so even ready callbacks must wait)

### Typical ordering intuition
1. Global code runs first
2. When global code ends and stack clears:
   - microtasks (`.then`) run before tasks (`setTimeout`)
3. But actual timing depends on when the network request finishes

---

## 11) Why Developers Struggle (Lecture Points)

### Problems
- Many developers use Promises daily but don’t understand:
  - microtask vs task queues
  - why `.then` callbacks run when they do
  - how this impacts debugging and ordering
- This makes debugging harder and can affect interview performance

### Benefits
- Promises give a cleaner, more readable “pseudo-synchronous” style:
  - chain `.then(...)`
  - centralised error handling (with `.catch`, not shown in your excerpt but part of the model)

---

## 12) Key Takeaways (Exam/Interview Style)

### Core definitions
- **Two‑pronged facade function**: starts background browser work + returns a Promise immediately
- **Promise**: JS placeholder object for a future value
- **`.then(handler)`**: registers a function to run when the Promise fulfills
- **Microtask queue**: where Promise reaction callbacks go
- **Task/callback queue**: where `setTimeout` callbacks go
- **Event loop**: moves ready callbacks to the call stack when it’s empty (microtasks first)

### Why this matters for building apps
Promises + Web APIs + queues + event loop enable:
- **Non-blocking applications** (UI stays responsive)
- **Unpredictable completion times handled safely**
- Modern web apps to load data dynamically without freezing the page

---

## 13) Glossary (Quick Reference)

| Term | Meaning |
|---|---|
| **Web API / Browser feature** | Background system handling network/timers/etc. outside JS thread |
| **Promise** | Special object representing a future value |
| **Fulfilled / Resolved** | Promise has successfully produced its value |
| **Rejected** | Promise failed (error) |
| **`.then()`** | register “on success” continuation |
| **Call stack** | where JS executes functions |
| **Microtask queue** | queue for Promise handlers |
| **Task queue** | queue for timer/events callbacks |
| **Event loop** | scheduling mechanism that checks stack + queues |

---

## 14) Your One-Sentence Summary (Improved)
**`fetch()` is a two‑pronged facade function: it asks the browser to perform a network request in the background and immediately returns a Promise placeholder to JavaScript, where we attach `.then()` handlers that run later via the**



Any code we want to run on the returned data must also be saved on the promise object, added using the .then method to the hidden property ‘onFulfilment’

Automatic Porperty of Pormise ObJECT: An value property and an Array of function to be triggered when the value is updated

hen a promise's value property is updated, what happens to functions in the onFulfilled array? ( When like you get a new tweet for it) You: They are automatically triggered to run with the updated value as input

The fetch .then (Micro queue) is the one with the highest priority..
The function goes in another queue known as the callback queue.

Functions attached to promise objects using .then() or .catch(), and functions associated with promise resolution. Any function directly linked to a promise object will be added to the microtask queue.

The data from the network requst in JS is populates when: The data is not filled in immediately, but only after all global code has finished running. This ensures that the data is available before any associated callback functions are executed.