## Basics

#### 8. Structural typing

Duck Typing:

```tsx
let point2D: Point2D = { x: 0, y: 10 };
let point3D: Point3D = { x: 0, y: 10, z: 20 };

point2D = point3D;
```

If it walks like a duck, speaks like duck - then it must be a duck.

## Section2 - intermediate

### 22. Lexical this:

```tsx
class Person {
    private _age:number;
    constructor (age:number){
        this._age = age;
    }

    growOld(){
        this._age++;
    }

    getAge(){
       return this._age;
  }
}

const person = new Person(19);
person.growOld();
console.log(person.getAge());// 20

const firstClassFuncVariable = person.growOld();
firstClassFuncVariable(); // cannot read propery undefined of age. because for now the invoking is not an object/ its a first Class Func Variable, hence error.

Arrow functions in JavaScript capture this from the surrounding context.

Since all property initializers execute at the end of the constructor, this will be bound to whatever

instance is present within the constructor.

And now, since it is no longer driven by the calling context, we don't need to worry about it being

invoked incorrectly.

```

There are two ways to think about this keyword in js:

1. One way is the calling context - for normal functions mostly.

2. The other way is lexically scoped. other than arrow function and bind functions, this -> always points to the calling/invoking object. in case of first class functions undefined will be thrown when we assign a function to a variable.Now, in this case, since the method is not being invoked on any object, the calling context and therefore the this keyword is undefined.

When growOld is defined as an arrow function, it captures the `this` from the `Person` class `constructor's` scope. This captured `this` permanently refers to the `instance of the Person object`. Therefore, no matter how or where `growOld` is called—whether directly on the object `(person.growOld())` or indirectly via a variable `(firstClassFuncVariable())`—`this` will always correctly point to the person instance, allowing it to increment the correct \_age property.

### 23. readOnly modifier

```tsx
type Point = {
    x: number
    y: number
}

const point: Point = {
    x:4,
    y: 6
}

point.x = 5 // is allowed because we can access the members of a const point object.
To prevent this we need can add a readOnly modifier to this.

Note: readOnly modifier is only for compile time checking, not for runtime js.

same for we can add readOnly modifier for a class member variable.
```

### 24. union types

[Typescript union doc](https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html#discriminating-unions)

Def: A union type describes a value that can be one of several types. We use the vertical bar (|) to separate each type, so number | string | boolean is the type of a value that can be a number, a string, or a boolean.

```tsx
type Result = string | number | boolean;
let outcome: Result = "Success"; // Valid
outcome = 100; // Valid
outcome = true; // Valid
// outcome = {}; // Error: Type '{}' is not assignable to type 'Result'
```

```tsx

type Greet = string | string[];

function greet(welcome: Greet){
    let line = '';

    if(typeOf welcome === string ) {
        line = welcome.trim();
    }
    else {
        line = welcome.map(str => str.trim()).join(' ');
    }
    return line;
}

greet("hello");
greet(["hello world"]);
greet(55555); // if no union then this will be an error.
```

### 25. Literal types

Literal types allow you to specify the exact value a variable or property can hold, rather than a broader type like string or number. They are essentially "single-value" types.

```tsx
type Direction = "north" | "south" | "east" | "west"; // This is a union of string literals
let myDirection: Direction = "north"; // Valid
// let anotherDirection: Direction = "up"; // Error: Type '"up"' is not assignable to type 'Direction'.
```

### 26. Type Narraowing

[Typescript doc](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates)

1. `typeOf` Operator narrowing for all primitve types of javascript.

```tsx
function handlePrimitive(
  value: string | number | boolean | bigint | symbol | undefined
) {
  if (typeof value === "string") {
    // value is string
    return value.toUpperCase();
  } else if (typeof value === "number") {
    // value is number
    return value.toFixed(2);
  } else if (typeof value === "boolean") {
    // value is boolean
    return value ? "Yes" : "No";
  } else if (typeof value === "bigint") {
    // value is bigint
    return value.toString() + "n";
  } else if (typeof value === "symbol") {
    // value is symbol
    return value.toString();
  } else if (typeof value === "undefined") {
    // value is undefined
    return "No value";
  }
}
```

2. `instanceOf` operator narrowing for objects of a class.

```tsx
class Dog {
  bark() {
    return "Woof!";
  }
}

class Cat {
  meow() {
    return "Meow!";
  }
}
```

```tsx
function speak(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    // animal is narrowed to Dog
    animal.bark();
  } else if (animal instanceof Cat) {
    // animal is narrowed to Cat
    animal.meow();
  }
}
```

3. `in` operator narrowing

For the type Square, type Rectangle - we cannot use the typeOf operator, or the instanceOf operator because they are not created using classes. hence use `in` operator for narrowing their shape.

```tsx
type Square = { size: number };
type Rectangle = { width: number; height: number };

function area(shape: Square | Rectangle) {
  if ("size" in shape) {
    // shape is Square
    return shape.size * shape.size;
  }
  if ("width" in shape) {
    // shape is Rectangle
    return shape.width * shape.height;
  }
}
```

### 27. # Discriminating Unions

`kind`

- In the above example we are narrowing down using the one of the properties of shape, if we are adding more example like below `Circle` type has no width propery. We can narrowdown using a discriminating union `kind` in every property.

```tsx
type Circle = { kind: "circle"; radius: number };
```

```tsx
type Square = { kind: "square"; size: number };
type Rectangle = { kind: "rectangle"; width: number; height: number };

function area(shape: Square | Rectangle) {
  if (shape.kind == "circle") {
    return Math.PI * shape.radius ** 2;
  }

  if (shape.kind == "square") {
    // shape is Square
    return shape.size * shape.size;
  }
  if (shape.kind == "rectangle") {
    // shape is Rectangle
    return shape.width * shape.height;
  }
}
```

* `kind` could also replaced by a common propery like `isValid` in all union types.

```tsx
type ValidationSuccess = {
  isValid: true;
  validatedValue: string;
};

type ValidationFailure = {
  isValid: false;
  errorReason: string;
};

type ValidationResult = ValidationSuccess | ValidationFailure;

function logResult(result: ValidationResult) {
  if (result.isValid) {
    console.log("success validated value;", result.validatedValue);
  }
  if (result.isValid === false) {
    console.log("success validated value;", result.errorReason);
  }
}
```

### 28. Class Parameter properties

* typescript allows us to add public to the member variables of a class to remove duplication of member variables.


```tsx
class Person {
    public name: string;
    public age: number;

    constructor(name : string, age: number) {
        this.name = name;
        this.age = age;
    }
}
```


```tsx
class Person {
  constructor(public name: string, public age: number) {
      this.name = name;
      this.age = age;
  }
}

const Adam = new Person('adam', 2000);
console.log(Adam.name);
```

### 29. Strict Compiler Option

On using `npx tsx --init` the default of strict will be `strict: true`. 
Setting `strict: true` all the strict family properties are set to true. We still can enable true/false for individual properties.

```tsx
strict: Enables all strict type-checking options.
strictNullChecks: Ensures values cannot be null or undefined unless explicitly allowed.
strictBindCallApply: Checks arguments for bind, call, and apply methods.
strictFunctionTypes: Checks function type compatibility more strictly.
strictPropertyInitialization: Ensures class properties are initialized in the constructor.
noImplicitAny: Disallows variables and parameters with an implicit any type.
noImplicitThis: Ensures this expressions have a type.
alwaysStrict: Parses files in strict mode and emits "use strict" for each file.
strictBuiltinIteratorReturn: It is a TypeScript compiler option introduced to improve type safety for built-in iterator-returning methods (like Array.prototype.keys, values, entries, etc.).When enabled, it ensures that the return type of these built-in iterator methods is strictly typed, preventing accidental misuse and improving type inference in code that uses iterators.
```

Examples 1:

* `strict`: false

```tsx
function add(first, second) { // typescript infers arguments as any
  return second + first;
}

add(1, 2); // 3
add("Hello", "World"); // World Hello
```

* `strict`: true

```tsx
function add(first, second) { // compile time error is shown for this.
  return second + first;
}

function add(first:number , second: number) { // compile time error is shown for this.
  return second + first;
}

add(1, 2); // 3
```

Examples 2:

* `strict`: false


```tsx
class Point {
  x: number;
  y: number;
  
  move(x:number, y: number){
    this.x += x;
    this.y +=y;
  }
}

const point = new Point(); // ts no allows a point with having x as 'NAN', y as 'NAN'
point.move(1,3);
console.log(point.x, point.y); // 'NAN' 'NAN'
```

* `strict`: true

```tsx
class Point {
  x: number;
  y: number;

  constructor(x:number, y:number){
    this.x = x;
    this.y = y;
  }
  
  move(x:number, y: number){
    this.x += x;
    this.y +=y;
  }
}

const point = new Point(1, 3);  // ts here enforces us to have a initial point
point.move(2,5);
console.log(point.x, point.y); // 3, 8
```

### 30. Null vs undefined

- Method throws `null` for a non-matching string.

```tsx

function logVowels(value: string){
    return value.match(/[aeiou]/gi);
}


console.log(logVowels('helllo')); 
console.log(logVowels('sky')); 
```


- Example 2: null vs undefined
```tsx
console.log(null == null); // true
console.log(undefined == null); // true
console.log(undefined == undefined); // true

// falsly values are not null
console.log(false == null); // false
console.log('' == null); // false
console.log(0 == null); // false
 

// we can group the null & undefined in a same condition using == operator.
const result = someBooleanOrNullOrUndefined;

if(result != null) {
    const boolResult = result; // true | false
}

if (result == null){
    const nullOrUndefined = result; // null | undefined
}

```
- Example 3: null vs undefined.

```tsx
function decorateString(text: string | null | undefined){
    if(text == null){
        return text
    }
    
    return `--${text.trim()}--`;
}

console.log(decorateString("Hello Wolrd"));
console.log(decorateString(null));
console.log(decorateString(undefined));
```

### 31. Intersection types

[Intersection-types](https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html#intersection-types)

* Intersection types are closely related to `union` types, but they are used very differently. **An intersection type combines multiple types into one**. This allows you to add together existing types to get a single type that has all the features you need. For example, `Person` & `Serializable` & `Loggable` is a type which is all of `Person` and `Serializable` and `Loggable`.


```tsx

interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtworksData {
  artworks: { title: string }[];
}

interface ArtistsData {
  artists: { name: string }[];
}

// These interfaces are composed to have
// consistent error handling, and their own data.

type ArtworksResponse = ArtworksData & ErrorHandling;
type ArtistsResponse = ArtistsData & ErrorHandling;

const handleArtistsResponse = (response: ArtistsResponse) => {
  if (response.error) {
    console.error(response.error.message);
    return;
  }

  console.log(response.artists);
};
```

### 32. Optional Modifier

* Optional modifer has type as `givenType | undefined` by default. 
Example: `phone?: number;` -> `number | undefined` by default, if we want to add `null` then  

```tsx
type ContactDetails = {
  name: string;
  age: number;
  phone?: number; // optional modifier
}

type ContactDetailsWithnull = {
  name: string;
  age: number;
  phone?: number | null; // optional modifier // supports number | undefined | null
}


const bruce: ContactDetails = {
  name: 'bruce',
  age: 32,
  phone: 89765,
}

console.log('bruce phone number', bruce.phone); // 89765




const alfred: ContactDetails = {
  name: 'bruce',
  age: 32,
}

console.log('alfred phone number', alfred.phone); // undefined
```

### 33. Non-null Assertion operator

- It is entirely possible to end up in a situation where Typescript's code flow analysis cannot be sure that a value is null or undefined.

- When typescript compilation indicates a compile time error that a value is null or undefined, we can use `!` - non-null assertion operator.

**So its generally better to re-write the code without using non-null assertion operator.**

 **non-null assertion operator example:**

<img width="796" height="306" alt="Image" src="https://github.com/user-attachments/assets/4619c7f1-ea24-4a1b-b2eb-d973a1a79c3d" />

<img width="812" height="353" alt="Image" src="https://github.com/user-attachments/assets/d9a0bc8a-4bc2-47dc-a294-229454629de2" />



```tsx
type Point = {
  x: number;
  y: number;
}

let point: Point;
function initializePoint(){
  point = { x : 0, y : 0}
}

initializePoint();

console.log('x=', point!.x, "y=", point!.y); // added non-null assertion operator
```


```tsx
type Point = {
  x: number;
  y: number;
}

/**
 * // rewritten the function to remove the dangling variable declaration & 
 * return the point & use after initialized.
 */
function initializePoint(): Point {
  return { x : 0, y : 0}
}

const point =  initializePoint();

console.log('x=', point.x, "y=", point.y); 
```

* Example 2: Ts code flow shows that person.email can be null - place to use non-null assertion operator/rewrite the code.


```tsx
type Person = {
  name: string;
  email?: string | null | undefined;
}


function SendEmail(email: string){
  console.log('send email:', email);
}


function ensurePersonContactable(person: Person) {
   if(person.email == null) throw new Error(`Person doesn't have any email to contact`);
}


function Contact(person: Person){
  ensurePersonContactable(person);
  /**
   * // here typescript code flow analysis make it person.email can still be null,
   * but we already ensured it to throw error if its null.
   */
  SendEmail(person.email); // place to add non-null assertion or rewrite the code
}
```

* Example: refactored code without non-null assertion operator

```tsx
type Person = {
  name: string;
  email?: string | null | undefined;
}


function SendEmail(email: string){
  console.log('send email:', email);
}

function Contact(person: Person){
  /**
   * Here ts codeflow analysis knows that person.email cannot be null.
   */
 if(person.email == null) throw new Error(`Person doesn't have any email to contact`);
  SendEmail(person.email); // place to add non-null assertion or rewrite the code
}
```





### 34. types vs interfaces usages:

* `type`: type aliase in ts in particularly designed for using:
   - the types similar to js object declaration & variables with an equalto operator in the syntax.

* `interface`: they are similar to how we declare classes in javascript, they are useful for other programming languages engineers coming to ts.

1. Merging interfaces
2. familiarity with `extends` keyword usages.

```tsx
type Point2D = {
   x: number;
   y: number;
}

type Point3D = Point2D & {
  z: number;
}

export const point: Point3D = {
  x: 0,
  y: 9,
  z: 0
}


interface Point2d {
   x: number;
   y: number;
}

interface Point3d extends Point2d {
  z: number;
}

export const pointWith3D: Point3d = {
  x: 0,
  y: 0,
  z: 0
}
```



### 35. Interface declaration merging:


```tsx

interface Request {
  body: any;
}


interface Request {
  json: any;
}


export function handleRequest(req: Request){
  req.body;
  req.json;
}
```

### 36: Types vs interface

 * The advantage of type alias: Can separate each property of type to an individual type.
 * The interface syntax: should defenitely have a body '{}'


```tsx
type InputProps = {
  input: 'email' | 'text';
  value: string;
  onInputChange: (value: string) => void;
}


type Input = 'email'| 'text';
type Value = string;
type onInputChange = (value: string) => void;

type InputProp = {
  input: 'email' | 'text';
  value: string;
  onInputChange: (value: string) => void;
}
```

<img width="1600" height="900" alt="Image" src="https://github.com/user-attachments/assets/05fd24b9-0321-46bd-adc1-5296b34681ad" />

### 37. Never type

* The never type is the inferred return type for functions that always throw an error or contain an infinite loop, ensuring that the function will never successfully complete execution and return a value.

```tsx
    function throwError(message: string): never {
        throw new Error(message);
    }

    function infiniteLoop(): never {
        while (true) {
            // This loop never exits
        }
    }
```


```tsx
/**
 * The type never can be used for checking whether all unions of a type alias is handled.
 */


type Square = {
  kind: 'square'
  size: number
}

type Rectangle = {
  kind: 'rectangle'
  width: number;
  height: number;
}

type Shape = Square | Rectangle;

function calculateArea(s: Shape){
  if(s.kind == 'square'){
     return s.size * s.size;
  } else if(s.kind == 'rectangle'){
    return s.width * s.height;
  }else {
  const _ensureAllCasesHandled: never = s;
  throw new Error('unhandled shape');
  }

}
```

## Advanced

### 38. implements keyword

[difference-implements-and-extends](https://stackoverflow.com/questions/38834625/whats-the-difference-between-extends-and-implements-in-typescript)


`implements` keyword can be used for treating/restricting the class/object to have the same shape.


```tsx

type Animal = {
  name: string;
  voice(): string;
}

function log(animal: Animal){
  console.log(`Animal ${animal.name}: ${animal.voice()}`);
}

class Cat implements Animal {
  constructor(public name: string){}
  voice(){ return 'meow';}
}

class Dog implements Animal {
  constructor(public name: string){}
  voice() { return 'woof';}
}

log(new Cat('Salem'));
log(new Dog('Lassie'));

```

### 39. Definite Assignment Assertion


Definite Assignment Assertion `let dice!: number` can be used when typescript still says  `Variable x is used before being assigned` at that time we can use this.

Typescript shows error `Variable 'dice' is used before being assigned`For this we can use definite Assignment operator. 

Example 1:

```tsx
let dice!: number;   
function rollDice(){   
  dice = Math.floor((Math.random() * 6) + 1); 
}  
rollDice(); 
console.log('dice', dice);
```

Example 2: without `Definite Assignment Assertion`

```tsx
class Point {
  x: number;
  y: number;

  constructor(x: number , y: number){
    this.x = x;
    this.y = y;
  }

  moveRandom() {
    this.x = Math.random();
    this.y = Math.random();
  }
}
```

- If we remove the initializer of this.x = x; the scenario if same as above, then fix using Definie Assignment Assertion.

```tsx
class Point {
  x!: number; // included Definite Assignment Assertion
  y!: number; // included Definite Assignment Assertion

  constructor(){
    this.moveRandom();
  }

  moveRandom() {
    this.x = Math.random();
    this.y = Math.random();
  }
}
```

### 40. User Defined Type Guards

[typescript-doc-user-defined-type-guards](https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards)

- To alter shape of object - we narrow the shape using discriminating unions like `kind`

- To not alter the shape - we can use User Defined Type Guards

```tsx

type Circle = {
   radius: number;
}


type Rectangle = {
   length: number;
   breadth: number;
}

type Square = {
   size: number;
}

type Shape = Circle | Rectangle | Square;

// we are telling typescript that if isSquare() returns true then shape is of type Square.

function isCircle(shape: Shape): shape is Circle  { // user defined type guards
    return 'radius' in shape;
}

function isRectangle(shape: Shape): shape is Rectangle { // user defined type guards
  return 'length' in shape; 
}

function isSquare(shape: Shape): shape is Square { // user defined type guards
  return 'size' in shape;
}

// using user defined type guards
function calculateArea(shape: Shape ){
   if(isCircle(shape)){
      return Math.PI * shape.radius * shape.radius;
   }

   if(isRectangle(shape)){
      return shape.length * shape.breadth;
   }

    if(isSquare(shape)){
      return shape.size * shape.size;
   }
   
  const _ensureAllDeclared: never = shape;

  return _ensureAllDeclared;
}

// discriminating unions
function calculateArea(shape: Shape ){
   if( 'radius' in shape){
      return Math.PI * shape.radius * shape.radius;
   }

   if('length' in shape){
       return shape.length * shape.breadth;
   }

     if('size' in shape){
       return shape.size * shape.size;
   }
   
  const _ensureAllDeclared: never = shape;

  return _ensureAllDeclared;
}
```

### 41. Assertion Functions


- typescript here is not doing any implicit checking here, it does support explicit checking in the form of assertion checking.

- use `asserts functions` for application tests 
- use `User Defined Type Guards` for developmenet works.


```tsx

type Person = {
  name: string;
  dateOfBirth: Date;
}

function assert(condition: unknown, message: string): asserts condition {
  if(!condition) throw new Error(message); 
}

function asserDate(value: unknown): asserts value is Date {
   if(value instanceof Date) return;
   else throw new TypeError ('value is not Date');
}

const loadPerson = () :Person => {
  console.log('fetches person');
  return { name: 'jack', dateOfBirth: new Date()}
}

const mayBePerson = loadPerson();

assert(mayBePerson != null, 'could not load person');

console.log('name:', mayBePerson.name);
console.log('name:', mayBePerson.dateOfBirth);
```

### 42. Function Overloading


- typescript correctly infers the output1 as `function remapVowels(word: string): string (+1 overload)`
- typescript correctly infers the output2 as `function remapVowels(word: string[]): string[] (+1 overload)`
- those declaration are not part of runtime code, its compile time only.

```tsx
// function declaration for all function overloading
function remapVowels(word: string): string;
function remapVowels(word: string[]): string[];

// we need have only one body of function definition that handles all overloading
function remapVowels(word:string | string []) { 
    if(Array.isArray(word)) {
      return word.map((w) =>  w.match(/[aeiou]/gi) ?  w='-' : w=w);
    }
    if( typeof word === 'string') {
      return word.split("").map((w) =>  w.match(/[aeiou]/gi) ?  w='-' : w=w).join(' ');
    }
    throw new Error('input only string | string[]')
}

const output1 = remapVowels('australia'); // "output 1",  "- - s t r - l - -" 
const output2 = remapVowels(['a', 'u', 's', 't', 'r', 'a', 'l', 'i', 'a']); // "output 2",  ["-", "-", "s", "t", "r", "-", "l", "-", "-"]
```

### 43. Function Call Signatures

```tsx
type RemapVowelsProps = (word: string | string[]) => string | string[];

const remapVowels:RemapVowelsProps = (word:string | string [])  => {
    if(Array.isArray(word)) {
      return word.map((w) =>  w.match(/[aeiou]/gi) ?  w='-' : w=w);
    }
    if( typeof word === 'string') {
      return word.split("").map((w) =>  w.match(/[aeiou]/gi) ?  w='-' : w=w).join(' ');
    }
    throw new Error('input only string | string[]')
}
```

#### Function Call Signatures in type alias

```tsx
// function overloads in type alias
// we cannot use Add type alias to normal functions, we assign it to only arrow functions
type Add = {
  (a: number, b: number): number;
  (a: number, b: number, c: number): number;
}

const add:Add = (a:number, b:number) => {
  return a + b;
}
```

Example for creating a signature for class


```tsx
type PointCreator = new (x:number, y: number) => {x:number, y:number};

const Point:PointCreator = class {
   x:number;
   y: number;
    constructor(x:number, y: number){
      this.x = x;
      this.y = y;
    }

    move() {
      this.x = Math.floor(Math.random() + 1);
      this.y = Math.floor(Math.random() + 1);
    }
}

const point1 = new Point(3, 5);
```

### 44. Abstract classes


 * Cannot create intsance of abstract class
 * The implmentation happens in the inherited sub-class.
 * Abstract methods, which are declared without an implementation (only a signature). 
 * Subclasses must provide an implementation for all abstract methods.

```tsx
abstract class LivingCreature {
   abstract eat(noOfMeals: number):string;
   abstract sleep(): void;
   abstract work(): void

   breath(){
     return 'living creatures breath';
   }
}

class Human extends LivingCreature {
  eat(noOfMeals:number){
    return `eats ${noOfMeals} meals a day`;
  }

  sleep() {
    return `eight hours a day`;
  }

  work(){
    return `work in a day for living`
  }
}
```

### 45. Index Signature

[index-signatures](https://www.typescriptlang.org/glossary/#index-signatures)

- A type in TypeScript usually describes an exact set of fields to match on an object. An index signature is a way to define the Shape of fields which are not known ahead of time.
- The key in object can be `string/number` supported by js by default. 


```tsx
type Person = {
  name: string;
  email: string;
}


type Dictionary = {
  [key: string]: Person; // the property name is known at runtime.
}


const persons: Dictionary = {
  jane: {name: 'jane', email: 'jane@example.com'}
}

persons['mike'] = {name: 'mike', email: 'mike@example.com'};


type DictionaryWithoutIndex = {
   name: Person;
}

const personsWithoutDic: DictionaryWithoutIndex = {
   name: {name: 'mike', email: 'mike@example.com'}
}

console.log(personsWithoutDic['name']);
```

### 46. Readonly Arrays & Tuples

type Neat = readonly number[];
type Long = ReadonlyArray<number>; // readonly generic interface


```tsx

const originalArray = [89, 90, 13, 12, 66];
const newArr = originalArray.sort();

console.log('originalArray', originalArray); //  [12, 13, 66, 89, 90] 
console.log('newArr', newArr); //  [12, 13, 66, 89, 90] 


const originalArray: Neat = [89, 90, 13, 12, 66];
const newArr = originalArray.slice().sort();

console.log('originalArray', originalArray); // [89, 90, 13, 12, 66] 
console.log('newArr', newArr); //  [12, 13, 66, 89, 90] 
```

Tuples: Arrays with fixed length.

```tsx
type Point = readonly [number, number];

function move(point:Point, x:number, y: number) {
  return [point[0]+ x, point[1] + y];
}


const point:Point = [1,2];
const moved = move(point, 5, 5);

console.log(point);
console.log(moved);
```

### 47. Double Assertion


note about `any` & `unknown`


```tsx
any, unknown are universal superset types.

- Similarities: any, unknown can accept any values/types.
- Difference: any - can do anything, unknown - a type safe version of any.



let exampleAny: any;
let exampleUnknown: unknown;
 
exampleAny = 123;
exampleAny = 'Hello World';
exampleUnknown = 123;
exampleUnknown = 'Hello Wolrd';
 
 
exampleAny.doAny.allow.anything.do();
 
if (typeof exampleUnknown == 'string') {
   exampleUnknown.trim();
}
if (typeof exampleUnknown == 'boolean') {
   let isLoading:boolean = exampleUnknown;
}
```

Double assertions:

 * unknown can be used for double assertion telling ts that the type is going to unknown & then its second asserted type.

```tsx
let employeeIDs: string[] = ['45', '75', '90'];

let emplyeeIDAsNumber: Array<number> = employeeIDs as unknown as number[];
```

### 48. as const Assertion

Does three things

`as const` assertion provides three things:

1. Any primitives to literal types (can contain only the specified value of the object).

2. object properties are also readonly 

3. object property contains any arrays to read only tuples.

```tsx
const DaveAddress = {
  name: 'dave',
  address: '22, New Street',
  ph: ['989894567', '7654312345']
} as const;

// as per js we can't ressign DaveAddress but we can re-modify name, address, ph.
// we achieve this using as const assertion.
```

** as const assertion **:

<img width="1146" height="732" alt="Image" src="https://github.com/user-attachments/assets/c0880a8c-3079-4a67-99b2-bca3db04bb13" />

### 49. this paramter

- this here is person (calling context), we can restrict the object using `this` object should have `value` as the first parameter in the object.

```tsx
function incrementValue(this: {value: number }) {
    this.value = this.value * 2;
}


const person = {
  value: 10000,
  double: incrementValue
}

person.double();

const personWithImproperValue = {
    valve: 15000,
    double: incrementValue
}

personWithImproperValue.double();
```

### 50. Generic constraints with Generics Notes

```tsx
// 1. defining a function with generics
//takes a type parameter Type, and an argument arg which is an array of Types, and returns an array of Types.”
function logIdentity<T>(arg: T):T{
    return arg;
}

const argWithString = logIdentity<string>('string'); //given as string
const argWithNumber = logIdentity<number>(56); //given as string
const argWithBoolean = logIdentity<boolean>(true);
const argWithUndefined = logIdentity<undefined>(undefined);
const argWithNull = logIdentity<null>(null);

// 2. using the syntax of using generics
// type argument
let output = logIdentity<string>('chair person');
// type argument interface
let outputWithTypeArgumentInterface = logIdentity('chair person');


// 3. Generic Type Variables
function CalculateLength<T>(arg: T): T{
  if(arg.length > 0) // Property 'length' does not exist on type 'T'.
  return arg;
}

//Note: ts cannot confirm that the Type paramter can always hold a parameter that contains length in it.

const len = CalculateLength<string>('text');

// we can solve it 
function CalculateLengths<T>(arg: T[]): T[]{
  if(arg.length > 0) {
      return arg;
  } 
  return [];
}

const l = CalculateLengths<string>(['text1', 'text2', 'text3']);

// can be written in 
function CalculateLengthsWithOtherSyntax<T>(arg: Array<T>): Array<T>{
  if(arg.length > 0) {
      return arg;
  } 
  return [];
}

//4 . Generic types

function logValue<T>(arg: T): T {
  return arg;
}

const logV: <T> (arg: T) => T = logValue;

// now we can separate above inline type as interface called Generic type 
interface LogValue {
  <T> (arg:T) : T // for interface we use : after the argument() brackets.
}

interface LogValueWithTypeParameter<T> { // for using type paramter
  (arg:T) : T 
}

function logV_Interface<T>(arg: T):T { // normal function cannot annotate a generic interface
    return arg;
}

const logV_With_Interface: LogValue = logV_Interface; // assign to a variable
const logV_With_Interface_Other_Syntax: LogValueWithTypeParameter<number> = logV_Interface; // assign to a variable


// 5. Generic classes - follows same structure as interface

class DemonstrateGenericClass<NType> {
    initialParameter: NType;
    lastParameter: NType;
    add() : NType {
      return this.initialParameter as any + this.lastParameter as any;
    }

    constructor(intialParameter: NType, lastParameter: NType) {
      this.initialParameter = intialParameter;
      this.lastParameter = lastParameter;
    }
}

const Instance = new DemonstrateGenericClass<number>(5, 10);
console.log(Instance.add())


// 6 generic constraints

function loggingIdentity<Type>(arg: Type): Type {
  console.log(arg.length);
 // Property 'length' does not exist on type 'Type'.
  return arg;
}

// we need a function that works with all types as type variable, argument, return type.

// its possible through a constraints

interface SizeWise {
  size: number 
}

function loggingIdentityx<Type extends SizeWise>(arg: Type ): Type {
  console.log(arg.size);
 // Property 'size' does not exist on type 'Type'.
  return arg;
}


// 7. using type paramters in generic constraints

function getProperty<Type, KeyInput extends keyof Type>(obj: Type, key: KeyInput) {
  return obj[key];
}
 
let x = { a: 1, b: 2, c: 3, d: 4 };
 
console.log(getProperty(x, "a"));
console.log(getProperty(x, "m"));
```

### 51. Dealing with Temporal Uncertainity.

## Expert

### 52 typeof type operator

- The typeof operator can be used to derive type from existing objects
- can also be used to derive types from the json from API response

```tsx
const center = {
    x: 0,
    y:0,
    z: 0
}

type Point = typeof center;

const point: Point = {
    x: 1,
    y: 2,
    z: 3
}

const pointWithInline: typeof point = {
    x: 1,
    y: 2,
    z: 3
}
```

### 53. Lookup types 

- Lookup types, also known as indexed access types, are a powerful feature in TypeScript that allow for the extraction and reuse of specific property types from existing types. This capability addresses several needs in TypeScript development.

```tsx
// 1. Interface for a single User object
interface User {
  id: number;
  username: string;
  email: string;
  is_active: boolean;
}

// 2. Interface for the entire API response structure
interface ApiResponses {
  // Response for fetching a single user by ID
  'fetchUserById': {
    status: number; // e.g., 200
    data: User;
    timestamp: string;
  };
  // Response for fetching a list of users
  'fetchAllUsers': {
    status: number; // e.g., 200
    data: User[]; // Array of User objects
    meta: {
      totalCount: number;
    };
  };
  // Response for an error (e.g., 404 Not Found)
  'notFoundError': {
    status: 404;
    error: {
      code: string;
      message: string;
    };
  };
}

type fetchUserById = ApiResponses['fetchUserById']; // access 1st level types
type fetchUserByStatus = ApiResponses['fetchUserById']['status']; // access 2nd level types
```

### 54. keyof type operator

- The keyof operator takes type as the input and returns unions of keys from the taken input.

```tsx
type User = {
    name: string;
    email: string;
    address: string;
}

type Userkeys = keyof User;

const userDetail:Userkeys = 'name';
```

### 55. Conditional types

```tsx
// ts way of finding the given input is number or not
type IsNumber<T> = T extends number ? 'number' : 'other';

type typeWithNumber = IsNumber<number>;
type typeWithOther = IsNumber<string>;


// Javscript way of finding the number
const IsNumber = (arg: unknown) => {
    if(typeof arg === 'number') {
        return 'number'
    }
    return 'other';
}

const WithNumber = IsNumber(123);
const WithOther = IsNumber('other');

console.log(WithNumber);// number
console.log(WithOther); // other
```

### 56. Conditional types with Unions and never

[stackoverflow-answer](https://stackoverflow.com/questions/42291811/use-of-never-keyword-in-typescript)

1. We cannot assign to a never typed variable.
2. We can assign a never returned variable to other types because never is something that not going to be assigned to it.

```tsx
function logError(message:string):never {
    throw new Error(message);
}


const notAllowed:never = 'some string'; //Type '"some string"' is not assignableto type 'never'.

const allowed:string = logError('error'); // can assign never to anything

type Verbose = string | never;

type Concise = string;
```


Conditional types in never

Exclucde null and undefined from T

```tsx
export type NoEmpty<T> = T extends null | undefined ? never : T;

apply union to T, is equivalent to type ExpandedExample.
type Example = NoEmpty<string | null>;
type ExpandedExample = NoEmpty<string> | NoEmpty<null>;

inline expansion

type ExpandedInline = (string extends null | undefined ? never: string) |
 (null extends null | undefined ? never : string);

 after expression how ts sees

 type ConcludeExpanded = string | never;
 type Result = string;
 ```

### 57 infer keyword and ReturnType<T>

- In TypeScript, the `infer` keyword is used within conditional types to capture and extract a type from another type. It allows for dynamic type extraction and manipulation, making it a powerful tool for advanced type transformations.

```tsx
type IsArray<T> = T extends Array<any> ? 'array': 'other';


type typeWithArray = IsArray<string []>;
type WithoutArray = IsArray<string>;

//using infer keyword
type UnboxArray<T> = T extends Array<infer Member> ? Member[] : T;
type _typeWithArray = UnboxArray<string[]>;
type _WithoutArray = UnboxArray<string>;
```

- ReturnType

```tsx
function CreatePerson(firstName: string, lastName: string){
    return {
        firstName: firstName,
        lastName: lastName,
        fullName: `${firstName} ${lastName}`
    }
}

// internal ReturnType looks like below
// type ReturnType<T> = T extends (...args: any) => infer R ? R : never;

-T extends (...args: any[]) => infer R checks if T is a function type.
-If it is, infer R captures the return type of that function into a new type -variable R.
-The conditional type then returns R (the inferred return type); otherwise, it returns any.

function AddPerson(person: ReturnType<typeof CreatePerson>){
    const list = [];
    list.push(person);
}

const person = CreatePerson('mike', 'jane');
AddPerson(person);
```

### 58. Mapped Types

- The great thing about map types is that you can add modifiers that will apply to all the loop items.

- So if you apply the modifier, read only all of these members become read only, which is the type that we want to generate.


```tsx
type Point = {
 x: number;
 y: number;
 z: number;
}

type ReadOnlyPoint = {
 readonly [Item in keyof Point]: number; // is equivalent to adding readonly to all properties
}


const center:  ReadOnlyPoint = {
   x: 0,
   y: 0,
   z:0
}

center.x = 5; // we cannot modifiy this
```








