#### 基础铺垫
![b3ab6bb2879d22268a8f5c97d865d7c5](《JavaScript设计模式与开发实践》学习笔记（上）.resources/3B40B426-E3EB-4A28-9503-5A8BCA411C59.png)
![d1f7e630ed6595d53f69c743b22892bc](《JavaScript设计模式与开发实践》学习笔记（上）.resources/F5E4C776-6A73-442F-91EC-F41CD7C8BDA0.png)
![1c4c2f75b90ee8b52689ba113f2fc168](《JavaScript设计模式与开发实践》学习笔记（上）.resources/5CC50619-0EF4-40AF-9D4E-823073031F71.png)
![7c97ae12bca6638860fcff94a71d2068](《JavaScript设计模式与开发实践》学习笔记（上）.resources/C3C0E2AA-2D0C-4CD8-8621-7FC39834520F.png)
![60e2d7546c35e659cf419e20cd5a4638](《JavaScript设计模式与开发实践》学习笔记（上）.resources/11845489-D525-4880-BAC5-C7E16CEA2AA2.png)
![3b559cfba6619e0c7fc0c5e188ac8f9e](《JavaScript设计模式与开发实践》学习笔记（上）.resources/B61D2DD3-F638-4C69-AB5E-4440D5F05634.png)
![c786c216d7c0cb18f63fae495aee9b2b](《JavaScript设计模式与开发实践》学习笔记（上）.resources/197A6475-D821-4C70-88C9-DE8D8389E49A.png)
![9ea7a4b11a06301900a794a0d8e5d025](《JavaScript设计模式与开发实践》学习笔记（上）.resources/AC5FDDE8-0F9A-4B35-B0C9-FF00CD17849C.png)
![876164ec450bab551e192d969e05e0ac](《JavaScript设计模式与开发实践》学习笔记（上）.resources/017E39C2-5F18-45B3-8086-E91A8B6DBB01.png)
![c114a16cf383713c2772d6cc72db4370](《JavaScript设计模式与开发实践》学习笔记（上）.resources/36931DDB-06AB-4D8D-B20C-8835457F9188.png)

![a7ad1dfffb1f9978c4c715967e0461ef](《JavaScript设计模式与开发实践》学习笔记（上）.resources/23896D5C-9CF2-45BD-8432-0E71495A2D3F.png)
![486d260ecf74d3c8d96e15ba4c70aed4](《JavaScript设计模式与开发实践》学习笔记（上）.resources/69DE0403-D3EF-4DE6-9393-4237AD2AE416.png)
![a0cf94821c0952d09cfe02275ed00f04](《JavaScript设计模式与开发实践》学习笔记（上）.resources/E0942254-34B2-489E-85C2-E94E95DA3370.png)
![45f4c14f5e52e9d7c0a545ce73773370](《JavaScript设计模式与开发实践》学习笔记（上）.resources/FEFF17BE-88DC-4DCC-9F15-F3A9C6C2092F.png)

#### 单例模式
**单例模式的定义**：保证一个类仅有一个实例，并提供一个访问他的全局访问点。单例模式常见于模块，或作为工具为程序其他部分提供功能支持。
`举个栗子：比如一个页面中只需要一个弹出框，如果在显示的时候创建一个，再次显示再创造一个，页面就会有两个弹出框了。所以会想到隐藏的时候移除这个节点。但是频繁的创造和移除节点不合理。就会想到创建一个节点，控制他的显示或者不显示。但是如果用户从来不让这个节点有显示的机会，那创建的这个节点不就是多余的了吗，所以我们想到在用户点击的时候再创建这个节点，如果创建过了就不在重复创建，这个时候就需要单例模式了。`

**思路**：要实现一个标准的单利模式并不是很复杂，无非就是用一个变量来标志当前是否已经为这个类创建过对象，如果是，则在下一次获取该类的实例时，直接返回之前创建的对象。

**JS中的实践**：
```js
//基于类的单例模式，在JS中并不是很合适
Singleton.getInstance = (function(){
     var instance = null;
     return function( name ){
         if ( !instance ){
         instance = new Singleton( name );
     }
     return instance;
     }
})(); 
```
这个是一个基于’类‘的单例，基于“类”的单例模式在 JavaScript 中并不适
用，下面我们将以 WebQQ 的登录浮窗为例，介绍与全局变量结合实现惰性的单例。
![7fb7a8ca40c3fc6fff96511b1d008b46](《JavaScript设计模式与开发实践》学习笔记（上）.resources/741D0189-8C5E-4752-AA78-ED06629B0065.png)
![13390bed127866684de14254d2450890](《JavaScript设计模式与开发实践》学习笔记（上）.resources/E56ACBEE-6FD8-47B6-9EBB-D811ECF53DB3.png)
![dd28715d66929848dfc6576e2b9e2ef8](《JavaScript设计模式与开发实践》学习笔记（上）.resources/D884BE6B-5B10-4213-83C8-EFF461E79139.png)
![fb2ece9ad6bfd3cedc9fe9822d5deb3c](《JavaScript设计模式与开发实践》学习笔记（上）.resources/3A4B3A05-E312-4435-83AA-D3AE4DC9489C.png)
![b87837d1647ab100a636ba031e9ba096](《JavaScript设计模式与开发实践》学习笔记（上）.resources/70EFE4EA-C6FA-433F-AC02-9E290F7D1DD3.png)
![79f7d2c13d398c8b188a932d9b848ace](《JavaScript设计模式与开发实践》学习笔记（上）.resources/935D8AA1-4F1C-4B32-BF30-CF2299C2E7A1.png)
现在我们就把如何管理单例的逻辑从原来的代码中抽离出来，这些逻辑被封装在 getSingle函数内部，创建对象的方法 fn 被当成参数动态传入 getSingle 函数：
```js
var getSingle = function( fn ){
  var result;
  return function(){
    return result || (result = fn .apply(this, arguments));
  }
};
```
接下来将用于创建登录浮窗的`方法用参数 fn 的形式传入 getSingle`，我们不仅可以传入createLoginLayer，还能传入 createScript、createIframe、createXhr 等。之后再让 getSingle `返回一个新的函数，并且用一个变量 result 来保存 fn 的计算结果。result 变量因为身在闭包中，它永远不会被销毁。在将来的请求中，如果 result 已经被赋值，那么它将返回这个值。`代码如下：
```js
var createLoginLayer = function(){
 var div = document.createElement( 'div' );
 div.innerHTML = '我是登录浮窗';
 div.style.display = 'none';
 document.body.appendChild( div );
 return div;
};
var createSingleLoginLayer = getSingle( createLoginLayer );
document.getElementById( 'loginBtn' ).onclick = function(){
 var loginLayer = createSingleLoginLayer();
 loginLayer.style.display = 'block';
};
//下面我们再试试创建唯一的 iframe 用于动态加载第三方页面：
var createSingleIframe = getSingle( function(){
 var iframe = document.createElement ( 'iframe' );
 document.body.appendChild( iframe );
 return iframe;
});
document.getElementById( 'loginBtn' ).onclick = function(){
 var loginLayer = createSingleIframe();
 loginLayer.src = 'http://baidu.com';
};
```
在这个例子中，我们把`创建实例对象的职责`和`管理单例的职责`分别放置在两个方法里，这两个方法可以独立变化而互不影响，当它们连接在一起的时候，就完成了创建唯一实例对象的功能，看起来是一件挺奇妙的事情。

#### 策略模式
**定义**：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。`举个栗子：当我么需要为一个动画过程指定运动路线的函数的时候linear，easeIn，easeOut等等，我们不是在动画实现的函数中用if else来分隔多个分支情况。而是把这些函数各自定义（strategies）。通过一个统一的入口（context）按照用户传入的需求，来调用不同的函数`

**JS中的实现**：JS中实现策略模式非常自然，有时候都有点察觉不出自己使用了策略模式。下面这个是一个计算年终奖的实现:
![e073574b9ed9611a1027517988cedd30](《JavaScript设计模式与开发实践》学习笔记（上）.resources/035E4253-F02F-4711-9D0C-7C7BEFB85C2C.png)

