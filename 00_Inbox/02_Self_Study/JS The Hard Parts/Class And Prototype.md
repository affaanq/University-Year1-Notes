
## ✅ Objects & Encapsulation
Store data and related functions in one place.



```js
const user1 = {
  name: "Will",
  score: 3,
  
  increment: function() { user1.score++; }
};
user1.increment(); // user1.score -> 4
```

> This is **encapsulation**: data + methods together.

---

## ✅ Creating Objects (Different Methods)

### Method 1: Object Literal
```js
const user1 = {
  name: "Will",
  score: 3,
  increment: function() { user1.score++; }
};
```

### Method 2: Empty Object + Dot Notation
```js
const user2 = {};
user2.name = "Tim";
user2.score = 6;
user2.increment = function() {
  user2.score++;
};
```

### Method 3: `Object.create()`
```js
const user3 = Object.create(null);
user3.name = "Eva";
user3.score = 9;
user3.increment = function() {
  user3.score++;
};
```

✅ `Object.create()` gives **fine-grained control** of the prototype chain.

---

## ❌ Why the First Approach is Not Usable (Your Note)
- We recreate the **same function again and again**
- Wastes memory and code
- Adding features requires changing every copy

✅ Solution: **Use `Object.create()` for shared methods**

---

## ✅ Solution 2: Prototype Chain (Shared Method Store)

```js
const userFunctionStore = {
  increment: function(){ this.score++; },
  login: function(){ console.log("Logged in"); }
};

function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore);
  newUser.name = name;
  newUser.score = score;
  return newUser;
}

const user1 = userCreator("Will", 3);
const user2 = userCreator("Tim", 5);
user1.increment();
```

### Key Idea:
If `.increment` isn't found on `user1`, JS looks **up the prototype chain**.

---

## ✅ `__proto__` and `prototype`

### What is `__proto__`?
`__proto__` is a hidden link from an object to its prototype.

✅ Default:
```js
Object.prototype === user1.__proto__
```

> All objects automatically link to `Object.prototype`.

### Why is this useful?
It enables **method sharing** without duplication.

---

## ✅ `hasOwnProperty`

```js
user1.hasOwnProperty('score');
```

`hasOwnProperty` comes from `Object.prototype`.

---

## ✅ Nested Functions and `this`

### Problem (Old JS)
```js
const userFunctionStore = {
  increment: function() {
    function add1(){ this.score++; }
    add1();
  }
};
```

👉 `this` inside `add1` defaults to **global object**.

### Classic Fix: `that = this`
```js
increment: function() {
  const that = this;
  function add1(){ that.score++; }
  add1();
}
```

### Modern Fix: Arrow Function
```js
increment: function() {
  const add1 = () => { this.score++; }
  add1();
}
```

✅ Arrow functions **lexically bind `this`**.

---

## ✅ Why Arrow Functions Should NOT Be Used for Object Methods
If arrow functions are used as methods:
- `this` becomes **lexically scoped**
- Often points to **global scope**
- Breaks intended object context

---

## ✅ The `new` Keyword (Constructor Functions)

### What `new` does automatically:
1. Creates an empty object
2. Sets `this` to that object
3. Links `__proto__` to the function’s `prototype`
4. Returns the object

```js
function userCreator(name, score){
  this.name = name;
  this.score = score;
}
userCreator.prototype.increment = function(){ this.score++; };
userCreator.prototype.login = function(){ console.log("login"); };

const user1 = new userCreator("Eva", 9);
user1.increment();
```

### If you call without `new`:
`this` becomes **global object** → bugs.

---

## ✅ Functions Are Also Objects

```js
function multiplyBy2(num){
  return num * 2;
}
multiplyBy2.stored = 5;
multiplyBy2(3);      // 6
multiplyBy2.stored;  // 5
multiplyBy2.prototype; // {}
```

✅ Every function has a **prototype** property (an object).

---

## ✅ Class Syntax (ES2015) — Syntactic Sugar

```js
class UserCreator {
  constructor (name, score){
    this.name = name;
    this.score = score;
  }
  increment(){ this.score++; }
  login(){ console.log("login"); }
}

const user1 = new UserCreator("Eva", 9);
user1.increment();
```

### Equivalent to:
```js
function userCreator(name, score){
  this.name = name;
  this.score = score;
}
userCreator.prototype.increment = function(){ this.score++; };
userCreator.prototype.login = function(){ console.log("login"); };
```

---

## ✅ Key Interview Notes (Important)
- Most devs **do not understand how prototypes work**
- `class` hides prototype behavior but doesn’t change it
- Knowing prototype chain = **interview advantage**

---

## ✅ Important Concept Definitions (Your Notes Included)

### What is the purpose of `__proto__`?
It links an object to its prototype so JS can look up methods in the chain.

### What is an implicit parameter?
A parameter automatically provided — in JS methods, it is `this`.

### How does `this` enable method reuse?
A single method uses `this` to access the correct object instance.

### What happens to `this` in nested functions (old JS)?
Defaults to the global object.

### How do we fix it?
Use `that = this` or arrow functions.

---

## ✅ Summary: Four Solutions for Object Creation

1. **Object literal** – simple but repetitive
2. **Function factory** – still duplicates methods
3. **Prototype chain** – shared methods via `Object.create()`
4. **Constructor + new / Classes** – automated + cleaner syntax

---
