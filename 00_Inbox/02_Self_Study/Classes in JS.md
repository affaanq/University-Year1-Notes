# JavaScript Classes: The Blueprint Pattern

**Tags:** #JavaScript #OOP #Classes #ES6 #ProgrammingParadigms **Related:** [[JavaScript Objects]], [[Prototypes]], [[This Keyword]]

## 1. Core Concept: The Blueprint

**Definition:** Classes in JavaScript are templates (blueprints) for creating objects. They encapsulate **data** (properties) and **behavior** (methods) into a single logical unit.

> [!NOTE] Strategic Insight: Why use Classes? JavaScript is prototype-based, but Classes provide a cleaner syntax (syntactic sugar) for object-oriented programming. They allow you to generate multiple objects with the same structure without manually duplicating code.

### Syntax Structure

``` js
class ClassName {
  constructor() { ... } // Initialization
  method1() { ... }     // Behavior
  method2() { ... }     // Behavior
}
```

## 2. The Constructor: Initialization

The `constructor` is a specialized method that runs **automatically** and **immediately** when you create a new instance using the `new` keyword.

**Purpose:** To set up the initial state (properties) of the object.

``` js
class Dog {
  constructor(name) {
    // 'this' refers to the new object being created
    this.name = name; 
  }

  bark() {
    console.log(`${this.name} says woof!`);
  }
}
```

> [!DANGER] Critical Rule A class can only have **one** method named `constructor`. If you define more than one, JavaScript will throw a `SyntaxError`.

## 3. The `this` Keyword: Contextual Reference

The `this` keyword is the connection between the generic blueprint and the specific object.

- **Inside a class:** `this` refers to the **current instance** of the class.
    
- **Why it matters:** It allows methods to access and modify the data specific to _that_ particular object, ensuring the blueprint works for _any_ instance.
    

``` js
class Dessert {
  constructor(name, price) {
    this.name = name;   // Assigns argument 'name' to the object's 'name' property
    this.price = price;
  }

  showPrice() {
    // 'this' ensures we get THIS dessert's price, not another's
    console.log(`The price of ${this.name} is $${this.price}.`);
  }
}

const brownie = new Dessert("Brownie", 5.99);
brownie.showPrice(); // Output: The price of Brownie is $5.99.
```

## 4. Instantiation: Bringing the Blueprint to Life

Defining a class does nothing on its own. You must create an **instance** of it.

**The `new` Keyword:**

1. Creates a new empty object.
    
2. Sets the value of `this` to point to that new object.
    
3. Calls the `constructor` method.
    
4. Returns the new object.
    

``` js
// 'dog' is an instance of the Dog class
const dog = new Dog("Gino"); 

console.log(dog.name); // Accessing property: "Gino"
dog.bark();            // Invoking method: "Gino says woof!"
```

## 5. Inheritance: The "Don't Repeat Yourself" (DRY) Mechanism

Inheritance allows a **Child Class** (Subclass) to derive properties and methods from a **Parent Class** (Superclass).

**Key Keywords:**

- `extends`: Establishes the parent-child relationship.
    
- `super`: Calls functions on the parent's prototype.
    

### The Mechanism

``` js
// Parent Class (Superclass)
class Vehicle {
  constructor(brand, year) {
    this.brand = brand;
    this.year = year;
  }
}

// Child Class (Subclass)
class Car extends Vehicle {
  honk() {
    console.log("Honk! Honk!");
  }
}

let myCar = new Car("Toyota", 2019);
console.log(myCar.brand); // Inherited property
myCar.honk();             // Child-specific method
```

### The `super()` Constructor

If the child class has its own constructor, you **must** call `super()` before using `this`.

> [!ERROR] Fatal Error If you try to access `this` in a child constructor _before_ calling `super()`, JavaScript calls a ReferenceError. The parent must finish its setup before the child can add to it.

``` js
class Car extends Vehicle {
  constructor(brand, year, numDoors) {
    // 1. Call parent constructor first
    super(brand, year); 
    
    // 2. Now allowed to use 'this'
    this.numDoors = numDoors; 
  }
}
```

## 6. Static Methods & Properties: Class-Level Logic

**Definition:** Static members belong to the **class itself**, not to the instances created from the class.

**Use Cases:**

1. **Utility Functions:** Helpers that don't need object data (e.g., comparing two objects).
    
2. **Configuration:** Fixed constants (e.g., `numberOfWheels`).
    
3. **Factory Methods:** Advanced methods to create instances in specific ways.
    

### Static Syntax

``` js
class Movie {
  constructor(title, rating) {
    this.title = title;
    this.rating = rating;
  }

  // Utility Method: Compares two separate instances
  static compareMovies(movieA, movieB) {
    if (movieA.rating > movieB.rating) return `${movieA.title} wins`;
    return `${movieB.title} wins`;
  }
}

// Usage: Called on the CLASS, not the object
Movie.compareMovies(obj1, obj2); 
// obj1.compareMovies(obj2); // <-- THIS WOULD ERROR
```

### Factory Method Pattern (Advanced)

Using `this` inside a static method refers to the **Class Constructor** itself, allowing you to generate new objects dynamically.

``` js
class Pizza {
  constructor(type, price) {
    this.type = type;
    this.price = price;
  }

  // Factory: Standardizes how a Margherita is made
  static createMargherita() {
    return new this("Margherita", 6.99); // equivalent to new Pizza(...)
  }
}

const lunch = Pizza.createMargherita();
console.log(lunch.type); // "Margherita"
```

## 7. Class Expressions

Classes can be anonymous and assigned to variables, similar to function expressions. This is less common but valid.

``` js
const Rectangle = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
```

## Summary Checklist

- [ ] **Constructor:** Runs once on `new`. Initializes properties.
    
- [ ] **Methods:** Define behavior. Shared by all instances.
    
- [ ] **`this`:** Points to the specific object instance.
    
- [ ] **Inheritance:** `extends` links classes; `super()` links constructors.
    
- [ ] **Static:** Belongs to the blueprint, not the object.