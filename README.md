# Javascript-course

*Syntax Parsers*: reads the code and tells what it does and if it's valid (=> translate to the computer)

*Execution contexts* : a wrapper to help manage the code that is running (lots of lexical environment running, which one is currently running)

*Lexical environment* : where something sits physically in the code you write

*name/value pair*: name which maps to a unique value, 1 name to a given context but can be several same name in different context.

*Object* : a collection of name/value pairs

## The global environment & global object:
=> accessible from everywhere.
execution contexts create :
* global object (windows) = in the global context (not inside a function)
* 'this' -> windows
* your code
* outer environment

Execution context => Creation and *Hoisting*

### phase 1 : creation phase
Global Object + this + outer environment + Setup memory space for variables and functions (Hoisting)

*Hoisting* : before the code is executed the engine create in memory all the variables, functions and objects

!!! the variables are created but not assigned so the value is 'undefined' until the code executes. (Never rely on hoisting always call after the definition)

undefined =! from Reference Error
is a special value in JS (if a===undefined) => valid

!!! never do a=undefined

### Phase 2: execution phase => runs the code line by line.

*Single threaded* => one command at the time
*Synchronous Execution* => one at the time and in order of appearance.

Function invocation :running a function, use ()

```javascript
function b() { // b() execution context
}      

function a() {  // a() execution context
  b();          
}           

a(); // Global Execution context
```                                           

!!! Every function create a new execution context!
```javascript
function b(){ // when executed b()EC myVar -> undefined
  var myVar;
}                                               

function a(){ // when executed a()EC : myVar -> 2
  var myVar=2;
  b();
}   

var myVar = 1; // Global EC : myVar -> 1
a();
```
myVar is always different!

```javascript
function b(){
  console.log(myVar);  // 1
}       

function a(){
  var myVar=2;
  b();
}

var myVar = 1;
a();  
```

Here console.log(myVar); display 1 because b execution context does'nt have a definition of myVar so JS will look in the outer Environment.
*The outer environment* is link to where a function is written lexically. Here B is written in the Global Environment so the outer environment is going to myVar=1;
This is the *scope chain*.

*Scope*: where a variable is available in your code

*Asynchronous callback*: More than one at the time

###How the browser works:
In the browser there is *Rendering engine* + *JS Engine* + *HTTP Request*

*Execution context stack* : Global --> a() --> b()
*Event Queue* : Click --> HTTP Request --> ...

When the stack is done go to look to the event queue and start a new stack.

*Dynamic typing*: You don't tell the type if data a variable hold

*Primitives types* : a type that represents a single value and not an object (which is a collection) : `undefined`, `null`, `boolean`, `number`, `string`, `symbol`(es6)

*Operator precedence and association* : priority and which sense to associate (from the left or the right)

*Coercion*: Converting a value from on type to another

`undefined`, `null`, `''`, `0` will return false as boolean coercion

### Objects and functions
*function* : *properties*(primitives or objects) + *methods*(function)

*object literal*: `var person = {};`

####!!!!! Functions are object
so everything we can do with object we can do with function + invocable()

*Function expressions* : a unit of code that results in a value => `var a = 3` or `a===3`

*Function statements* : `if(exp)`
```javascript
function greet(){  
  console.log('hi');  
} // statement, doesn't result in a value

// function expression result in a value because anonymousGreet(); returns 'hi' (here // we have an anonymous function)
var anonymousGreet = function() {
  console.log('hi');  
}
```

### Functional programming
```javascript
// function expression pass to a function because functions are objects
function log(a){
  a();
}

log(function(){
    console.log('hi')     
});
```

### JS memory
A *primitive is pass by Value* and *object are pass by Reference* except when a new Object is assign to a variable (reference means no copy of the object point to the same spot in memory)

### `this`
During phase 1 when execution context is created the `this` key word is created pointing to different object depending on how the function is invoked.
Everytimes a function is invoked a new Environment context is created.

```javascript
function foo(){
  console.log(this);
}

var bar = function(){
  console.log(this);
}

foo();  //windows
bar();   //windows
```

Here 3 execution context are created so 3 `this` but there are pointing to the same spot because `foo` and `bar` are called from the global context (`this` -> windows)

```javascript
var c = {
   name:'huhu',
   log: function(){
      console.log(this);  
   }
}

c.log() // Object {name: "huhu"}
```

Because `log()` which contains `this` is called from `c` execution context so `this`-> `c`

```javascript
var c = {
   name:'huhu',
   log: function(){
      var setName = function(newName){
          this.name = newName; //undefined in c and global context
      }
      setName('haha');
      console.log(this);
   }
}

c.log();  // Object {name: "huhu"} and no Object {name: "haha"} why?
```
Because the `this` in the `setName` point to the global context and not from `c`.

A pattern to go around is to do ```var self = this;``` and then use `self.name`

###Arrays are collection of anything
```
var arr=[];
```

During Phase 1 creation, as well, of `arguments` (arrays of parameters passed to a function, keyword in JS)

```javascript
function greet(firstname, lastname, langauge){
    console.log(arguments) // [values of the arguments in an array]
}
```

### IIFE : Immediately Invoked Function Expression

```javascript

// function statement
function greet(name){
  ...
}

greet('huhu');
```

```javascript
// Function expression (put in memory on the fly during execution)
var greet = function() {
  ...
}

greet();
```

```javascript
// IIFE invoke the function immediately after defining
var greet = function() {
  ...
}();

// anonymous IIFE
(function(name){
   var greeting ='hi';
    console.log(greeting + name);
}());
```

without `()` around the function doens't compile (trick the syntax parser)
#### It is very powerful to encapsulate variable, here `greeting` is outside the global context so the global context won't be interfered this code.

#### Pattern to use the global context inside your code without interfering.

```javascript
// windows is the global object from the browser environment

(function (global,...){
      ...
}(windows,...))
```

### Closure:

```javascript
function greet(whatToSay){
  return function(name){  //return a function
    console.log(whatToSay + name);
  }
}

greet('hi')('tony');

var sayHi = greet('hi');
sayHi('Tony');    // ????
```
How does `sayHi` knows the value of `whatToSay` here?
=> because of *closure*. It's a feature of JS which allow to do nice patterns

## TODO put schema closure here

```javascript
function buildFunctions(){
  var arr[];
  for (var i=0; i<3;i++){
    arr.push(function(){  // we create a function object but don't execute it
      console.log(i);
    })
  }
  return arr;
}

// create EC buildFunctions() with i=3 and arr=[function, function, function]
// once execution done remove EC buildFunctions() but keep variables (closure)
var fs = buildFunctions();

// look for i in the scope chain which is the outer environment created by EC
// buildFunctions() where i=3
fs[0](); // 3  
fs[1](); // 3
fs[2](); // 3

```

It is like asking children the age of their parents. They won't awnser relative to their own age but they will anwser with the context of their parents which will be the same for all the children.

To fix that :
``` let j = i;  
```

ES6 creates a new memory spot for j everytimes is called.

Or called the function immediately with IIFE

### Function Factory and Closure

```javascript
function makeGreeting(language){ //Factory
  return function(firstName, lastName){
    if (language==='en'){
      console.log('Hello');
    }
    if (language==='es'){
      console.log('Hola');
    }
    console.log(firstName + ' ' + lastName);
  }
}

var greetEnglish = makeGreeting('en'); // one EC with language = 'en'
var greetSpanish = makeGreeting('es'); // one EC with language = 'es'

greetEnglish('john', 'doe'); // Hello john doe
greetSpanish('john', 'doe'); // Hola john doe
```

### Closures and callback
Callback function : a function you give to another function to be run when the other function finishes

So the function you call (invoke) call back by calling the function you gave it when it finishes.

``` function tellMeWhenDone(callback){
  // some code
  callback();
}

tellMeWhenDone(function () {
  console.log('huhu');
  })
```

### call(), apply(), bind(): control this

function :
1. code
2. name (optional)
3. call()
4. apply()
5. bind()

```javascript
var person = {
  firstName = 'John',
  lastName = 'Doe',
  getFullName: function(){
    var fullName = this.firstName + ' ' + this.lastName;
    return fullName;
  }
}

var logName = function(greeting){
  console.log(greeting + this.getFullName()) // won't work of course
}.bind(person) // now will works

var logPersonName = logName.bind(person); //will work, creates a copy of this

logName(); //without the bind => error
logPersonName(); //works

logName.call(person, param...); //call control who is this and executes

logName.apply(person, [...]); //like call but uses an array
```

### function Borrowing
```javascript
person2 = {...}
person.getFullName.apply(person2);
```

### function Currying
```javascript
function multiply(a, b){
  return a*b;
}

var multiplyByTwo = multiply.bind(this, 2); //a=2

multiplyByTwo(4); // 8, b=4
```

*Function Currying* Create a copy of a function but with some preset parameters.

###Functional programming
```javascript
var arr1=[1,2,3];
var arr2=[];

for (var i=0, i<arr1, i++){
  arr2.push(arr1[i]*2);
}

function mapForEach(arr, fn){
  var new Arr=[];
  for (...){
    newArr.push(fn(arr1));
  }
}

var arr2= mapForEach(arr1, function(item){
  return item*2;
})

```
Build cleaner code,
Focus on minimum responsabilty for each function

```javascript
var checkPastLimit = function(limiter, item){ // 2 arguments but fn just accept 1
  return item > limiter;
}

var arr2 = mapForEach(arr1, checkPastLimit.bind(this,1));
// good but need to use bind

var checkPastLimitsimplified= function (limiter){
  return function (limiter, item){ // returns a function. creates a function object but
    return item > limiter;         // don't execute!!!
  }.bind(this, limiter); //currying, preset parameter
}

var arr3 = mapForEach(arr1, checkPastLimitsimplified(2));
```

### Pattern : How to give functions to functions, how to return functions object. Split code in more smaller part.

### The tiniest function must try not to mutate data better to return something new

### Object oriented JS & Prototypal Inheritance
Inheritance: One object get access to the properties and methods of another object

Prototypal Inheritance: flexible, extensible, easy to understand

### Everything is Js is an Object (or a Primitive)

### How to set prototype?
Function
-> code
-> name
-> `.prototype` // always use with `new` or as function constructor

```javascript

var john = new Person();

Person.prototype.getFullName = function(){ // point to `--proto--`
  ...
}

```

usually `properties` in the object and `method` in the `.prototype`
for efficiency reason (less memory used) => only need one method for all the objects created.

### !!! Never forgot new with function constructor because it's  function that returns nothing so the call of the function will be undefined

Possible to add features to s build-in to enhance JS :
```javascript
String.prototype.isLenghtGreaterThan = function (){
  ...
}
```   
