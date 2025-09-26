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