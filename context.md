## Context
This page will show snippets that demonstrate how context is handled in JavaScript.

### The Basics
The following function will create a local variable and a function which returns the value of the variable. It is useless as it is, because it doesn't have any side effects or return anything. It will simply execute and all objects will be garbage collected.

```javascript
function myFunc() {
    var hello = "hello";
    var getHello = function() {
        return hello;
    }
}

// returns undefined
myFunc();

// returns new object based on the myFunc prototype, which right now would only contain constructor information
var test = new myFunc();
// returns undefined since this is a local variable of the previous object
test.hello;
```

### 'Static Methods using Object Literals
The following function will create and return an object literal with two properties, one a primitive string object and one a function. This could be considered similar to the static methods you would find in OOP languages, such as C# or Java.

```javascript
function getObj() {
    return { 
        hello: "hello", 
        getHello: function() { 
            return hello;
        }  
    }
}

var myObj = getObj(); // you could use the `new` keyword if you like, it will always return the object literal either way
myObj.hello = "hello";
myObj.getHello();
```

### Which `this`?
If you use the `this` keyword when creating a variable in a function, it creates it in the scope of where the function exists in. If you create and run a function in global scope, then `this` refers to the global object. In a web browser, that is the `window` object. In Node, that is simply `global`.

```javascript
window.hello = "hello window";

function MyObj() {
    this.hello = "hello object";
    this.getHello = function() {
        return this.hello;
    }
}

// the variables are created in global context
MyObj();

// returns "hello object"
this.hello; // or window.hello or hello
this.getHello(); // or window.getHello() or getHello()
```

Now lets create a new object with it's own scope using the `new` keyword.

```javascript
var test = new MyObj();

// returns Uncaught ReferenceError: hello is not defined
this.hello;
// returns "hello object"
test.hello;
```
You can explicitly specify the `this` context using the `call` or `apply` function. The following are different ways of doing this:

```javascript
// uses the "this" context from the object instance
console.log(test.getHello());

// more explicitly uses the "this" context from the object instance (pointless to use in practice)
console.log(test.getHello.call(test));

// uses the comma operator, which evaluates 0 and then returns the second function definition 
// to be called from the context of the global object
console.log((0, test.getHello)());

// use the call operator to explicitly set the "this" context to the window object
console.log(test.getHello.call(window));

// all the following will default to the global object
console.log(test.getHello.call());
console.log(test.getHello.call(null));
console.log(test.getHello.call(undefined));
```
