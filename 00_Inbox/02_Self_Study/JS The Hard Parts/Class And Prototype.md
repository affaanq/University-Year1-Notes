
Method 1:

Why the first approach NoT USabel 

It is fundamentally wrong as it restore the same function again and again..
We will have copy of each of them, we will waste a lot of line of code here.
If we want to add a feature, we will have to add it manually

We can use the Object.create() method to solve this issue 


What is the purpose of the `__proto__` property in JavaScript?
The `__proto__` property provides a hidden link between an object and its prototype, allowing JavaScript to look up methods and properties on the prototype chain when they are not found directly on the object itself.

Using `Object.create()` allows creating objects with an automatic link to another object (prototype), enabling method sharing without duplicating function code across multiple objects.


What does the phrase 'implicit parameter' mean in the context of JavaScript methods?
implicit parameter is a parameter automatically provided by JavaScript without being explicitly defined, in this case referring to the 'this' keyword

How does the 'this' keyword enable method reusability across different objects?
By using 'this', a method can be defined once and used across multiple objects, with 'this' dynamically referring to the specific object the method is called on



What is the default value of the `__proto__` property for all objects in JavaScript?
By default, the `__proto__` property links to `Object.prototype`


What happens to the value of `this` when a nested function is declared inside a method in older JavaScript implementations?
In older JavaScript implementations, when a nested function is declared inside a method, the `this` value defaults to the global window object, not the original object the method was called on.

How did developers traditionally solve the `this` context issue in nested functions?


Developers would use the `that=this` pattern, where they would assign the original object's context to a variable named `that` before the nested function, and then use `that` inside the nested function to reference the original object.

Why should arrow functions not be used for object methods?
If arrow functions are used for object methods, the 'this' binding will be lexically scoped to where the function was defined (typically global), which breaks the intended method context and prevents accessing the correct object properties.

