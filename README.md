## Basics


#### 8. Structural typing

Duck Typing:
```
let point2D: Point2D = {x:0, y:10};
let point3D: Point3D = {x:0, y:10, z: 20};

point2D = point3D;
```

If it walks like a duck, speaks like duck - then it must be a duck.



## Section2 - intermediate

### 22. Lexical this:
```
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