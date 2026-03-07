# THE JS do the thret of execution, it do as it goes

# feature 2:  it saved the stuff in the computer memory b it function or anything

## the name of the function is called the identifier

 the execution context by default is global:
 it has two parts:
The local memory which will store the input value or we can say as the argument and the result 

two types of memory in js the locat and the global memory when ewer retur nthe function output, it pushed the output to the global memeory\
the local memeort is the place to stre the function and other temporal dAta

the call stack is the tradiotanl way of storing the data in our computer

the global is always there present, and it is to the bottom..
the three call componenets of js 


the global memory
threat execution whcih looks a function stores its data to  the local memory
and then we have the call stack.


What is the key rule of a stack in programming?

The key rule of a stack is that you are only engaged with the top item on the stack. Whatever is at the top of the call stack is the currently running function or context.



Here are your in-depth, well-structured Obsidian notes covering everything from your slides and your personal notes — formatted for Obsidian with proper markdown, callouts, code blocks, and linked concepts:

---


# 🧠 JavaScript: The Hard Parts — Master Notes


# Part 1 — JavaScript Principles

> [!info] Core Idea
> When JavaScript code runs, it does exactly **two things**:
> 1. **Executes** code line by line (Thread of Execution)
> 2. **Saves** data in memory (Variables, Functions, Objects)

---

## Thread of Execution

JavaScript is a **single-threaded**, **synchronously-executed** language.

- It reads your code **top to bottom, one line at a time**
- It **cannot** multitask on the main thread (natively)
- Each line must finish before the next begins *(unless async, covered later)*

```js
const num = 3;                    // Line 1 — executed first
function multiplyBy2(input) {     // Line 2 — function is defined (saved, not run)
  const result = input * 2;
  return result;
}
const output = multiplyBy2(num);  // Line 3 — function is now invoked/run
const newOutput = multiplyBy2(10);// Line 4 — invoked again with new argument
````

> [!tip] Mental Model
> Think of the thread of execution like a **cursor** moving through your code, line by line. It never jumps ahead, never goes back (unless loops/recursion).

---

## Memory

JavaScript has a **memory store** — it saves data as it encounters it.

| Type                    | Example                        | Stored As                    |
| ----------------------- | ------------------------------ | ---------------------------- |
| Primitive values        | `const num = 3`                | Actual value directly        |
| Arrays                  | `const arr = [1,2,3]`          | Reference to memory address  |
| Objects                 | `const obj = { name: "Will" }` | Reference to memory address  |
| Functions (code itself) | `function multiplyBy2() {...}` | Stored as code, not executed |

> [!important] Key Fact
> **Functions are stored as code** in memory — the instructions are saved but NOT run until the function is called. The name used to refer to the stored function is called the **identifier**.

```js
// "multiplyBy2" is the IDENTIFIER pointing to the stored function code

function multiplyBy2(inputNumber) {
  const result = inputNumber * 2;
  return result;
}
```

### 🔑 Two Types of Memory in JavaScript

| Memory Type       | Scope                        | Lifespan                   | Stores                                 |
| ----------------- | ---------------------------- | -------------------------- | -------------------------------------- |
| **Global Memory** | Accessible everywhere        | Lives entire runtime       | Global vars, function definitions      |
| **Local Memory**  | Only inside current function | Dies when function returns | Parameters, local variables, temp data |

> [!note] Local vs Global
> When a function returns its output, that **return value is pushed out to global memory** (or wherever the return value is being assigned). The **local memory is destroyed** after the function finishes executing.

---

## Functions & Execution Context

### What is an Execution Context?

An **execution context** is a "box" or environment created **every time a function is invoked**. It is the place where:
1. The function's code is executed (thread of execution)
2. Local data is stored (local memory)

> [!info] Two Parts of Every Execution Context
> 1. **Thread of Execution** — runs the code inside the function line by line
> 2. **Local Memory (Variable Environment)** — stores arguments, local variables, intermediate results

```js
function multiplyBy2(inputNumber) {
  // 🔽 LOCAL MEMORY for this execution context:
  // inputNumber = 3  (argument passed in)
  const result = inputNumber * 2;
  // result = 6
  return result;   // 6 is returned to global memory
}

const output = multiplyBy2(3); // output = 6 is now in GLOBAL memory
```

### Execution Context Diagram

```
GLOBAL EXECUTION CONTEXT
├── Global Memory
│   ├── num = 3
│   ├── multiplyBy2 = [function code]
│   └── output = (being evaluated...)
│
└── When multiplyBy2(3) is called:
    └── LOCAL EXECUTION CONTEXT (multiplyBy2)
        ├── Local Memory
        │   ├── inputNumber = 3
        │   └── result = 6
        └── Returns 6 → sent back to global → output = 6
```

> [!tip] Default Context
> The **default execution context** is always the **Global Execution Context** — it exists before any function is called and persists throughout the entire program's runtime.

---

## Call Stack

### What is the Call Stack?

The **call stack** is JavaScript's mechanism to **track which function is currently running** and where to return control after a function finishes.

It follows the **LIFO principle**:

> [!important] Key Rule of a Stack
> **Last In, First Out (LIFO)** — You are ONLY ever engaged with the **top item** on the stack. The function at the top of the call stack is the **currently executing function**.

### How It Works

| Event | Call Stack Action |
|-------|------------------|
| Program starts | `[global]` pushed |
| Function is called | Function context pushed on top |
| Function finishes (`return`) | Function context **popped** off |
| All code done | Only `[global]` remains, then it's popped too |

```js
const num = 3;

function multiplyBy2(inputNumber) {
  const result = inputNumber * 2;
  return result;
}

const output = multiplyBy2(num);    // PUSH multiplyBy2 → runs → POP
const newOutput = multiplyBy2(10);  // PUSH multiplyBy2 → runs → POP
```

### Call Stack Visual Trace

```
Step 1:       Step 2:           Step 3:       Step 4:           Step 5:
──────────    ──────────────    ──────────    ──────────────    ──────────
             │multiplyBy2(3)│              │multiplyBy2(10)│
             ├──────────────┤              ├───────────────┤
│  global  │ │    global    │  │  global  │ │    global     │  │ global │
└──────────┘ └──────────────┘  └──────────┘ └───────────────┘  └────────┘
  (starts)      (running)       (returned)      (running)        (done)
```

### Three Core Components of JavaScript Execution

```
┌──────────--───────---──────────────────────┐
│          JavaScript Runtime Engine         │
│                                            │
│  1. 🧵 Thread of Execution                 |
│     └─ Goes line by line through code      │
│                                            │
│  2. 💾 Memory                              │
│     ├─ Global Memory (always present)      │
│     └─ Local Memory (per function call)    │
│                                            |
│  3. 📚 Call Stack                          │
│     └─ Tracks current execution context    │
│        (Global is always at the bottom)    │
└────────────────────────────────────────────┘
```

> [!success] Summary of Part 1
> JavaScript's engine is powered by three things working together:
> - **Thread of Execution** — reads and runs code line by line
> - **Memory** — stores data (global and local)
> - **Call Stack** — keeps track of where we are (global always at the bottom)

---

# Part 2 — Callbacks & Higher Order Functions

> [!info] Why This Matters
> Callbacks and Higher Order Functions are one of the **most misunderstood but most powerful** patterns in JavaScript. They:
> - Enable `map`, `filter`, `reduce` (functional programming)
> - Make code **declarative** and **DRY**
> - Are the backbone of **asynchronous JavaScript**
> - Are one of the **most tested topics** in mid/senior engineering interviews

---

## Why Functions Exist — DRY Principle

### The Problem: Repetition

```js
// Specific functions — NOT reusable
function tenSquared() {
  return 10 * 10;  // 100
}

function nineSquared() {
  return 9 * 9;  // 81
}

function eightSquared() {
  return 8 * 8;  // 64
}
```

**What principle are we breaking?**

> [!danger] Violation: DRY Principle
> **DRY = Don't Repeat Yourself**
> Every function above does the same logic — just with a different number. Any change to the logic requires changing every single function.

---

## Generalising Functions

The fix: use **parameters** as placeholders so we can pass in the value at call time.

```js
// ✅ Generalised, reusable
function squareNum(num) {
  return num * num;
}

squareNum(10); // 100
squareNum(9);  // 81
squareNum(8);  // 64
squareNum(125); // 15625
```

> [!tip] The Generalisation Principle
> Parameters are **placeholders** — they allow us to defer the decision of *what data* to operate on until the function is actually called.

### Extending Generalisation to Behaviour

What if we want to perform different operations on an array?

```js
// ❌ Three separate functions — breaking DRY again

function copyArrayAndMultiplyBy2(array) {
  const output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(array[i] * 2);
  }
  return output;
}

function copyArrayAndDivideBy2(array) {
  const output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(array[i] / 2);
  }
  return output;
}

function copyArrayAndAdd3(array) {
  const output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(array[i] + 3);
  }
  return output;
}
```

**The fix:** pass the *instruction itself* (a function) as an argument!

```js
// ✅ One generalised function — DRY and reusable

function copyArrayAndManipulate(array, instructions) {
  const output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]));  // run the passed-in function
  }
  return output;
}

function multiplyBy2(input) { return input * 2; }

const result = copyArrayAndManipulate([1, 2, 3], multiplyBy2);
// result = [2, 4, 6]
```

> [!tip] Key Insight
> Just like we generalise *data* with parameters, we can generalise *behaviour* by passing functions as arguments. This is the heart of **higher-order programming**.

---

## First-Class Functions in JavaScript

This power comes from a fundamental property of JavaScript:

> [!important] Functions are First-Class Objects in JavaScript
> Functions can be treated like **any other value** in JavaScript:
> 1. **Assigned** to variables or object properties
> 2. **Passed** as arguments into other functions
> 3. **Returned** as values from other functions

```js
// 1. Assigned to a variable
const multiplyBy2 = function(input) { return input * 2; };

// 2. Passed as an argument
copyArrayAndManipulate([1,2,3], multiplyBy2);

// 3. Returned from a function
function createMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}
const triple = createMultiplier(3);
triple(5); // 15
```

---

## Higher Order Functions

> [!info] Definition
> A **Higher Order Function (HOF)** is any function that either:
> - **Takes in** a function as an argument, OR
> - **Returns** a function as its output
>
> There's nothing inherently special about them — it's just a *label* for functions that do this.

```js
function copyArrayAndManipulate(array, instructions) {  // ← HOF
  const output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]));
  }
  return output;
}
//          ↑
//  This is the Higher Order Function
//  because it takes `instructions` (a function) as a parameter
```

### Real-World Built-In Higher Order Functions

| HOF | What it does |
|-----|-------------|
| `Array.map(fn)` | Copies array, transforms each element with `fn` |
| `Array.filter(fn)` | Copies array, keeps elements where `fn` returns `true` |
| `Array.reduce(fn)` | Reduces array to single value using `fn` |
| `Array.forEach(fn)` | Runs `fn` on each element (no return value) |
| `setTimeout(fn, ms)` | Calls `fn` after delay — async HOF |

---

## Callback Functions

> [!info] Definition
> A **Callback Function** is the function that is **passed into** a Higher Order Function to be called/invoked later.

```js
function copyArrayAndManipulate(array, instructions) {
  const output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]));
  }
  return output;
}

function multiplyBy2(input) { return input * 2; }
//  ↑ This is the CALLBACK FUNCTION
//  It is passed into the HOF and called inside it

const result = copyArrayAndManipulate([1, 2, 3], multiplyBy2);
```

### Labelled Diagram

```
copyArrayAndManipulate([1,2,3], multiplyBy2)
│                               │
│                               └── CALLBACK FUNCTION
│                                   (passed in to be called later)
└── HIGHER ORDER FUNCTION
    (outer function that receives the callback)
```

> [!warning] Common Misconception
> A callback is NOT just a function that runs after another function is done. More precisely, it is **any function passed as an argument to another function**, to be *called back* (invoked) at some point within that function.

---

## Arrow Functions & Anonymous Functions

### Shorthand Syntax Progression

```js
// Full function declaration
function multiplyBy2(input) {
  return input * 2;
}

// Function expression (assigned to variable)
const multiplyBy2 = function(input) {
  return input * 2;
};

// Arrow function with block body
const multiplyBy2 = (input) => {
  return input * 2;
};

// Arrow function with implicit return (concise body)
const multiplyBy2 = (input) => input * 2;

// Arrow function — single param, no parentheses needed
const multiplyBy2 = input => input * 2;

// Usage
const output = multiplyBy2(3); // 6
```

### Using Arrow Functions as Callbacks

```js
function copyArrayAndManipulate(array, instructions) {
  const output = [];
  for (let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]));
  }
  return output;
}

// Named arrow function as callback
const multiplyBy2 = input => input * 2;
const result1 = copyArrayAndManipulate([1, 2, 3], multiplyBy2);

// Anonymous inline arrow function as callback
const result2 = copyArrayAndManipulate([1, 2, 3], input => input * 2);
// ↑ Same result — the function has no name (anonymous), passed directly
```

> [!note] Anonymous Functions
> When you pass `input => input * 2` directly without assigning it to a variable first, it's an **anonymous function** — it has no identifier/name. It's still just a function being passed in as a callback.

> [!warning] Arrow Functions — More Than Syntax Sugar
> For now, arrow functions appear to be "just shorthand." However, they have an important difference: **they do not have their own `this` binding.** This becomes critical in OOP and event handling contexts (covered in Classes & Prototypes section).

---

# Key Mental Models & Analogies

| Concept               | Mental Model                                                                     |
| --------------------- | -------------------------------------------------------------------------------- |
| Thread of Execution   | A cursor moving line by line through your code                                   |
| Global Memory         | A whiteboard that persists for the whole program                                 |
| Local Memory          | A sticky note used during a function, thrown away after                          |
| Execution Context     | A self-contained "room" where a function runs                                    |
| Call Stack            | A stack of plates — you only touch the top one                                   |
| First-Class Function  | A function that can be treated like any other value (variable, argument, return) |
| Higher Order Function | A function factory or a function manager — takes/returns functions               |
| Callback Function     | A function you hand to someone else to call when they need it                    |

---

# Quick Reference Card

```
JAVASCRIPT RUNTIME — THREE CORE COMPONENTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  🧵 THREAD OF EXECUTION
  └─ Reads & runs code line by line (synchronous)

  💾 MEMORY
  ├─ GLOBAL  → persists entire runtime
  └─ LOCAL   → created per function, destroyed on return

  📚 CALL STACK  (LIFO — Last In, First Out)
  └─ Tracks current execution context
     └─ Global always at the bottom
        └─ Rule: only interact with the TOP item

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

FUNCTIONS — KEY FACTS
━━━━━━━━━━━━━━━━━━━━━
  • Defined → stored in memory as code (not run yet)
  • Invoked → new Execution Context created
  • Execution Context has: Thread of Execution + Local Memory
  • Returns → output goes to caller, local memory destroyed

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

HIGHER ORDER FUNCTIONS & CALLBACKS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  HOF     → takes in OR returns a function
  Callback → function passed into a HOF

  copyArrayAndManipulate(array, instructions)
              │                  │
             HOF            Callback fn

  Functions are FIRST-CLASS in JS:
  ✓ Assign to variables
  ✓ Pass as arguments
  ✓ Return from functions

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

DRY PRINCIPLE — DON'T REPEAT YOURSELF
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Generalise DATA     → use parameters
  Generalise BEHAVIOUR → pass functions as arguments
```

---

### 📌 What's in the notes:

| Section | Content |
|---|---|
| **Thread of Execution** | Line-by-line model, mental model callout, code trace |
| **Memory (Global vs Local)** | Table comparison, what gets stored, what gets destroyed |
| **Execution Context** | Deep breakdown with 2-part model, ASCII diagram |
| **Call Stack** | LIFO rule, step-by-step visual trace, your personal note on the key rule |
| **Three Core Components** | Unified diagram of all three working together |
| **DRY & Generalisation** | The full problem→solution arc from the slides |
| **First-Class Functions** | All three properties with code examples |
| **HOF vs Callback** | Clear labelled definition with diagram |
| **Arrow Functions** | Full syntax evolution from declaration → concise arrow |
| **Mental Models Table** | Quick analogies for every concept |
| **Quick Reference Card** | Cheatsheet-style summary of everything |