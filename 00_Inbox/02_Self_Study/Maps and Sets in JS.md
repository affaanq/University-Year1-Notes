
# 🧠 JavaScript Collections: Set, Map, WeakSet, & WeakMap

## 1. The Set Family (Unique Values)

A `Set` is a collection of **unique** values. Unlike an Array, you cannot have duplicate elements.

### **Set** (The Standard)

- **Storage:** Any type (Primitives + Objects).
    
- **Uniqueness:** Uses "Same-value-zero equality" (similar to `===`, but `NaN` is considered equal to `NaN`).
    
- **Iteration:** Fully iterable via `forEach`, `for...of`, `.keys()`, and `.values()`.
    
- **Key Property:** `.size` (returns the count of elements).


> [!important]
> In a `Map`, `set` requires two arguments: a **key** and a **value**.

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
- `Map.prototype.set()` **overwrites** the value if the key already exists. It does not sum or merge them automatically.
    

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

# Practice Project to understand the concept

``` javascript

const ballerina = {

    commonName: "Spanish lavender",

    scientificName: "Lavandula stoechas",

    cultivar: "Ballerina"

}

  

const prettyPolly = {

    commonName: "Spanish lavender",

    scientificName: "Lavandula stoechas",

    cultivar: "Pretty Polly"

}

  

const willowVale = {

    commonName: "Spanish lavender",

    scientificName: "Lavandula stoechas",

    cultivar: "Willow Vale"

}

  

const hidcote = {

    commonName: "English lavender",

    scientificName: "Lavandula angustifolia",

    cultivar: "Hidcote"

}

  

const imperialGem = {

    commonName: "English lavender",

    scientificName: "Lavandula angustifolia",

    cultivar: "Imperial Gem"

}

  

const royalCrown = {

    commonName: "French lavender",

    scientificName: "Lavandula dentata",

    cultivar: "Royal Crown"

}

  

const catalog = new Map();

catalog.set(ballerina, { small: 20, medium: 15, large: 12 });

catalog.set(prettyPolly, { small: 31, medium: 14, large: 24 });

catalog.set(willowVale, { small: 3, medium: 5, large: 0 });

catalog.set(hidcote, { small: 33, medium: 13, large: 18 });

catalog.set(imperialGem, { small: 19, medium: 35, large: 28 });

catalog.set(royalCrown, { small: 40, medium: 22, large: 9 });

  

const sellPlants = (plant, size, potsNo) => {

    if (!catalog.has(plant)) return "Item not found.";

    const name = `${plant.scientificName} '${plant.cultivar}'`

    const pots = catalog.get(plant);

    if (pots[size] - potsNo < 0) {

        return `Not enough ${size} size pots for ${name}. Only ${pots[size]} left.`

    }

    pots[size] -= potsNo;

    return `Catalog successfully updated.`

}

  

const removePlant = plant => catalog.delete(plant);

  

const displayCatalog = () => {

    let catalogString = "";

    catalog.forEach((val, key) => {

        catalogString += `${key.scientificName} '${key.cultivar}': ${val.small} S, ${val.medium} M, ${val.large} L

`

    })

    return catalogString

}

const displayPlantsSet = () => {

    const catalogSet = new Set();

    catalogSet.add(ballerina);

    catalogSet.add(ballerina);

    catalogSet.add(prettyPolly);

    return catalogSet

};

const plantsSet = displayPlantsSet();

console.log(plantsSet);
```

### 🛠 Map Methods Table

- `map.set(key, value)`: Adds or updates.
    
- `map.get(key)`: Retrieves value.
    
- `map.has(key)`: Returns boolean (exists?).
    
- `map.clear()`: Empties the entire Map (size becomes 0).
    

---

## 3. Reference vs. Value (The "Catalog" Trap)

This was your most consistent error: trying to update a catalog by assigning a value to a temporary variable.

> [!danger] The Shadowing Mistake
> 
> **The Fix (Mutation):** You must modify the object property directly so the change persists in the Map.