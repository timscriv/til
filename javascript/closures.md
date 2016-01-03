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
        console.log("i: " + i);
    },i*1000);
}
```
   

