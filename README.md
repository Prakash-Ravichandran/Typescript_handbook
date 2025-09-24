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

In the above example we are narrowing down using the one of the properties of shape, if we are adding more example like below `Circle` type has no width propery. We can narrowdown using a discriminating union `kind` in every property.

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

`kind` could also replaced by a common propery like `isValid` in all union types.

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
    if(result.isValid) {
        console.log('success validated value;', result.validatedValue);
    }
    if(result.isValid === false) {
        console.log('success validated value;', result.errorReason);
    }

}
```
