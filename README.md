## Get Started

I will write some demo to show how prototype work in Javascript.

## Object

First, All objects in `javascript` are extends from `Object`, So if I declare an object varible:

```js
let Peter = {
  name: "peterroe",
  age: 21,
  getIntro() {
    return `My name is ${this.name}, I am ${this.age} years old`
  }
}
```

The data structure maybe like this:

```mermaid
classDiagram
  ObjectPrototype <|-- Peter: __proto__
  ObjectPrototype <|-- Object: prototype
  ObjectPrototype --|> Object: constructor
  Peter <-- Object: new Object()
  class ObjectPrototype {
    constructor
    toString()
    valueOf()
    hasOwnProperty()
    ...()
  }
  class Peter {
    name
    age
    __proto__
    getIntro()
  }
  class Object {
    prototype
    keys()
    values()
    entries()
    ...()
  }
```

So you can deduce:

```js
let o = {} // equal: let o = new Object()
Peter.__proto === Object.prototype
Object.prototype.contructor ===  Object
```

The above shows the core of `Javascript Prototype`.

You just to remember: **Once you create an object, which is already in protype chain, you can access Object along the chain.**

## Constructor

Sometimes we declare an Object throught invoke consructor. Such as:

```js
function Persion(name, age) {
  this.name = name
  this.age = age
}
Persion.prototype.getInfo = function() {
  return `My name is ${this.name}, I am ${this.age} years old`
}
let P = new Person('peterroe', 21)
```

Let's see what happend:

```mermaid
classDiagram
  ObjectPrototype <|-- Object: prototype
  ObjectPrototype --|> Object: constructor
  PersonPrototype --|> ObjectPrototype: __proto__
  Person --|> PersonPrototype: prototype
  Person <|-- PersonPrototype: constructor
  Person --> P: new Person()
  P --|> PersonPrototype: __proto__
  class ObjectPrototype {
    constructor
    toString()
    valueOf()
    hasOwnProperty()
    ...()
  }
  class Object {
    prototype
    keys()
    values()
    entries()
    ...()
  }
  class Person {
    +new()
  }
  class PersonPrototype {
    getIntro()
  }
  class P {
    name
    age
  }
```

But since functions can be declared in the form of `new Function()`, so actually their relationship will more complex:

```mermaid
classDiagram
  ObjectPrototype <|-- Object: prototype
  ObjectPrototype --|> Object: constructor
  PersonPrototype --|> ObjectPrototype: __proto__
  Person --|> PersonPrototype: prototype
  Person <|-- PersonPrototype: constructor
  Person --> P: new Person()
  Person --|> FunctionPrototype: __proto__
  Function --> Person: new Function()
  Function --|> FunctionPrototype: prototype
  Function <|-- FunctionPrototype: constructor
  FunctionPrototype --|> ObjectPrototype: __proto__
  P --|> PersonPrototype: __proto__
  class ObjectPrototype {
    constructor
    toString()
    valueOf()
    hasOwnProperty()
    ...()
  }
  class Object {
    prototype
    keys()
    values()
    entries()
    ...()
  }
  class Function {
  
  }
  class FunctionPrototype {

  }
  class Person {
    +new()
  }
  class PersonPrototype {
    getIntro()
  }
  class P {
    name
    age
  }
```

## Built-in constructor

Some constructors are build in Javascript: `Date, RegExp` or `Symbol, BigInt`, they are all constructors similar to the  above.

let's instantiate `RegExp`, others are the same.

```js
let D = new Date()
let R = new RegExp()
let B = BigInt(2E+80)
let S = Symbol()
```

Built-in constructors are almost equivalent to their own declarations:

```mermaid
classDiagram
  ObjectPrototype <|-- Object: prototype
  ObjectPrototype --|> Object: constructor
  RegExpPrototype --|> ObjectPrototype: __proto__
  RegExp --|> RegExpPrototype: prototype
  RegExp <|-- RegExpPrototype: constructor
  RegExp --> R: new RegExp()
  RegExp --|> FunctionPrototype: __proto__
  R --|> RegExpPrototype: __proto__
  Function --> RegExp: new Function()
  Function --|> FunctionPrototype: prototype
  Function <|-- FunctionPrototype: constructor
  FunctionPrototype --|> ObjectPrototype: __proto__
  class ObjectPrototype {

  }
  class Object {

  }
  class Function {

  }
  class FunctionPrototype {

  }
  class RegExp {
    +new()
  }
  class RegExpPrototype {
    dotAll
    exec()
    test()
    ...()
  }
  class R{

  }
```

## Class

`class` comes from `ES6`, Which help us buit complex relationship easily.

Let consider a complex example to see how `class` work

```js
class Person {
  construcor(name) {
    this.name = name
  }
  static str = "world"        //static property
  static greet() {            //static method
    console.log("Hello" + this.foo)
  }
  getName() {
    return this.name
  }
}
let P = new Person()
Person.str     //=> "world"
Person.greet() //=> "Hello world"
```

Structure:

```mermaid
classDiagram
  PersonPrototype --|> ObjectPrototype: __proto__
  Person --|> PersonPrototype: prototype
  Person <|-- PersonPrototype: constructor
  Person --> P: new Person()
  P --|> PersonPrototype: __proto__
  class ObjectPrototype {

  }
  class Person {
    str
    greet()
    +new()
  }
  class PersonPrototype {
    getName()
  }
  class P {
    name
  }
```


## Class Extends

`Extends` is also comes from `ES6`, it allows us to build inheritance relationships more easily.

let think a easily demo:

```js
class Father {}

class Son extends Father {}

let F = new Father()
let S = new Son()
```

let's draw what happed above?

```mermaid
classDiagram
  FatherPrototype --|> ObjectPrototype: __proto__
  Father --|> FatherPrototype: prototype
  Father <|-- FatherPrototype: constructor
  Son --|> Father: __proto__
  Son --|> SonPrototype: prototype
  Son <|-- SonPrototype: constructor
  SonPrototype --|> FatherPrototype: __proto__
  Father --> F: new Father()
  Son --> S: new Son()
  S --|> SonPrototype: __proto__
  F --|> FatherPrototype: __proto__
  class ObjectPrototype {

  }
  class Father {

  }
  class FatherPrototype {

  }
  class Son {

  }
  class SonPrototype {
  
  }
  class F {
  
  }
  class S {
  
  }
```

And actually, `extend` will destory the relationship of `Function` and `Son`, so Let's draw a most complete diagram


```mermaid
classDiagram
  FatherPrototype --|> ObjectPrototype: __proto__
  FatherPrototype --|> Father: constructor
  Father --|> FatherPrototype: prototype
  Father --> F: new Father()
  Father --|> FunctionPrototype: __proto__
  Function --|> FunctionPrototype: __proto__
  Function --> Father: new Function()
  Function --> Son: new Function()
  FunctionPrototype --|> Function: constructor
  FunctionPrototype --|> ObjectPrototype: __proto__
  Son --|> Father: __proto__
  Son --|> SonPrototype: prototype
  SonPrototype --|> Son: constructor
  SonPrototype --|> FatherPrototype: __proto__
  Son --> S: new Son()
  S --|> SonPrototype: __proto__
  F --|> FatherPrototype: __proto__
  class ObjectPrototype {

  }
  class Function {
  
  }
  class FunctionPrototype {
    
  }
  class Father {

  }
  class FatherPrototype {

  }
  class Son {

  }
  class SonPrototype {
  
  }
  class F {
  
  }
  class S {
  
  }
```
