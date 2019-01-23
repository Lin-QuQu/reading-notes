#### this
JS的this总是指向一个对象，而具体指向哪个对象是在运行时基于函数的执行环境动态绑定的，而非函数被声明时的环境。

一般有四种情况：
>1. 当函数作为对象的方法被调用时，this指向该对象。
>2. 当函数不作为对象的属性被调用时，也就是普通的函数方式，此时的this总是指向全局对象。window，global
>3. 当使用new运算符调用函数时，该函数总会返回一个对象，通常情况下，构造函数里面的this就是指向返回的这个对象
>4. Function.prototype.call() Function.prototype.apply()可以动态的改变this。当传入null的时候，非严格模式下，函数体内的this会指向默认的宿主对象（window,global）


---
#### call 、 apply
他们的作用一般有：
1. **改变this的指向**
getName.call(obj1),会输出obj1中的name，getName中的this会指向obj1对象。
2. **Function.prototype.bind(obj)**
指定函数内部的this指向。内部使用的还是apply
3. **借用其他对象的方法**
比如想要网arguments里面添加一个元素，而arguments又只是类数组，这个时候就可以借用Array.prototype.push.call.apply(arguments,'next')；至于能不能借用得到就要看这个函数内部是怎么实现的了。对传入的对象有什么要求。一般Math.max.apply(null,[1,2,3,4,5]);传入null，this会指向window.因为目的只是想借用一下max函数，所以直接传入null
 
---
#### bind
这个方法会创建一个函数的实例，其this值会被绑定到传给bind()函数的值
```js
window.color = 'red';
var o = {color:'blue'};
function sayColor(){
    alert(this.color);
}
var objectSayColor = sayColor.bind(o);
objectSayColor()//'blue'
//objectSayColor内部的this是指向o的，即使objectSayColor是在全局作用域中被调用的
```