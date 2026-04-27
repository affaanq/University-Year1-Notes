
Method 1:

Why the first approach NoT USabel 

It is fundamentally wrong as it restore the same function again and again..
We will have copy of each of them, we will waste a lot of line of code here.
If we want to add a feature, we will have to add it manually

We can use the Object.create() method to solve this issue 


What is the purpose of the `__proto__` property in JavaScript?
The `__proto__` property provides a hidden link between an object and its prototype, allowing JavaScript to look up methods and properties on the prototype chain when they are not found directly on the object itself.

Using `Object.create()` allows creating objects with an automatic link to another object (prototype), enabling method sharing without duplicating function code across multiple objects.