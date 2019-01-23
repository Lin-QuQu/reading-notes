## Set和Map数据结构

#### Set
ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。
Set 本身是一个构造函数，用来生成 Set 数据结构。
```js
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4

// Set 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。
//例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size // 5

//上面代码也展示了一种去除数组重复成员的方法。
[...new Set(array)]
```
Set 结构的实例有以下属性。

    Set.prototype.constructor：构造函数，默认就是Set函数。
    Set.prototype.size：返回Set实例的成员总数。
    
    
    Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。
    四个操作方法。
    add(value)：添加某个值，返回 Set 结构本身。
    delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
    has(value)：返回一个布尔值，表示该值是否为Set的成员。
    clear()：清除所有成员，没有返回值。
    
    四个遍历方法:
    keys()：返回键名的遍历器
    values()：返回键值的遍历器
    entries()：返回键值对的遍历器
    forEach()：使用回调函数遍历每个成员
    另外 ... 也可用于Set
        let set = new Set(['red', 'green', 'blue']);
        let arr = [...set];
        // ['red', 'green', 'blue']
        
#### Map
JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。  

    1）size 属性
    size属性返回 Map 结构的成员总数。  
    2）set(key, value)
    set方法设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。
    set方法返回的是当前的Map对象，因此可以采用链式写法。
    3）get(key)
    get方法读取key对应的键值，如果找不到key，返回undefined。
    4) has(key)
    has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中
    5）delete(key)
    delete方法删除某个键，返回true。如果删除失败，返回false。
    6）clear()
    clear方法清除所有成员，没有返回值。
    
    keys()：返回键名的遍历器。
    values()：返回键值的遍历器。
    entries()：返回所有成员的遍历器。
    forEach()：遍历 Map 的所有成员。

