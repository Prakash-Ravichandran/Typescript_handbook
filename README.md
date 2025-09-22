## Basics


#### 8. Structural typing

Duck Typing:
```tsx
let point2D: Point2D = {x:0, y:10};
let point3D: Point3D = {x:0, y:10, z: 20};

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

2. The other way is lexically scoped. other than arrow function and bind functions,  this -> always points to the calling/invoking object. in case of first class functions undefined will be thrown when we assign a function to a variable.Now, in this case, since the method is not being invoked on any object, the calling context and therefore the this keyword is undefined.


When growOld is defined as an arrow function, it captures the `this` from the `Person` class `constructor's` scope. This captured `this` permanently refers to the `instance of the Person object`.  Therefore, no matter how or where `growOld` is called—whether directly on the object `(person.growOld())` or indirectly via a variable `(firstClassFuncVariable())`—`this` will always correctly point to the person instance, allowing it to increment the correct _age property.



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
function greet(welcome: string | string []){
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
