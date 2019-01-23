## ES6模块

#### export
需要特别注意的是，export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。
```js
// 报错
function f() {}
export f;

// 正确
export function f() {};

// 正确
function f() {}
export {f};
```
另外，export语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。
```js
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
```
上面代码输出变量foo，值为bar，500 毫秒之后变成baz。

#### import
```js
import {a} from './xxx.js'

a = {}; // Syntax Error : 'a' is read-only;

a.foo = 'hello'; // 合法操作
```
import了进来之后，如果是一个对象是可以修改他的属性的，但是其他模块也会变成修改之后的属性。这种方式很难查错，所以还是最好将import进来的当做一个只读的对象来处理。

##### import的路径
import后面的from指定模块文件的位置，可以是相对路径，也可以是绝对路径，.js后缀可以省略。如果只是模块名，不带有路径，那么必须有配置文件，告诉 JavaScript 引擎该模块的位置。
```js
import {myMethod} from 'util';
```
上面代码中，util是模块文件名，由于不带有路径，必须通过配置，告诉引擎怎么取到这个模块。

##### “编译时” 还是 “运行时”
注意，import命令具有提升效果，会提升到整个模块的头部，首先执行。

上面的代码不会报错，因为import的执行早于foo的调用。这种行为的本质是，import命令是编译阶段执行的，在代码运行之前。

由于import是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。
    
    import是在编译的时候执行的，而require是在运行的时候执行的，所以require可以动态加载，而import是静态加载的。

由于现在的import是在编译时加载的，所以无法实现动态加载的效果，所以孕育了一个提案，import()

import命令能够接受什么参数，import()函数就能接受什么参数，两者区别主要是后者为动态加载。

import()返回一个 Promise 对象。下面是一个例子。
```js
const main = document.querySelector('main');

import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main);
  })
  .catch(err => {
    main.textContent = err.message;
  });
```

#### 浏览器的模块加载
浏览器加载 ES6 模块，也使用<script>标签，但是要加入type="module"属性。
```js
<script type="module" src="./foo.js"></script>
```
浏览器对于带有type="module"的<script>，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了<script>标签的defer属性。


#### ES6模块 和 CommonJS模块的差异
1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

第一个差异也就意味着：CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。
```js
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,
  incCounter: incCounter,
};
```

第二个差异是因为 CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

#### Node加载
Node 对 ES6 模块的处理比较麻烦，**因为它有自己的 CommonJS 模块格式，与 ES6 模块格式是不兼容的。目前的解决方案是，将两者分开，ES6 模块和 CommonJS 采用各自的加载方案。**

Node 要求 ES6 模块采用.mjs后缀文件名。也就是说，只要脚本文件里面使用import或者export命令，那么就必须采用.mjs后缀名。require命令不能加载.mjs文件，会报错，只有import命令才可以加载.mjs文件。反过来，.mjs文件里面也不能使用require命令，必须使用import。

#### 常见的模块化规范的区别
    **CommonJS:**
      1. 模块输出的是一个值的拷贝， 模块是运行时加载，同步加载
    　2.CommonJS 模块的顶层this指向当前模块
    　
        require : 加载所要依赖的其他模块
        module.exports 或者exports :对外暴露的接口
    **AMD模块 **
    异步加载，不阻塞页面的加载，能并行加载多个模块，但是不能按需加载，必须提前加载所需依赖
    require([module],callback):
    
    **ES6**
     ES6 模块的设计思想是尽量的静态化，编译时加载”或者静态加载，编译时输出接口
    模块功能主要由两个命令构成：export 和 import。

    export：用于规定模块的对外接口，
    import：用于输入其他模块提供的功能。
