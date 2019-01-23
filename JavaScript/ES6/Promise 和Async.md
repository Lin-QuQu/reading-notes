## Promise

#### 1.promise.all()
```js
const p = Promise.all([p1, p2, p3]);
```

p的状态由p1、p2、p3决定，分成两种情况。  

（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

```js
Promise.all(promises).then(function (posts) {
  // ...
}).catch(function(reason){
  // ...
});
```
只有数组实例的状态都变成fulfilled，或者其中有一个变为rejected，才会调用Promise.all方法后面的回调函数。

注意，如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法。
```js
const p1 = new Promise((resolve, reject) => {
  resolve('hello');
})
.then(result => result)
.catch(e => e);

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了');
})
.then(result => result)
.catch(e => e);

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e));
// ["hello", Error: 报错了]
```
上面代码中，p1会resolved，p2首先会rejected，但是p2有自己的catch方法，该方法返回的是一个新的 Promise 实例，p2指向的实际上是这个实例。该实例执行完catch方法后，也会变成resolved，导致Promise.all()方法参数里面的两个实例都会resolved，因此会调用then方法指定的回调函数，而不会调用catch方法指定的回调函数。

如果p2没有自己的catch方法，就会调用Promise.all()的catch方法。

#### 2.Promise.race()
```js
const p = Promise.race([p1, p2, p3]);
```
上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

Promise.race方法的参数与Promise.all方法一样，如果不是 Promise 实例，就会先调用下面讲到的Promise.resolve方法，将参数转为 Promise 实例，再进一步处理。    
```js
const p = Promise.race([
  fetch('/resource-that-may-take-a-while'),
  new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('request timeout')), 5000)
  })
]);

p
.then(console.log)
.catch(console.error);
```
上面代码中，如果 5 秒之内fetch方法无法返回结果，变量p的状态就会变为rejected，从而触发catch方法指定的回调函数。
### 3.Promise.resolve()
有时需要将现有对象转为 Promise 对象，Promise.resolve方法就起到这个作用。
```js
const jsPromise = Promise.resolve($.ajax('/whatever.json'));


Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```  

+ 参数是一个Promise对象：`Promise.resolve`将不做任何处理，原封不动的返回这个对象
+ 参数是一个`thenable`的对象，将会被转换为一个Promise对象，然后立即执行`then()`
+ 参数不是具有then方法的对象，或根本就不是对象:生成一个Promise对象，状态为resolve
```js
const p = Promise.resolve('Hello');

p.then(function (s){
  console.log(s)
});
// Hello
```
上面代码生成一个新的 Promise 对象的实例p。由于字符串Hello不属于异步操作（判断方法是字符串对象不具有 then 方法），返回 Promise 实例的状态从一生成就是resolved，所以回调函数会立即执行。Promise.resolve方法的参数，会同时传给回调函数。

+ 不带有任何参数:Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve方法。
```js
const p = Promise.resolve();

p.then(function () {
  // ...
});
```
**TIPS:需要注意的是，立即resolve的 Promise 对象，是在本轮“事件循环”（event loop）的结束时，而不是在下一轮“事件循环”的开始时。**

```js
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```
上面代码中，setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.resolve()在本轮“事件循环”结束时执行，console.log('one')则是立即执行，因此最先输出。

#### 4.Promise.reject()

Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为rejected。
```js
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});
// 出错了
```
注意，Promise.reject()方法的参数，会原封不动地作为reject的理由，变成后续方法的参数。这一点与Promise.resolve方法不一致。

#### 5.应用：图片的加载
当图片加载完成的时候出发onload事件的时候就调用resolve，当图片加载失败的时候就出发onerror事件的时候就触发reject状态改变
```js
const preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    const image = new Image();
    image.onload  = resolve; //onload事件
    image.onerror = reject;  //onerror事件
    image.src = path;
  });
};

preloadImage('https://xxx.xxx.xxx')
    .then(this.images[inedx])
    .catch(this.images[index])
```

#### 6.Promise.try()

实际开发中，经常遇到一种情况：不知道或者不想区分，函数f是同步函数还是异步操作，但是想用 Promise 来处理它。因为这样就可以不管f是否包含异步操作，都用then方法指定下一步流程，用catch方法处理f抛出的错误。一般就会采用下面的写法。
```js
const f = () => console.log('now');
Promise.try(f);
console.log('next');
// now
// next
```
#### 7.Promise.finally()
finally() 方法返回一个Promise，在执行then()和catch()后，都会执行finally指定的回调函数。避免同样的语句需要在then()和catch()中各写一次的情况。
```js
p.finally(onFinally);

p.finally(function() {
   // 返回状态为(resolved 或 rejected)
});参数
```


## Async
async 函数是什么？一句话，它就是 Generator 函数的语法糖。

但是，async函数对 Generator 函数的改进，体现在以下四点:
1. 内置执行器。Generator 函数的执行必须靠执行器，所以才有了co模块，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。
2. 更好的语义。async和await，比起星号和yield，语义更清楚了。**async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。**
3. 更广的适用性。co模块约定，yield命令后面只能是 Thunk 函数或 Promise 对象，**而async函数的await命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）**
4. 返回值是 Promise。**async函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用then方法指定下一步的操作。**
5.

#### 1.基本用法
async函数返回一个 Promise 对象，可以使用then方法添加回调函数。当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。
```js
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```

#### 2.返回一个 Promise 对象
async函数返回一个 Promise 对象。

async函数内部return语句返回的值，会成为then方法回调函数的参数。
```js
async function f() {
  return 'hello world';
}

f().then(v => console.log(v))
// "hello world"
```
async函数内部抛出错误，会导致返回的 Promise 对象变为reject状态。抛出的错误对象会被catch方法回调函数接收到。
```js
async function f() {
  throw new Error('出错了');
}

f().then(
  v => console.log(v),
  e => console.log(e)
)
// Error: 出错了
```

#### 3.await 命令
正常情况下，await命令后面是一个 Promise 对象。如果不是，就返回对应的值。
```js
async function f() {
  // 等同于
  // return 123;
  return await 123;
}

f().then(v => console.log(v))
// 123
```
只要一个await语句后面的 Promise 变为reject，那么整个async函数都会中断执行。
```js
async function f() {
  await Promise.reject('出错了');
  await Promise.resolve('hello world'); // 不会执行
}
```
有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个await放在try...catch结构里面，这样不管这个异步操作是否成功，第二个await都会执行。
```js
async function f() {
  try {
    await Promise.reject('出错了');
  } catch(e) {
  }
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// hello world
```
#### 4.错误处理
如果await后面的异步操作出错，那么等同于async函数返回的 Promise 对象被reject。防止出错的方法，也是将其放在try...catch代码块之中。如果有多个await命令，可以统一放在try...catch结构中。
```js
async function main() {
  try {
    const val1 = await firstStep();
    const val2 = await secondStep(val1);
    const val3 = await thirdStep(val1, val2);

    console.log('Final: ', val3);
  }
  catch (err) {
    console.error(err);
  }
}
```

#### 5.Async \ await 的使用注意
1. 第一点，前面已经说过，await命令后面的Promise对象，运行结果可能是rejected，所以最好把await命令放在try...catch代码块中。
2. 第二点，多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
```js
let foo = await getFoo();
let bar = await getBar();
```
getFoo和getBar是两个独立的异步操作（即互不依赖），却被写成继发关系。这样比较耗时，因为只有getFoo完成以后，才会执行getBar，完全可以让它们同时触发。
```js
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);

// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```
3. 第三点，await命令只能用在async函数之中，如果用在普通函数，就会报错

#### 6.和Promise的比较
现在要实现一个功能，经常遇到一组异步操作，需要按照顺序完成。比如，依次远程读取一组 URL，然后按照读取的顺序输出结果。

promise的写法：
```js
function logInOrder(urls) {
  // 远程读取所有URL
  const textPromises = urls.map(url => {
    return fetch(url).then(response => response.text());
  });

  // 按次序输出
  textPromises.reduce((chain, textPromise) => {
    return chain.then(() => textPromise)
      .then(text => console.log(text));
  }, Promise.resolve());
}
```

下面是 async 函数实现：
```js
async function logInOrder(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(await response.text());
  }
}
```
上面代码确实大大简化，问题是所有远程操作都是继发。只有前一个 URL 返回结果，才会去读取下一个 URL，这样做效率很差，非常浪费时间。我们需要的是并发发出远程请求。

```js
async function logInOrder(urls) {
  // 并发读取远程URL
  const textPromises = urls.map(async url => {
    const response = await fetch(url);
    return response.text();
  });

  // 按次序输出
  for (const textPromise of textPromises) {
    console.log(await textPromise);
  }
}
```
上面代码中，虽然map方法的参数是async函数(**每个map是不会堵塞的，但是每个map里面的fetch会被堵塞，其实也就相当于并发执行，同理forEach也是一样的，如果不想，可以使用for循环**)，但它是并发执行的，因为只有async函数内部是继发执行，外部不受影响。后面的for..of循环内部使用了await，因此实现了按顺序输出。


#### 7.异步遍历器
    TIPS：等看完Iterator在补充 