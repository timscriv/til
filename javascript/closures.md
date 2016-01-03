##Closures
Closure is when a function "remembers" its lexical scope even when the function is executing outside that lexical scope.

```js
function foo(){
    var bar = "bar";
    
    function baz(){
        console.log(bar);
    }
    
    bam(baz);
}

function bam(baz){
    baz();      //"bar"
}

foo();
```
- foo() calls bam() with baz() as a parameter.
- When bam() call baz() that was passed in the lexical scope that is used will be the scope of foo() and not global scope.
- baz() still used the lexical scope of foo().

###Closure Examples
```js
function foo(){
    var bar = "bar";
    
    return function(){
      console.log(bar);
    };
}
function bam(){
    foo()();    //"bar"
    //first set of () gets the returned function, then executes it
} 

bam();
```
```js
function foo(){
    var bar = "bar";
    
    setTimeout(function(){
        //even though this function is not inside the scope it still remembers its lexical scope (called closure)
        console.log(bar);
    },1000);
}

foo();
```

- If you keep a reference around to an object then it cannot be garbage collected until all references are lost.
- If you keep a reference to a function with a closure of the scope then the scope will never go away(garbage collected).
- This can be nested as many times as needed

```js
for(var i = 1; i<=5; i++) {
    setTimeout(function(){
        console.log("i: " + i); //prints 6 five times.
    },i*1000);
}
```
- i is always going to be the parent i.
- i is part of the parent scope(closure)

```js
for(var i = 1; i<=5; i++) {
    (function(i){
        setTimeout(function(){
            console.log("i: " + i); //prints 1,2,3,4,5.
        },i*1000);
    })(i);
}
```
This fixes by creating a new scope for each iteration.

###More Closure Examples

```js
for(let i = 1; i<=5; i++) {
    setTimeout(function(){
        console.log("i: " + i); 
    },i*1000);
}
```
- let will create a brand new i for each iteration

```js
var foo = (function(){
    var o = {bar:"bar"};
    return {obj:o};
})();

console.log(foo.obj.bar); //"bar"
```
This is not an example of closure because there is no function keeping a reference to it's lexical scope.

### Module Patterns

** Classic Module Pattern **

```js
var foo = (function(){

    var o = {bar:"bar"};
    
    return {
      bar: function(){
          console.log(o.bar);
      }  
    };    
})();

foo.bar();  // "bar"
```

Characteristics
1. Must have outer wrapping function that gets executed.
2. Must be 1 or more function that get returned from the outer function with closure over the private scope

** Modern Module Pattern **

```js
define("foo",function(){

    var o = {bar:"bar"};
    
    return {
      bar: function(){
          console.log(o.bar);
      }  
    };    
});
```

** ES6 Module Pattern **

```js
var o = {bar:"bar"};

export function bar() {
    return o.bar;
}
```

With this export we can then `import` into another file.



