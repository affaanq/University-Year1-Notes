
# 🔒 JavaScript: The Hard Parts — Closure

## What is Closure — The Big Picture



> [!info] Definition
> **Closure** is when a function "remembers" the variables from the scope in which it was **defined**, even after that scope has finished executing and is gone from the call stack.

Closure is described as the most **esoteric** concept in JavaScript, not because it's complicated, but because it involves understanding how JavaScript manages memory, execution contexts, and function definitions at a deep level.
### Why it feels confusing

Most concepts in programming are visible — you can see the data, you can see it being passed in. Closure is **invisible** — the data lives in a hidden property attached to a function, persisting silently in memory. You cannot see it directly. You have to **understand the engine** to know it's there.
### What Closure Unlocks

| Use Case               | How Closure Helps                                      |
| ---------------------- | ------------------------------------------------------ |
| `once()` helper        | Remembers if a function has already been called        |
| `memoize()` helper     | Caches results of expensive function calls             |
| Iterators & Generators | Maintains position/state across multiple calls         |
| Module Pattern         | Preserves private state without polluting global scope |
| Async JavaScript       | Callbacks & Promises hold onto data across time        |
| Partial Application    | Pre-fill some arguments and return a new function      |

---

## Functions Get a New Memory Every Run

Before understanding closure, you must deeply understand the **default behaviour**: functions **do not** remember anything between calls.

```js
function multiplyBy2(inputNumber) {
  const result = inputNumber * 2;
  return result;
}

const output    = multiplyBy2(7);   // new execution context → result = 14 → destroyed
const newOutput = multiplyBy2(10);  // brand new execution context → result = 20 → destroyed
```

### What happens in memory:

```
CALL 1 — multiplyBy2(7)
┌─────────────────────────────┐
│ LOCAL MEMORY                │
│  inputNumber = 7            │
│  result      = 14           │
└─────────────────────---─────┘
   Returns 14 → local memory DELETED ❌

CALL 2 — multiplyBy2(10)
┌─────────────────────────────┐
│ LOCAL MEMORY (brand new)    │
│  inputNumber = 10           │
│  result      = 20           │
└─────────────────────────────┘
   Returns 20 → local memory DELETED ❌
```

> [!important] Default Rule
> Every time a function is invoked, a **completely fresh** execution context is created with **empty local memory**. The previous call's data is gone. There is **zero memory** between runs — by default.

---

## Functions With Memories — The Core Problem

> [!question] The Engineering Problem
> What if a function **needed** to remember data between executions?

Think about real-world use cases:
- A **counter** that increments every time it's called
- A **cache** that remembers expensive results
- A **rate limiter** that tracks how many times a function has run

With the default model, none of this is possible. Local memory dies. Global memory works, but it's **polluting the global namespace** — messy, insecure, and hard to scale.

The solution is closure — **but it all starts with returning a function from another function.**

---

## Returning a Function From a Function

This is the mechanical foundation. Functions in JavaScript are **first-class objects**, meaning they can be returned just like a number or string.

```js
function createFunction() {
  function multiplyBy2(num) {
    return num * 2;
  }
  return multiplyBy2;  // ← returning the function DEFINITION, not calling it
}

const generatedFunc = createFunction();  // generatedFunc = multiplyBy2's code
const result = generatedFunc(3);         // 6
```

### Step-by-step execution trace:

```
1. createFunction() is called
   └── New execution context created
       └── LOCAL MEMORY:
           multiplyBy2 = [function code]
   └── Returns multiplyBy2 function definition out to global

2. Global memory:
   └── generatedFunc = [multiplyBy2 function code]

3. generatedFunc(3) is called
   └── New execution context
       └── LOCAL MEMORY:
           num = 3
   └── Returns 6
   └── output: result = 6
```

> [!tip] Key Distinction
> `return multiplyBy2` → returns the **code/definition** of the function (no parentheses)
> `return multiplyBy2()` → would **call** the function and return its result instead

---

## Defining vs Calling — Lexical Scope

This is the rule that makes closure possible. It is the single most important rule to understand:

> [!important] The Fundamental Rule of Closure
> **Where you DEFINE a function determines what data it has access to when it is called — not where you call it.**

This is called **lexical scoping** (or static scoping). The word *lexical* means "related to where the code is written."

### Example: Defined and called in the same context

```js
function outer() {
  let counter = 0;

  function incrementCounter() {
    counter++;           // ← accesses outer's variable
  }

  incrementCounter();    // ← called INSIDE outer, where it was defined
}

outer();
```

Here `incrementCounter` is both **defined and called** inside `outer`. It can access `counter` because they share the same execution context. Nothing special yet.

### Example: Defined inside, called outside

```js
function outer() {
  let counter = 0;

  function incrementCounter() {
    counter++;           // ← still accesses outer's counter
  }

  return incrementCounter; // ← function definition is sent out
}

const myNewFunction = outer(); // outer runs, returns incrementCounter
myNewFunction();               // counter becomes 1
myNewFunction();               // counter becomes 2
```

> [!warning] This Shouldn't Work — But It Does
> `outer()` has **finished executing**. Its execution context has been **removed from the call stack**. Its local memory should be **gone**.
>
> And yet `counter` is still being incremented correctly. Why?
>
> Because of **closure** — `incrementCounter` took `outer`'s local memory with it when it was returned.

---

## The Full Closure Mechanism — Step by Step

Let's trace through the full execution of the closure example with engineering precision:

```js
function outer() {
  let counter = 0;
  function incrementCounter() { counter++; }
  return incrementCounter;
}

const myNewFunction = outer();
myNewFunction();
myNewFunction();
```

### Phase 1 — `outer()` is called

```
CALL STACK:
│  outer()  │  ← added to stack
│  global   │

LOCAL MEMORY of outer:
  counter             = 0
  incrementCounter    = [function code]
                         ↑
                         At the moment this function is DEFINED,
                         it gets a hidden [[scope]] bond to outer's
                         local memory
```

### Phase 2 — `outer()` returns `incrementCounter`

```
outer() returns incrementCounter function definition
  └── The function definition carries a hidden [[scope]] property
      pointing to outer's variable environment:
      { counter: 0 }

outer's execution context is REMOVED from call stack ✓
BUT outer's variable environment is NOT garbage collected
because incrementCounter's [[scope]] still references it
```

### Phase 3 — `myNewFunction()` is called (first time)

```
CALL STACK:
│ myNewFunction() │  ← added
│ global          │

LOCAL MEMORY of myNewFunction:
  (empty — no local variables declared)

JS engine looks for `counter`:
  1. Check local memory → ❌ not found
  2. Check [[scope]] backpack → ✅ found! counter = 0
  3. Increment counter → counter becomes 1 in the backpack

CALL STACK after return:
│ global │  ← myNewFunction popped off
```

### Phase 4 — `myNewFunction()` is called (second time)

```
Same process — JS finds counter in [[scope]] backpack
counter is now 1 → incremented to 2
```

### Full Memory Diagram

```
GLOBAL MEMORY
├── outer          = [function code]
└── myNewFunction  = [function code — incrementCounter]
                          │
                          │ hidden [[scope]] property
                          ▼
                    BACKPACK (closure)
                    └── counter = 2  (after 2 calls)
```

---

## The Backpack — What It Actually Is

When `incrementCounter` was **defined** inside `outer`, JavaScript did something invisible but critical:

> [!important] The Bond
> When a function is **defined**, it gets a **bond** to the surrounding local memory (Variable Environment) in which it was created.
> This bond is stored in a hidden internal property: `[[scope]]`

The "backpack" is a metaphor for this bond. Imagine the function definition being "sent out" of `outer` with a little backpack strapped to it — containing all the live variable data from the scope where it was born.

### What the backpack contains:

- **Only** the variables that the inner function actually **references** (modern JS engines optimise this)
- The **live values** at the time the closure was created
- A **reference** — not a copy — so values can be mutated and read accurately

### The formal names for "the backpack":

| Name | Origin |
|------|--------|
| **Backpack** | Will Sentance / Codesmith — intuitive metaphor |
| **Closure** | The official JavaScript term |
| **C.O.V.E.** | Closed Over Variable Environment |
| **P.L.S.R.D.** | Persistent Lexical Scope Referenced Data |

> [!tip] All the same thing
> C.O.V.E., P.L.S.R.D., "backpack", and "closure" all refer to the **same mechanism** — the persisting variable environment attached to a returned inner function via `[[scope]]`.

---

## The `[[scope]]` Hidden Property — Under the Hood

Every function object in JavaScript has a hidden internal property called **`[[scope]]`** (also called `[[Environment]]` in the ECMAScript spec).

> [!note] ECMAScript Specification
> Per the ECMAScript spec, when a function is created, its `[[Environment]]` internal slot is set to the **current Lexical Environment** — the scope in which the function was defined. This is the formal mechanism behind closure.

```js
function outer() {
  let secret = 'xyz';

  function inner() {
    console.log(secret);  // inner.[[scope]] → outer's env → secret = 'xyz'
  }

  return inner;
}

const myClosure = outer();
myClosure(); // 'xyz'
```

### Scope chain — how JavaScript looks up variables:

```
When inner() runs and encounters `secret`:

  1. Look in inner's LOCAL MEMORY          → ❌ not found
  2. Look in inner's [[scope]] (outer env) → ✅ found: 'xyz'
  3. (Would continue to global if not found in [[scope]])
```

### Memory persistence explained:

```
Normal garbage collection rule:
  If no references to an object exist → it gets garbage collected (deleted)

Closure exception:
  outer()'s variable environment has a reference kept alive
  by inner's [[scope]] property
  → JavaScript engine cannot garbage collect it
  → It lives in memory as long as the closure (inner) exists
```

> [!warning] Memory Implication
> Closures **keep data in memory**. If you create many closures over large data structures and never release them, you can cause **memory leaks**. Understanding closure is therefore also important for **performance engineering**.

---

## Individual Backpacks — Each Closure is Independent

This is a critical and often misunderstood point:

```js
function outer() {
  let counter = 0;
  function incrementCounter() { counter++; }
  return incrementCounter;
}

const myNewFunction  = outer();  // Call 1 — creates closure A
myNewFunction();   // counter in closure A = 1
myNewFunction();   // counter in closure A = 2

const anotherFunction = outer(); // Call 2 — creates closure B (brand new)
anotherFunction(); // counter in closure B = 1
anotherFunction(); // counter in closure B = 2
```

### Why are they independent?

```
CALL 1 → outer() runs
  └── new execution context
      └── new local memory: counter = 0
      └── new incrementCounter defined → gets [[scope]] → this counter (0)
  └── Returns incrementCounter → assigned to myNewFunction
  └── BACKPACK A: { counter: 0 }

CALL 2 → outer() runs AGAIN
  └── another brand new execution context
      └── brand new local memory: counter = 0
      └── new incrementCounter defined → gets [[scope]] → this NEW counter (0)
  └── Returns incrementCounter → assigned to anotherFunction
  └── BACKPACK B: { counter: 0 }   ← completely separate from BACKPACK A

myNewFunction and anotherFunction are TWO DIFFERENT function objects
with TWO DIFFERENT backpacks pointing to TWO DIFFERENT variable environments.
```

```
GLOBAL MEMORY
├── myNewFunction   → [fn code] + BACKPACK A { counter: 2 }
└── anotherFunction → [fn code] + BACKPACK B { counter: 2 }
                                   ↑
                                   Independent — changes to one
                                   do NOT affect the other
```

> [!success] Rule
> Every time `outer()` is called, a **new execution context** is created, a **new local memory** is initialised, and therefore a **new independent backpack** is attached to the returned function.

---

## Why Closure Matters — Real World Uses

### 1. Helper Functions: `once`

A function that can only ever be called once — it "remembers" it was already called.

```js
function once(func) {
  let hasBeenCalled = false;
  let result;
  return function(...args) {
    if (!hasBeenCalled) {
      result = func(...args);
      hasBeenCalled = true;
    }
    return result;
  };
}

const onceAdd = once((a, b) => a + b);
onceAdd(3, 4); // 7
onceAdd(10, 20); // still 7 — won't re-run
```

`hasBeenCalled` and `result` live in the **closure backpack** — persisting between calls, invisible from outside.

---

### 2. Helper Functions: `memoize`

Cache the results of expensive operations so they are never computed twice.

```js
function memoize(func) {
  const cache = {};
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache[key] !== undefined) {
      return cache[key];         // return cached result
    }
    cache[key] = func(...args);  // compute and store
    return cache[key];
  };
}

const expensiveSquare = memoize(num => {
  console.log('Computing...');
  return num * num;
});

expensiveSquare(5); // Computing... → 25
expensiveSquare(5); // (no log) → 25 from cache
```

`cache` persists between calls via closure — a **private, persistent data store**.

---

### 3. Module Pattern — Private State

```js
function createCounter() {
  let count = 0;          // private — cannot be accessed directly from outside

  return {
    increment: function() { count++; },
    decrement: function() { count--; },
    getCount:  function() { return count; }
  };
}

const counter = createCounter();
counter.increment();
counter.increment();
counter.getCount(); // 2

// count is NOT accessible directly:
counter.count;       // undefined — it's private!
```

This is the **Module Pattern** — using closure to create **public methods** that operate on **private state**. The foundation of encapsulation in JavaScript.

---

### 4. Iterators

```js
function createIterator(array) {
  let index = 0;                 // ← persists in backpack
  return function() {
    if (index < array.length) {
      return array[index++];
    }
    return 'Done';
  };
}

const iterate = createIterator([10, 20, 30]);
iterate(); // 10
iterate(); // 20
iterate(); // 30
iterate(); // 'Done'
```

`index` persists between calls — closure maintains **state** across invocations without global variables.

---

### 5. Partial Application

Pre-fill some arguments of a function and return a new function.

```js
function multiply(a, b) {
  return a * b;
}

function partiallyApply(func, fixedArg) {
  return function(liveArg) {
    return func(fixedArg, liveArg);
  };
}

const double = partiallyApply(multiply, 2);
double(5);  // 10
double(9);  // 18

const triple = partiallyApply(multiply, 3);
triple(5);  // 15
```

`fixedArg` is stored in the backpack — reused across multiple calls to the returned function.

---

### 6. Asynchronous JavaScript

```js
function fetchData(url) {
  const requestId = Math.random(); // stored in backpack

  fetch(url).then(function(response) {
    // This callback runs LATER — possibly milliseconds, possibly seconds
    // But it still has access to requestId via closure
    console.log(`Request ${requestId} got response:`, response.status);
  });
}
```

When async operations complete (after delays), their callbacks need access to data that was present **when they were created** — not when they finally run. Closure makes this possible.

---

## Mental Models & Analogies

| Concept | Mental Model |
|---------|-------------|
| Closure | A function with a backpack of data from where it was born |
| `[[scope]]` | An invisible chain linking the function back to its birth environment |
| Lexical Scope | Where you *write* the code determines what variables it can *see* |
| Backpack | The persistent variable environment attached to a returned function |
| Individual backpacks | Every call to the outer function creates a new backpack — they are independent |
| Memory persistence | The backpack keeps data alive even after the outer function has returned |

> [!tip] The "Born vs Called" Mental Model
> Always ask two separate questions:
> 1. **Where was this function BORN (defined)?** → determines its backpack/closure
> 2. **Where is this function CALLED?** → determines its execution context
>
> The answer to question 1 determines what variables the function has access to. The answer to question 2 does NOT.

---

## Quick Reference Card

```
CLOSURE — CORE MECHANICS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  DEFAULT (no closure):
  Every function call → brand new local memory
  Function returns   → local memory DELETED ❌

  WITH CLOSURE:
  Inner function defined inside outer function
    └── At definition: inner gets [[scope]] bond to outer's local memory
    └── outer() returns inner function definition
    └── outer's execution context is removed from call stack
    └── BUT outer's variable environment PERSISTS ✅
        └── Attached to inner as a hidden "backpack"
        └── Kept alive because [[scope]] references it
        └── Cannot be garbage collected

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  THE KEY RULE:
  Where a function is DEFINED determines what
  data it has access to — NOT where it is called.

  "Lexical Scoping" or "Static Scoping"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  VARIABLE LOOKUP ORDER (Scope Chain):
  1. Inner function's own LOCAL MEMORY
  2. [[scope]] backpack (outer function's environment)
  3. Global memory
  → First match wins

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  NAMES FOR THE SAME THING:
  "Backpack" = Closure = C.O.V.E. = P.L.S.R.D.
  All refer to the persistent variable environment
  attached to a function via [[scope]] / [[Environment]]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  INDEPENDENCE RULE:
  Each call to outer() creates a brand new:
  ✓ Execution context
  ✓ Local memory
  ✓ [[scope]] backpack
  ✓ Returned function with its OWN independent closure

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  REAL-WORLD USES:
  once()         → remember if already called
  memoize()      → cache expensive results
  Module Pattern → private state + public API
  Iterators      → persistent position/index
  Partial Apply  → pre-fill function arguments
  Async JS       → hold data across time gaps
```
