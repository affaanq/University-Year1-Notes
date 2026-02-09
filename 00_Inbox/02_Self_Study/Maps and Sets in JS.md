
# 🧠 JavaScript Collections: Set, Map, WeakSet, & WeakMap

## 1. The Set Family (Unique Values)

A `Set` is a collection of **unique** values. Unlike an Array, you cannot have duplicate elements.

### **Set** (The Standard)

- **Storage:** Any type (Primitives + Objects).
    
- **Uniqueness:** Uses "Same-value-zero equality" (similar to `===`, but `NaN` is considered equal to `NaN`).
    
- **Iteration:** Fully iterable via `forEach`, `for...of`, `.keys()`, and `.values()`.
    
- **Key Property:** `.size` (returns the count of elements).
    

JavaScript

```
const treeSet = new Set(['Baobab', 'Jackalberry']);
treeSet.add('Baobab'); // Ignored (duplicate)
console.log(treeSet.size); // 2
```

### **WeakSet** (The Memory-Efficient Variation)

- **Storage:** **Objects and Symbols ONLY.** Primitives (strings, numbers) will throw a `TypeError`.
    
- **Garbage Collection:** References are "weak." If no other part of the code holds a reference to an object in a `WeakSet`, the garbage collector can reclaim it.
    
- **Limitations:** Not iterable, no `.size` property, no `.clear()` method.
    

---

## 2. The Map Family (Key-Value Pairs)

A `Map` is superior to a plain Object when you need sophisticated keys or need to preserve insertion order.

### **Map** (The Heavy Lifter)

- **Keys:** Can be _anything_ (Functions, Objects, Primitives).
    
- **Ordering:** Remembers the original insertion order.
    
- **Performance:** Generally performs better than Objects in scenarios involving frequent additions and removals.
    

JavaScript

```
const myMap = new Map();
const keyObj = { id: 1 };

myMap.set(keyObj, 'Premium User');
console.log(myMap.get(keyObj)); // 'Premium User'
```

### **WeakMap** (The Private Data/Memory Specialist)

- **Keys:** Must be **Objects or Symbols**.
    
- **Use Case:** Ideal for storing "private" data about an object without preventing that object from being deleted from memory.
    
- **Limitations:** Not iterable, no `.size`.
    

---

## 3. Comparison Matrix (The "Cheat Sheet")

|**Feature**|**Set**|**WeakSet**|**Map**|**WeakMap**|
|---|---|---|---|---|
|**Values**|Unique values|Unique Objects/Symbols|Key-Value pairs|Key-Value pairs|
|**Key Type**|N/A|N/A|Any type|Objects/Symbols only|
|**Iterable?**|Yes|**No**|Yes|**No**|
|**Size Prop?**|Yes (`.size`)|**No**|Yes (`.size`)|**No**|
|**GC Support**|No (Strong ref)|**Yes (Weak ref)**|No (Strong ref)|**Yes (Weak ref)**|

---

## 4. Crucial Blind Spots (What you missed)

### **The "Why" of Weak Collections**

In a regular `Map`, if you use an object as a key, that object will **never** be garbage collected as long as the Map exists—even if you delete every other reference to it. This causes **Memory Leaks**. `WeakMap` and `WeakSet` solve this by allowing the garbage collector to do its job.

### **Key Methods Comparison**

- **Set/WeakSet:** Use `.add(value)`
    
- **Map/WeakMap:** Use `.set(key, value)` and `.get(key)`
    

### **The `entries()` Oddity in Sets**

In a `Set`, `.entries()` returns `[value, value]`. This seems redundant, but it exists to keep the API consistent with `Map.entries()`, making it easier to swap data structures in complex algorithms.