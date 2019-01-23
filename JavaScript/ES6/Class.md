## Class
#### 1.基本用法
先来一个用class写的对对象的例子,其实这些都可以通过ES5实现。
```js
//定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

var b = new Point();
b.toString()
```
上面代码定义了一个“类”，可以看到里面有一个constructor方法，这就是构造方法，而this关键字则代表实例对象。**也就是说，ES5 的构造函数Point，对应 ES6 的Point类的构造方法。**  

注意，定义“类”的方法的时候，前面不需要加上function这个关键字，直接把函数定义放进去了就可以了。**另外，方法之间不需要逗号分隔，加了会报错。**

**事实上，类的所有方法都定义在类的`prototype`属性上面。在类的实例上面调用方法，其实就是调用原型上的方法。**
```js
b.constructor === B.prototype.constructor // true
```
#### 2.严格模式
类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。

考虑到未来所有的代码，其实都是运行在模块之中，所以 ES6 实际上把整个语言升级到了严格模式。

#### 3.constructor
constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。
```js
class Point {
}

// 等同于
class Point {
  constructor() {}
}
```
constructor方法默认返回实例对象（即this），当然也可以指定返回另外一个对象。

类必须使用new调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用new也可以执行。
#### 4.Class的静态方法
**类相当于实例的原型，所有在类中定义的方法，都会被实例继承**。如果在一个方法前，加上static关键字，**就表示该方法不会被实例继承**，而是直接通过类来调用，这就称为“静态方法”。
```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```
**注意，如果静态方法包含this关键字，这个this指的是类，而不是实例。**

父类的静态方法，可以被子类继承。

```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```
静态方法也是可以从super对象上调用的。
```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}

Bar.classMethod() // "hello, too"
```
#### 5.Class 的静态属性
静态属性指的是 Class 本身的属性，即Class.propName，而不是定义在实例对象（this）上的属性
```js
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```
目前，只有这种写法可行，因为 ES6 明确规定，Class 内部只有静态方法，没有静态属性。

#### 5.类的继承
Class 可以通过extends关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。
```js
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```
上面代码中，constructor方法和toString方法之中，都出现了super关键字，它在这里表示父类的构造函数，用来新建父类的this对象。

子类必须在constructor方法中调用super方法，否则新建实例时会报错 **。这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。**



    继承的实质 
     
    ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面（Parent.apply(this)）。
    ES6的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this。

另一个需要注意的地方是， **在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。** 这是因为子类实例的构建，基于父类实例，只有super方法才能调用父类实例。

#### 6.Object.getPrototypeOf()

Object.getPrototypeOf方法可以用来从子类上获取父类,因此，可以使用这个方法判断，一个类是否继承了另一个类。
```js
Object.getPrototypeOf(ColorPoint) === Point
// true
```

#### 7.super
super作为函数调用时，只能出现在构造函数中，super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B，因此super()在这里相当于A.prototype.constructor.call(this)。
```js
class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B
```

第二种情况，super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。
```js
class A {
  p() {
    return 2;
  }
}

class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}

let b = new B();
```
上面代码中，子类B当中的super.p()，就是将super当作一个对象使用。这时，super在普通方法之中，指向A.prototype，所以super.p()就相当于A.prototype.p()。

这里需要注意，由于super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。

```js
class A {
  constructor() {
    this.p = 2;
  }
}

class B extends A {
  get m() {
    return super.p;
  }
}

let b = new B();
b.m // undefined
```

#### 8.Mixin模式的实现
Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口。它的最简单实现如下,将多个类的接口“混入”（mix in）另一个类。
```js
const a = {
  a: 'a'
};
const b = {
  b: 'b'
};
const c = {...a, ...b}; // {a: 'a', b: 'b'}
```