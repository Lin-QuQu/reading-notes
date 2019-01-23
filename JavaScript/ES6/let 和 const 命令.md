## 变量的解构赋值
#### 1.数组的解构赋值
  
  ```js
  let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [foo] = []; // undefined
let [bar, foo] = [1]; // 1 undefined


let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
  ```
  
  如果等号的右边不是数组（或者严格地说，不是可遍历的结构）那么将会报错。
  ```js
  // 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
  ```
  解构赋值允许指定默认值。
  ```js
  let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b' 
 
 // 只有强等于undefined的时候才会使用默认值
 
 let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
  ```
  
 #### 1.对象的解构赋值
 对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。
```js
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```

如果变量名与属性名不一致，必须写成下面这样。
```js
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```
这实际上说明，对象的解构赋值是下面形式的简写
```js
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```
**也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。**

#### 3.字符串的解析赋值
字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。
```js
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"

let {length : len} = 'hello';
len // 5
```

#### 4.数值和布尔值的解析赋值

解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。

解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。
```js
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```

#### 5.函数参数的解析赋值
```js
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3

[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```
> `TIPS：Array.prototype.map()`<br>
map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。  

#### 6.解构赋值的用途
##### 6.1 变量的交换
```js
let x = 1;
let y = 2;

[x, y] = [y, x];
```
##### 6.2 从函数返回多个值
```js
// 返回一个数组

function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```

##### 6.3函数参数的定义
这样做的意义在于在函数调用的时候，更好的语义化参数，不会显得没头没脑。
```js
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```
##### 6.4提取 JSON 数据
```js
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
##### 6.5函数参数的默认值
指定参数的默认值，就避免了在函数体内部再写var foo = config.foo || 'default foo';这样的语句。
```js
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```
##### 6.6遍历 Map 结构
```js
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world

// 或者只想获取键名
for (let [key] of map) {
  // ...
}

// 或者只想获取键值
for (let [,value] of map) {
  // ...
}
```

##### 6.7输入模块的指定方法
加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。
```js
const { SourceMapConsumer, SourceNode } = require("source-map");
```

