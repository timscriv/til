# advanced-javascript
My notes on Advanced JavaScript video.
The "What you need to know" Parts

##Books
- High Performance JavaScript - Nicholas C. Zakas
- JavaScript Patterns

##Links
- https://developer.mozilla.org/en-US/docs/JavaScript
- https://github.com/rwldrn/idiomatic.js
    - Good place to start for a styleguide
- [ES5 Spec](http://www.ecma-international.org/ecma-262/5.1)
- http://wiki.ecmascript.org/doku.php?id=harmony:proposals
    - future of ecmascript spec

##Agenda
- Scope, Closure
    - Nested Scope
    - Hoisting
    - this 
    - Closure

##Scope
Where to look for things

Myth: JS is not a compiled language    
Fact: JS is a compliled language, but not the same as C#, we do not send out the binary form of JS.  
Fact: It is compiled everytime it is run.

- JavaScript has function scope only* 

```js
var foo = "bar";

function bar() {
    var foo = "baz";
}

function baz(foo) {
    foo = "bam";
    bam = "yay";
}
```
- Compiler phase finds variable/function declarations then passes through again to do execution.
- The function declarations will JIT compile and not on the first pass.

1. Line 1: Declare variable foo in global scope
2. Line 3: Declare function bar in global scope
3. Line 4: Declare variable foo in function bar scope
4. Back in global scope
5. Line 7: Declare function baz and compile baz function
6. Line 7: implicitly declare foo in arguments in baz scope
7. Done "compiling" 
8. Start "executing"
9. Ask the global scope for variable called foo.
10. Then set "bar" to foo.
11. Pretend we are executing bar()
12. Ask scope manager for LHS foo and it will return function bar scope foo.
13. Set foo to "baz".
14. Pretend to execute baz();
15. get foo variable from local parameter.
16. ask for bam variable and scope manager says go to next outer scope.
17. ask for bam var in the global scope, and the global scope will create it for you. (strict mode will not create a new variable)


###Terminology
- LHS/RHS - Left hand side and right hand side (of the =)
    - LHS is target, RHS is source
    
    
```js
var foo = "bar";

function bar(){
    var foo = "baz";
    
    function baz(foo){
        foo = "bam";
        bam = "yay"; //will create and use global bam
    }
    baz();
    
}

bar();
foo;        //output "bar"
bam;        //output "yay"
baz();      //throw a reference error
```

```js
var foo = function bar(){
    var foo = "baz";
    
    function baz(foo){
        foo = bar;
        foo; //function...
    }
    baz();
};

foo();
bar(); //Error! 

```

- foo() is not a function declaration. function would have to be the first word in the statement
- Named function expressions better because 
    1. anonymous function cannot refer to themselves (no recursion, or whatever)
    2. anonymous function debugging cannot name the function in the stack traces 
    3. makes the code a bit more readable
    
```js
var foo;

try{
    foo.length;
}
catch(err){ //is block scope like a function
    console.log(err); //TypeError
}

console.log(err); //ReferenceError
```    

- lexical scope means compile-time scope. no new scopes can be created outside of compile time.
- scopes are strictly nested inside.


###Cheating lexical scope: eval
- eval, takes any string and treats it as code.

```js
var bar = "bar";

function foo(str){
    eval(str); //cheating!
    console.log(bar); //42
}
foo("var bar = 42;");
```
- Cannot do any of the optimizations, just by having eval present.

```js
var obj = {
    a:2,
    b:3,
    c:4
};

obj.a = obj.b + obj.c;
obj.c = obj.b + obj.a;

with(obj){
    a = b + c;
    c = b - a;
    d = 3; // -- goes to global scope.
}

obj.d; // undefined
d; //3
```
- Creates a brand new lexical scope at runtime
- turns off optimizations

###IIFE Pattern

```js
var foo = "foo";

(function (){//open params will stop it from being a declaration
    var foo = "foo2";
    console.log(foo); // foo2
})(); // we can pass values like (foo) 

console.log(foo);   //foo
```

- IIFE will add a new scope to hide inner workings

###Block scope is ES6
- let keyword in ES6+ is block scope, like attaching directly to a for loop

```js
function foo(){
    var bar = "bar";
    for (let i=0; i<bar.length;i++){
        console.log(bar.charAt(i));
    }
    console.log(i); //ReferenceError
}

foo();
```
- Block scope is basically any {}
- In older js it would be closely equivalent to create a variable that has never been created before, but the garbage collection would happen at a different time.

```js
function foo(bar){
    if(bar){
        let baz = bar;
        if(baz){
            let bam = baz;
        }
        console.log(bam); //Error
    }
    console.log(baz); //Error
}

foo("bar");

```

###Problems with the Let Keyword
- let keyword does not hoist, even in the block scope

###Quiz: Scoping
1. What types of scoping rule(s) does JavaScript have? Exceptions?
    - Lexical scope. Can cheat with 'eval()' and 'with{}'
2. What are the different ways you can create a new scope?
    - Functions, catch{}, (es6) curly braces with let keyword inside.
3. Difference between undeclared and undefined?
    - Undeclared has never been declared but undefined means it doesn't currently have a value.

###Hoisting
```js
a;          //???
b;          //???
var a = b;
var b = 2;
b;          // 2
a;          //???
```
translates to this in the compiler
```js
var a;
var b;
a;      //???
b;      //???
a = b;
b = 2;
b;      //2
a;      //???
```
Nothing is actually moved to the top but handled in the compile phase and not execute phase.

```js
var a = b();    //will work
var c = d();    //will error    
a;
c;

function b(){ //function declaration will be hoisted
    return c;
}

var d = function(){ //function expression will not be hoisted
    return b();
};
```
translates to 
```js
function b(){ //functions will be hoisted above the variables
    return c;
}
var a;
var c;
var d;
a = b();
c = d();
a;
c;
d = function(){
    return b();
}
```

```js
foo(); //foo

var foo = 2;

function foo(){
    console.log("bar");
}

function foo(){
    console.log("foo");
}
```
Why does JS do this hoisting?
- mutual recursion would be impossible.  
Example:

```js
a(1):

function a(foo){
    if(foo > 20) return foo;
    return b(foo + 2);
}
function b(foo){
    return c(foo) + 1;
}
function c(foo){
    return a(foo*2);
}
```
This example would not be possible without recursion because one of the functions would not be declared.

###this Keyword
Every function, while executing, has a referece to its current execution context, called this.

```js
function foo(){
    console.log(this.bar);
}
var bar = "bar1";
var o2 = {bar:"bar2",foo:foo};
var o3 = {bar:"bar3",foo:foo};

foo();      //bar1 (default binding rule)
o2.foo();   //bar2 (implicit binding rule)
o3.foo();   //bar3
```

**4 Rules of this  **

1. Default binding rule - "strict mode"(this defaults to undefined) not strict means default to current context
2. Implicit binding rule - o2.foo then this keyword will point to o2 
3. Explicit binding rule - if you use .call or .apply they take a thisArg as the first argument
4. the new keyword binding 

**this binding Rules in order  **

1. Was the function called with `new`?
2. Was the function called with `call` or `apply` specifting an explicit `this`?
3. Was the function called vie a containing/owning object(context)?
4. DEFAULT: global object (except strict mode)

###Binding Confusion

```js
function foo(){
    var bar = "bar1";
    baz();
}
function baz(){
    console.log(this.bar);
}

var bar = "bar2";
foo();  // ???
```
Lexical scope and 'this' are fundamentally different

###Explicit Binding
Hard binding gives us a way to predictably call the this keyword
```js
function foo(){
    console.log(this.bar);
}

var obj = {bar:"bar"};
var obj2 = {bar:"bar2"};

var orig = foo;
foo= function(){orig.call(obj);};

foo();          // "bar"
foo.call(obj2); // "bar"
```
We could make this more robust by creating a bind utility that will bind this to the function
```js
function bind(fn,o){
    return function(){
        fn.call(o);
    };
}
function foo(){
    console.log(this.bar);
}

var obj = {bar:"bar"};
var obj2 = {bar:"bar2"};

foo = bind(foo,obj);

foo();          //bar
foo.call(obj2); //bar

```
Still kind of clunky that we have this on the scope and could make it easier by attaching to prototype

```js
if(!Function.prototype.bind2){
    Function.prototype.bind2 = 
        function(o) {
            var fn = this; // the function
            return function(){
                fn.apply(o,arguments); //use apply so I can add arguments
            };
        };
}
function foo(baz){
    console.log(this.bar + " " + baz);
}

var obj = {bar:"bar");
foo = foo.bind2(obj);

foo("baz");     //"bar baz"
```
Es5 actually build this in using .bind
- there is a polyfill that is built on the mozilla developer network

###The new Keyword

```js
function foo() {
    this.baz = "baz";
    console.log(this.bar + " " + baz);
}

var bar = "bar";
var baz = new foo(); //undefined undefined
baz.baz; //baz
```
- new Keyword changes a function call to a constructor call

**4 this that new keyword does**  

1. brand new empty object will be created
2. obj gets linked* to a different object
3. bound as the 'this' keyword for the function call
4. if the object does not return then it will implicitly return this;
