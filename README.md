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
```  

var anonymousGreet = function() { console.log('hi');  }  => function expression result in a value because anonymousGreet(); returns 'hi' (here we have an anonymous function)

function log(a){
     a();
}

log(function(){
    console.log('hi')     // function expression pass to a function because functions                                   //  are objects
});

This is functional programming

A primitive is pass by Value and object are pass by Reference except when a new Object is assign to a variable (reference means no copy of the object point to the same spot in memory)

During phase 1 when execution context is created the 'this' key word is created pointing to different object depending on how the function is invoked.
Everytimes a function is invoked a new Environment context is created.js

function foo(){
   console.log(this);
}

var bar = function(){
   console.log(this);
}

foo();  //windows
bar();   //windows

Here 3 execution context are created so 3 this but there are pointing to the same spot because foo and bar are called from the global context (this -> windows)

var c = {
   name:'huhu',
   log: function(){
      console.log(this);
   }
}

c.log() // Object {name: "huhu"} because log() which contains this is called from c execution context so this-> c

var c = {
   name:'huhu',
   log: function(){
      var setname = function(newName){
          this.name = newName; //undefined in c and global context
      }
      setname('haha');
      console.log(this);
   }
}

c.log();  // Object {name: "huhu"} and no Object {name: "haha"} why?
Because the this in the setname point to the global context and not from c.

A pattern to go around is to do var self = this; and then use self.name

Arrays are collection of anything
var arr=[];

During Phase 1 creation as well of 'arguments' (arrays of parameters passed to a function, keyword in JS)

function greet(firstname, lastname, langauge){
    console.log(arguments) // [values of the arguments in an array]
}

IIFE : Immediately Invoked Function Expression

function greet(name){ ... }   //Function statement
greet('huhu');

var greet = function() { ... }  // Function expression (put in memory on the fly                                                     // during execution)
greet();


var greet = function() {...} (); // IIFE invoke the function immediately after defining

(function(name){
   var greeting ='hi';
    console.log(greeting + name);
}());

without () around the function doens't compile (trick the syntax parser)
It is very powerful to encapsulate variable, here greeting is outside the global context so the global context won't be interfered by other code.
Pattern to use the global context inside your code without interfering.

(function (global,...){
      ...
}(windows,...))

Closure:

function greet(whatToSay){   //return a function
     return function(name){
               console.log(whatToSay + name);
      }
}

greet('hi')('tony');

var sayHi = greet('hi');
sayHi('Tony');    // How does sayHi knows the value of whatToSay here?

=> because of closure. It's a feature of JS which allow to do nice patterns

/* TODO put schema closure here  */
