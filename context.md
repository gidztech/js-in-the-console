## Context
This page will show snippets that demonstrate how context is handled in JavaScript.

### Which `this`?

```javascript
window.hello = "hello window";

function MyObj() {
    this.hello = "hello object";
    this.getHello = function() {
        return this.hello;
    }
}

var test = new MyObj();

// Note: The global object in a browser is `window`, but in Node this would just be `global`.

// uses the "this" context from the object instance
console.log(test.getHello());

// more explicitly uses the "this" context from the object instance (pointless to use in practice)
console.log(test.getHello.call(test));

// uses the comma operator, which evaluates 0 and then returns the second function definition to be called from the context of the global object
console.log((0, test.getHello)());

// use the call operator to explicitly set the "this" context to the window object
console.log(test.getHello.call(window));

// all the following will default to the global object
console.log(test.getHello.call());
console.log(test.getHello.call(null));
console.log(test.getHello.call(undefined));

```
