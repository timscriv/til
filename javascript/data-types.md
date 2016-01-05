#JavaScript Data Types
JavaScript has 7 data types, 6 are primative and immutable and Objects.

##Primative Types
**Boolean**
```js
var x = Boolean(true); //true
```
**Null**
```js
var x = null; //x exists but has no type or value
```
**Undefined**
```js
var x = undefined; //x DOES NOT exists, it is NOT defined 
```
**Number**
```js
var x = 123;  //x is 123
var y = Number("123"); //y is 123
```
**String**
```js
var x = "hello";  //x is "hello" string
var y = String("123"); //y is "123" string
```
**Symbol(ES6+)**
Symbols are always unique and immutable.
```js
Symbol("foo") === Symbol("foo"); //false
typeof(Symbol("foo")); //"symbol"
```
##Objects
`Arrays` and `Functions` are both regular objects as well as a few other types. These are going to be passed by reference.
```js
var x = {
	foo: "foo"
};

var y = x;
x.foo = "bar";

console.log(x.foo,y.foo); // bar bar
```

##References
[Mozilla JavaScript data types and data structures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)
[airbnb/javascript](https://github.com/airbnb/javascript)
