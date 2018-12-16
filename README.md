# Web前端面试题集合

因为公众号上最近没有那么多面试相关的话题了，但又不能拿之前的文章再说一遍车轱辘话，所以整理下面试相关的内容归档在这里，欢迎大家补充。

![扫码关注](./qrcode.jpg)

整体面试题目可分为两类，第一类是专业知识，我会把专业知识看的很重，如果满分100分，大约80分会在专业知识范畴里，剩下20分放在通用能力范畴里

## 专业知识

专业知识决定了候选人能做哪些工作，不能做哪些工作，同时现在的基础也决定了未来的发展方向和未来可以做的工作。

JavaScript、Html、CSS都是必备的基础，但是其中JS还是最重的一块，MVVM也可以算是工具使用，但它在前端中的地位实在是太特别了，所以下面这些中JS和MVVM额外重要一些

### JavaScript

函数、模块作用域，面向对象（类、继承）、Promise是我比较关注的点，因为它们让很多事情变得简单，你只要不把简单的事情搞得复杂就是高效率了，关注新语法和新api则是加分项

#### [函数作用域]以下代码运行的结果是怎样的？

```
var a = 5
var b = 6
function test(){
  var a = 7
  b = 8
  console.log({a,b})
}

test()
console.log({a,b})
```

这里test中的新定义的a就和外层的a没有关系了，而b则会被修改，其实var还存在声明提前的问题，大家可以改造下题目让它有更多的坑，个人觉得var不是未来所以变量声明提前的考察也不应该是重点

#### [声明提前]请举例说明变量/函数声明提前

```
console.log(a,b)
var a = 5
console.log(a,b)
function b(){}
```

#### [函数作用域]请举例描述const与var的不同？

1) 最早是没有const的，所以第一个问题是旧版本浏览器不支持const语法，如果你的用户会使用IE6、IE7那么使用const一定要编译（babel）

2) const是不可变的，和新语法中的let对应，试运行以下代码

```
var a=1
a=2
```

```
const a=1
a=2
```

```
let a=1
a=2
```


3) 那么是不是var变量不修改赋值的话就和const一样呢？请尝试分别运行下面两段代码，let又会是怎样的结果？

```
(()=>{console.log(a);var a=1})()
```

```
(()=>{console.log(a);const a=1})()
```

const、let只在声明后才存在，而var会提前声明

4) 重复声明

```
var a=1;var a=2; 
```

```
const a=1;const a=2; 
```

5) 总结：新语法规避了之前var语法中提前声明和重复声明容易导致的问题，同时约束了变量let和不可变量const让程序更加容易理解，能用const的时候尽量使用const，其次是let，只有你的代码需要不经过编译直接跑在旧版本得了浏览器的时候才去用var

#### [函数作用域、闭包]闭包是什么？什么场景下我们需要闭包？闭包可能导致什么问题？

1) 闭包是什么？闭包首先还是要先说函数作用域开始，简单说就是一个函数使用了外部作用域的变量，官方解释：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures

```
function getCounter(){
  let a = 1
  return ()=>a++
}
const counter = getCounter()

console.log(counter()) //1
console.log(counter()) //2
console.log(counter()) //3
console.log(counter()) //4

```

2) 什么场景下我们需要闭包？闭包可能会导致一些问题所以尽量不用闭包，但是它还是有存在的意义，那就是绝对的私有。我们知道即使是新语法中的类定义变量也是没有私有变量的（TypeScript是有的，也只是在编译到JS的过程中去检测），请尝试运行以下代码

```
class A {
  constructor(){
    this.a=5
  } 
  getA(){
    return this.a
  }
}

const a = new A()
console.log(a.getA()) //5
console.log(a.a) //5
```

```
function A() {
  var a=5
  this.getA=function(){
    return a
  } 
}

const a = new A()
console.log(a.getA()) //5
console.log(a.a) // undefined
```

3) 闭包存在的问题？最大的问题在于内存管理，正常来讲你的函数运行完了除了返回值不销毁其他的函数内申请的东西都可以清理，现在用了闭包就不一定了，试着运行以下代码并关注你的内存占用

```
var theThing = null;
var replaceThing = function () {
  var originalThing = theThing;
  var unused = function () {
    if (originalThing) // a reference to 'originalThing'
      console.log("hi");
  };
  theThing = {
    longStr: new Array(1000000).join('*'),
    someMethod: function () {
      console.log("message");
    }
  };
};
setInterval(replaceThing, 1000);
```

例子来自 https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec ，其中也详细讲解了内存泄露的原因


#### [函数作用域]箭头函数和使用function定义函数的区别

详细的解释参考 https://stackoverflow.com/questions/34361379/arrow-function-vs-function-declaration-expressions-are-they-equivalent-exch

应该尽量使用箭头函数，除非你需要像jq、vue一类的为了方便使用this，因为

- 即使是绑定this方便使用，不论是bind还是apply都需要理解成本，没有文档的话别人看到你的代码理解都要比一般代码费事一些

- 使用function会导致this丢失，容易出bug

```
$('a').click(function(e){
  e.preventDefault()
  $(this).text('binded')

  setTimeout(function(){
    console.log(this) // this 变成了 window
  })
})
```

- 有同学会反对说react项目里很多都用bind(this)，我觉得多数还是没有箭头函数时代留下来的习惯，箭头函数还是舒服的多

```
<a onClic={this.handleClick.bind(this)}>点我点我</a>
<a onClic={()=>this.handleClick()}>点我点我</a>
```

总的来说箭头函数的主要意义还是优化可读性和规避bug

#### [模块]请描述CMD和AMD的异同？

这道题已经过时了。估计大家写代码的时候应该都用的Node了吧（CommonJS），然后基于webpack或者browserfy一类的打包转换成浏览器端代码，当然也应该有些历史遗留的requireJS/seaJS项目也绝对是少数，趋势上还是Node的，只有Node才能ssr。

还是想了解具体的异的同直接百度

#### [面向对象]在JavaScript中如何实现类的继承？你了解多少种实现方法？

这个题目考察的不仅仅是继承的实现方式，而核心要考的是面向对象（OOP），要说面向对象就要提到三大特性：继承、封装、多态。如果JS还停留在JQ的年代，仅仅是为了做一些点击事件的绑定，做一些ajax请求，那是用不到了解面向对象的，但现在年代不同了，页面可以做到和一个客户端应用一样复杂（比如说Pinterest），除了业务的逻辑还包含了数据存储、按需加载、增量更新等，算起来比客户端可能还复杂，所以拿JS当脚本语言去做网页就直接沦为二流三流选手了

继承可能是减少重复代码最有效的办法了，所以才需要考察候选人能不能实现它，如果不能就可以直接归档到二流三流的池子了

1) 最好的方式就是新语法中的class了，既干净，有表现出了解新语法，一般而言对编译的过程也有所了解

```
class B extends A {}
```

2) 其次的是最原始的prototype继承方法，但是要想覆盖方法的话要小心prototype被修改会影响到父类，会这种方法至少是能实现继承的

```
function B(){
  A.call(this)
}
B.prototype = A.prototype
```

3) 更多的方法，其他的方法大多是通过一个函数，用类似2中的逻辑来处理继承，根据具体实现的不同可能性就变多了，性能上，结果上都有差异，甚至可以实现多重继承，具体大家可以去百度谷歌


#### [面向对象、闭包]在JavaScript中如何实现类的私有变量（面向对象的封装）？

前面已经提到过闭包，这里重贴一下代码

```
class A {
  constructor(){
    this.a=5
  } 
  getA(){
    return this.a
  }
}

const a = new A()
console.log(a.getA()) //5
console.log(a.a) //5
```

```
function A() {
  var a=5
  this.getA=function(){
    return a
  } 
}

const a = new A()
console.log(a.getA()) //5
console.log(a.a) // undefined
```

#### [异步逻辑]我们为什么需要Promise？

1) 我们为什么需要Promise？首先是避免回调地狱的问题

```
const mongoose = require('mongoose');
const Store = mongoose.model('Store');
const StoreB = mongoose.model('StoreB');

exports.createStore = (req, res) => {
  const store = new Store(req.body)
  storeB.findOne({
      id: req.params._id
  }, function(err, storeB) {
    if (!err) {
      store.storeb_id = storeB._id
      store.save(function(err, store) {
        if (!err) {
          // 成功
          res.redirect('/successPath', store);
        } else {
          res.redirect('/someOtherPathB', errors: err)
        }
      } else {
        res.redirect('/someOtherPathA', errors: err)
      }
    }
  });
};
```

看似正常的代码，唯一的不正常就是难以阅读和难以维护

```
const mongoose = require('mongoose');
const Store = mongoose.model('Store');
const StoreB = mongoose.model('StoreB');

// 使用es6 promise
mongoose.Promise = global.Promise;

exports.createStore = (req, res, next) => {
  const store = new Store(req.body);
  const itemB = StoreB
    .findOne({ id: req.params._id})
    .catch(next);

  itemB
    .then(record => {
      store.storeb_id = record._id;

      return store.save()
        .then(stores => Store.find())
        .then(stores => res.render('index', {stores: stores}))
    })
    // 错误处理
    .catch(next);
}
```

使用promise之后逻辑的层次变浅，好理解一些了，async/await在逻辑上还是promise的逻辑，但是更简洁也更易于阅读

```
const mongoose = require('mongoose');
const Store = mongoose.model('Store');
const StoreB = mongoose.model('StoreB');

//  使用es6 promise
mongoose.Promise = global.Promise

exports.createStore = async (req, res) => {
  const store = new Store(req.body);
  // 直到await执行完才会继续
  const record_we_want_to_associate = await StoreB.findOne({_id: req.params.id});
  store.storeb_id = record_we_want_to_associate._id

  // 直到await执行完才会继续
  await store.save();
  res.render('index', { stores: stores });
};
```

以上例子来自 https://medium.com/@ThatGuyTinus/callbacks-vs-promises-vs-async-await-f65ed7c2b9b4 里面还有更多的解释说明


#### [异步逻辑]如何定义一个Promise？请实现一个函数，返回一个会延迟若干毫秒后resolve的Promise

```
function delay(miliSeconds){
  // your code
}

delay(5000).then(()=>console.log('已经过了5秒钟'))

```

这个题目考察的是编写代码的熟练度，这么简单的逻辑写的慢了都要扣分的，为什么这么解释呢？

- 没有自己定制过Promise，等价于没有写过复杂的异步逻辑
- 如果有写过复杂的异步逻辑，都使用回调来实现，那代码质量是不合格的，学习能力也是不合格的
- 这个题目有多简单，可以对比下官方Promise示例 https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise

```
function delay(miliSeconds){
  return new Promise((reject,resolve)=>{
    setTimeout(resolve,miliSeconds)
  })
}
```

#### [异步逻辑]假设我有5个服务端JSON接口，当所有接口的值都获取到时触发一个函数，该如何实现？如果改成只要有其中任意一个返回了就触发，该如何实现？

这个题目考察的是异步逻辑的使用经验，知道`Promise.all`和`Promise.race`可能是对新知识保持了学习，也可能是工作中有用到复杂一点的异步逻辑，如果不知道也可以用笨的方法实现就是另一个层次，至少还有解决问题的能力，笨的办法也想不出来就没有分了

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/race

笨的方法的另一层意义在于理解`Promise.all`和`Promise.race`的底层实现，建议大家尝试自己实现一下这两个方法


#### [接口]代码实现DOM绑定屏幕触摸开始事件，如何实现双指缩放？简述冒泡和捕获的区别

单说这个题目有两个考察点，主要的考察点是事件绑定，其次是移动端开发经验。

1) 事件绑定

```
target.addEventListener(type, listener[, options]);
target.addEventListener(type, listener[, useCapture]);
target.addEventListener(type, listener[, useCapture, wantsUntrusted  ]); // Gecko/Mozilla only
```

https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener

这里要注意的就是`useCapture`了，如果父元素和子元素同时绑定了事件，`useCapture`决定了它们触发的顺序，`useCapture`默认为`false`为冒泡是从里向外，如果设置为`true`则表示捕获从`document`开始从外到里

事件绑定只是DOM接口的一个细节动作了，更多DOM操作也可以做许多题目了

2) 移动端开发经验

```
function startup() {
  var el = document.getElementsByTagName("canvas")[0];
  el.addEventListener("touchstart", handleStart, false);
  el.addEventListener("touchend", handleEnd, false);
  el.addEventListener("touchmove", handleMove, false);
  log("initialized.")
}
```

https://developer.mozilla.org/zh-CN/docs/Web/API/Touch_events

在pc中大部分都是click事件，移动版则都是touch，你要说都是用别人库里的`rotate`、`zoom`但是不知道原生的`touch`那也只是比完全不会强了一点点

接下来就要考察怎么用`touch`去实现一个`zoom`了，首先还是你是否理解别人封装的手势库的实现方式，其次就是对touch事件的了解和解决问题的思路问题了，不到50行代码

https://codepen.io/postor/pen/GYrYQM

```
$(document).ready(() => {
  const scaleMin = 0.1, scaleMax = 2.5
  let zooming = false, distanceStart = 0, scale = 1, scaleStart = 1, logCounter = 0
  let $el = $('.box'), $logs = $('.logs')

  $(window)
    .on("touchstart touchend touchcancle", function (e) {
      if (e.originalEvent.touches.length == 2) {
        zooming = true
        distanceStart = getDistance(e.originalEvent.touches);
        scaleStart = scale
        return
      }
      zooming = false
    })
  window.addEventListener('touchmove', ev => {
    ev.preventDefault()
    ev.stopImmediatePropagation()

    if (!zooming) return
    const newDistance = getDistance(ev.touches);
    scale = scaleStart * newDistance / distanceStart
    if (scale < scaleMin) {
      scale = scaleMin
    }
    if (scale > scaleMax) {
      scale = scaleMax
    }
    $logs.prepend(`[${logCounter++}]touchmove|scale=${scale}<br />`)
    $el.css('transform', `scale(${scale})`)
  }, { passive: false })
})

function getDistance(touches) {
  const [t1, t2] = touches
  const [p1, p2] = [t1, t2].map(p => {
    return {
      x: p.screenX,
      y: p.screenY
    };
  });
  return Math.hypot(p1.x - p2.x, p1.y - p2.y);
}

```

#### [接口]是否了解LocalStorage、IndexedDB、WebSocket、WebGL、WebRTC、WebAssembly等？

这个题目考察的是对浏览器新api的跟进和了解了，其实也并不算新，早就有很多项目在用localStorage了，比如多国语言就可以用localStorage存下来所有已经用到的翻译，IndexDB则被用来做前端日志（用于出错诊断统计等），WebSocket实时双向通信、WebGL图形图像3D等，WebRTC网页流媒体，WebAssembly则可以用其他语言写页面逻辑

当然这些只是举例子，有个什么理论来着是未知就是知道东西的周长，反正是知道的东西越多才能发现有更多不知道的东西


#### [数组]列举数组遍历操作相关的函数？

以下按常用程度排序

1) map 

map的使用场景主要是数组转化，举个例子，我有几个用户放在数组里，但我现在只想要他们的名字逗号分隔的字符串

```
const arr = [{id:1,name:'josh'},{id:2,name:'postor'}]
console.log(arr.map(x=>x.name).join(','))
```

2) reduce

当我们需要遍历并计算出一个结果的时候一般都可以用reduce，举个例子，获取数组中的最大值

```
const arr = [1,5,2,4,3]
console.log(arr.reduce((result,next)=>result>next?result:next))
```

3) filter

过滤器，只保留返回是true的元素

```
const arr = [1,5,2,4,3]
console.log(arr.filter(x=>x>2))
```

4) includes

包含，以前都要用indexOf的

```
const arr = [1,5,2,4,3]
console.log(arr.includes(2))
```

5) some

有一个返回true就可以
```
const arr = [1,5,2,4,3]
console.log(arr.some(x=>x>2))
```

6) every

全返回true才可以
```
const arr = [1,5,2,4,3]
console.log(arr.every(x=>x>2))
```

#### 什么事事件代理（event delegation）？

简单地说就是直接的绑定不能绑定到动态产生的元素上，基于事件冒泡的原理可以使用一个上层元素来代理事件，这个功能在jQuery.on中的实现可能是大家最常用的

```
$( "#dataTable tbody" ).on( "click", "tr", function() {
  console.log( $( this ).text() );
});
```

虽然同样可以解决动态元素事件绑定的问题，这里还是推荐使用MVVM的事件机制来解决

为什么默认的事件绑定无法解决动态元素事件绑定的问题？这里举例子就沿用前面代码的逻辑，当tr被点击的时候显示其中的文本

```
$( "#dataTable tbody tr" ).on( "click", function() {
  console.log( $( this ).text() );
});
```

然后假如我需要ajax一段数据，然后补充到table中

```
$( "#dataTable tbody" ).append('<tr><td>click on me and nothing will happen!</td></tr>');
```

现在你去点击它，控制台不会有任何输出，但如果你使用的是事件代理的方式，就会有输出了，说回来事件代理也是比较陈旧的技术，在复杂的项目里代理会影响到所有下级内容会变得很难控制，MVVM解决的就更优雅了，MVVM还是推荐react，至少也要会用vue


#### `this`在JavaScript中是如何工作的？

this在浏览器中默认是window对象，在对象中，在类中，在apply、call中、在箭头函数中可能都是不同的东西，这个问题很大程度上是OOP（面向对象编程）的范畴，深入理解可以参考我之前的翻译 [《在JavaScript中深入探讨this：为什么编写好的代码至关重要》](https://mp.weixin.qq.com/s/IwSq9D9qDBJp9SHXass40g)

#### 基于`prototype`的继承是如何工作的？

这个问题还是OOP范畴的，你是否知道JavaScript继承机制的基础呢？[《javascript 类的封装和类的继承及原型和原型链详解》](https://mp.weixin.qq.com/s/-OlMnpiK_IoHkYpxtXgsrw)


#### 解释为什么以下不能称作IIFE：function foo(){}(); 需要改变哪些才能使其成为IIFE？


为什么不能呢，因为运行都不会通过，不要被可能存在的全局污染忽悠了，运行出错都没机会污染，出错的原因在于引擎认为这是两句话

```
function foo(){}；();
```

所以小括号里是期待有个值的，或者是值的表达式，结果有没有找到值就报错了

```
function foo(){}();

// Uncaught SyntaxError: Unexpected token )
```


要使其变为IIFE最简单就是加小括号

```
(function foo(){})(); 
```

或者

```
(function foo(){}());
```

或者没有foo

```
(function (){})(); 
(function (){}());
```

或者箭头函数也可以

```
(()=>{})(); 
```

为什么有foo也可以？不会造成全局污染么？

不会，因为小括号限制了它的作用域

两种小括号的括法那种更好一些？是函数括起来还是整个括起来？

```
(function (){})(); 
(function (){}());
```

都一样，看自己喜欢的风格，个人倾向于第一种

考点在于IIFE是一种模式，而且是经常用得到的,这个就参考MDN官方吧[《立即执行函数表达式》](https://developer.mozilla.org/zh-CN/docs/Glossary/%E7%AB%8B%E5%8D%B3%E6%89%A7%E8%A1%8C%E5%87%BD%E6%95%B0%E8%A1%A8%E8%BE%BE%E5%BC%8F)

#### `null`、`undefined`和未定义的区别

我个人觉得`null`和`undefined`的区别并没有什么意义

```
let a = null;
let b;
console.log(typeof a);
// object
console.log(typeof b);
// undefined
```

类型不同，然而并没有什么意义，我为什么要去比较两个对象的类型呢？

```
null !== undefined 
// true
```

`!==`和`===`是先比较类型的，所以他们肯定不相等，按照推荐的编程规范，只有相同类型的变量才用来作比较

```
let logHi = (str = 'hi') => {
  console.log(str);
}

logHi(undefined);
// hi

logHi(null);
// null
```

函数默认值是判断`undefined`来决定赋值的，实际用的一般也是`logHi()`，传入`null`也是相当的脑抽了

所以`undefined`和未定义的比较才是有意义的，比如在Node.js环境下运行以下代码

```
var b
console.log(b)
//undefined

console.log(window)
//ReferenceError: window is not defined
```

要知道，Node.js的服务异常如果没有处理的话服务就挂掉了

#### `Array.forEach`和`Array.map`的区别及如何选择

简单的只要知道`map`是为了构造一个新数组，而`forEach`只是单纯的循环，对应的还有`for`、`for...in`等，了解更多可以参考 [《JavaScript完全手册》](https://mp.weixin.qq.com/s/IW5-sdpWFkabKt345CRRzA)

#### `call`、`apply`和`bind`的用法

首先要说尽量不要用，因为这会影响this的绑定，导致代码难以阅读

经常为了使用便利恰好需要绑定this，尤其是在造轮子的场景中，比如jquery、vue都有使用一些绑定

了解更多请参考[《在JavaScript中深入探讨this：为什么编写好的代码至关重要》](https://mp.weixin.qq.com/s/IwSq9D9qDBJp9SHXass40g)


#### 属性检测(feature detection)、属性推理(feature inference)和使用UA字符串

这个是用来做浏览器兼容性的，直接检测最靠谱，UA判断最不靠谱，比如某奇葩国产完全照搬chrome的UA

属性检测(feature detection)
直接判断是否存在

```
if (window.XMLHttpRequest) {
    new XMLHttpRequest();
}
```

属性推理(feature inference)
​基于浏览器的厂商实现，特性都是成组的支持，特定的检测几乎可以断定一些其他特性是否支持

```
if (document.getElementsByTagName) {
    element = document.getElementById(id);
}
```

使用UA字符串
直接根据UA判断浏览器版本，基于版本推理特性支持

```
if (navigator.userAgent.indexOf("MSIE 7") > -1){
    //do something
}
```

#### 尽可能详细的解释ajax，实现封装ajax，简述ajax的优缺点

AJAX不是JavaScript的规范，它只是一个哥们“发明”的缩写：Asynchronous JavaScript and XML，意思就是用JavaScript执行异步网络请求。

如果你想把标准写法和IE写法混在一起，可以这么写：

```
var request;
if (window.XMLHttpRequest) {
    request = new XMLHttpRequest();
} else {
    request = new ActiveXObject('Microsoft.XMLHTTP');
}
```

XMLHttpRequest对象的open()方法有3个参数，第一个参数指定是GET还是POST，第二个参数指定URL地址，第三个参数指定是否使用异步，默认是true，所以不用写。

注意，千万不要把第三个参数指定为false，否则浏览器将停止响应，直到AJAX请求完成。如果这个请求耗时10秒，那么10秒内你会发现浏览器处于“假死”状态。

最后调用send()方法才真正发送请求。GET请求不需要参数，POST请求需要把body部分以字符串或者FormData对象传进去。


ajax详解：
https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434499861493e7c35be5e0864769a2c06afb4754acc6000

从Ajax、JSONP 到 SSE、Websocket
http://www.52im.net/thread-1038-1-1.html


Ajax虽然是最古老的免刷新交互但它现在也依然是主流；一个阻塞可能是考点，IO、网络的逻辑都可能阻塞，避免阻塞才能写出高性能高质量的程序；一个同源策略可能成为考点，这是浏览器安全机制的一部分，从安全和浏览器熟悉的角度都应该掌握；一个技术选型可能是考点，比如简单ajax还是long polling，还是JSONP、SSE、WebSocket他们各有各自的特点，各自适合的场景

#### 是否有使用过JavaScript模板库？使用的什么库？

例如jQuery.template、lodash.template

在MVVM之前，jQuery直接操作DOM之后，有这样一个模板库活跃的阶段，虽然解决了构造html时候拼接字符串的问题，事件绑定也可以使用事件代理解决，但比起MVVM还是个很low的方案

#### 简要描述调用堆栈

#### 简要描述事件冒泡和捕获

#### “==”和“===”的区别在哪里？

如果单说他们俩的区别非常容易了，三等号需要提前比较一下类型，后面的比较和双等号一样的，用代码表示下

```
//三等号比较
function triple(a,b){
  if(typeof a != typeof b){
    return false
  }
  return double(a,b)
}

//双等号比较
function double(a,b){
  if(typeof a == typeof b){
    //返回值/引用比较
    ...
  }

  //如果不同类型则尝试转换为同类型比较
  ...
}
```

JS中有个特例就是`NaN`，它怎么比都不等于自己

```
NaN===NaN
//false
NaN==NaN
//false
```

其他的问题主要出在双等号比较上，举个WTFJS的例子吧

```
[] == ''   // -> true
[] == 0    // -> true
[''] == '' // -> true
[0] == 0   // -> true
[0] == ''  // -> false
[''] == 0  // -> true

[null] == ''      // true
[null] == 0       // true
[undefined] == '' // true
[undefined] == 0  // true

[[]] == 0  // true
[[]] == '' // true

[[[[[[]]]]]] == '' // true
[[[[[[]]]]]] == 0  // true

[[[[[[ null ]]]]]] == 0  // true
[[[[[[ null ]]]]]] == '' // true

[[[[[[ undefined ]]]]]] == 0  // true
[[[[[[ undefined ]]]]]] == '' // true
```

我并不要求大家深究到多深，最主要的问题还是比较的时候应当尽量使用三等号，因为双等号的自动转换类型经常有奇特的效果等着你

#### 请解释同源策略其中与JavaScript相关的部分

这个考点是实际应用经验，同源策略主要是对资源使用的限制，实际的网站应用还是很有可能使用一个其他域名下的接口数据的，代理就不算是和JavaScript相关的解决方案了（即使你强词夺理说用Node做代理），其中比较流行的就是JSONP了，其实JSONP就是利用JavaScript来绕过同源策略的一个方案

随便找的JSONP原理，百度搜应该有许多的 https://blog.csdn.net/hansexploration/article/details/80314948

另一个是js降域的，同样是绕过同源策略，这个方案只能用于同一个根域名的情况

同样百度找的降域解释 https://blog.csdn.net/wu_xiaozhou/article/details/52901374

最后我想提一下CSP，同源策略（CROS）和内容安全策略（CSP）可以简单理解一个控制访问，一个控制使用，事实上也是很少有CSP的题目，但我还是很看好CSP的，也希望这是你能给面试官一个超出预期惊喜的点

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS
https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP


### HTML

要知道以前JS可不是老大哥的地位，最初的网页并没有什么脚本，关于HTML我更关注SEO，SEO和语义化是相辅相成的，这里**所有**标签的理解就很重要

#### [SEO]百度谷歌是怎样收录你网站上的内容的？

#### [SEO]如果你不希望一些内容（例如后台页面）出现在百度的搜索中该怎么做？

#### [SEO、标签]搜索引擎一般对网页中哪些标签感兴趣，他们的作用分别是什么？

#### [标签]你是如何理解语义化的？`<b>`和`<strong>`的区别？

#### [标签]ul\th\abbr这些标签的含义？

#### [标签]请列举一些HTML5的新标签

#### [模板]是否了解pug\haml之类的html模板？它们带来了哪些便利？

### CSS

CSS很大的一块在知识面，我很少考察浏览器兼容的问题，但是类似盒模型、布局、动画GPU加速等都是好题目，当然还有SASS、LESS这种工具CSS变量grid布局这种新功能OOCSS、BEM这一类的标准也都可以考察一下

#### [CSS]简述两种盒模型的区别

#### [CSS]在一个元素上，一个样式属性的样式定义优先级关系是怎样的？

#### [CSS]简述animation与transition的异同

#### [CSS]如何将一个固定大小浮窗居中？有多少种实现方法？如果浮窗是不定宽高的呢？

#### [CSS]简单实现一个媒体查询，使得PC版网页字号16，手机版网页字号14

#### [CSS]是否了解flexbox、grid布局、css变量？

#### [框架]是否使用过bootstrap或者其他框架？假如我有一个列表，希望PC版每行显示4个，手机版每行显示1个，

#### [模板]是否了解scss/sass或less？他们接解决了什么问题？

#### [SVG]简述SVG的主要特点、使用场景？如果大量使用SVG该如何优化？

#### [字体]简述字体图标的主要特点、使用场景以及如何制作字体图标？

#### [字体]如果页面少量文字中使用了额外的字体，但字体文件较大，该如何优化？

### MVVM

MVVM为什么这重要，因为它解决了很多问题，比如JS中的DOM操作和事件绑定，比如css全局作用域干扰问题，总之很重要，对应的一些生态环境也应该划在MVVM范畴里，比如redux、ionic

#### [使用]使用react、angualr、vue中的任意一种将一个字符串的数组渲染到无序列表

#### [使用]在你最熟悉的MVVM框架中，组件的生命周期函数都有哪些，什么时候触发？

#### [使用]假如需要做一个问卷页面，包含所有问题的字符串数组可以通过一个后台接口/questions获得，所有问题都是问答题，用户回答后将答案post到另一个后台接口/subimit，请实现该页面逻辑，仅实现功能即可，无须样式，尽量使用MVVM

#### [使用]使用你最熟悉的MVVM框架，实现一个单选题的组件，组件接收以下属性：题目、答案数组、选择答案编号或回调函数

```
#react
<Question title={'1+1='} answers={[0,1,2,3]} onChange={(val)=>console.log(`selected ${val}`)} />

#angular
<question [title]="q.title" [answers]="q.answers" [(selected)]="q.selected" ></question>

#vue
<question :title="q.title" :answers="q.answers" :selected.sync="q.selected" ></question>

```

#### [扩展]是否有使用过store工具（redux、flux、vue store、rx store等）？在组件中如何使用的（代码示例从store中获取一个值并展示）？

#### [扩展]简述immutable的含义及使用场景

#### [扩展]是否使用过rxjs、storybook、i18n、jwt或类似工具？它们的使用场景？

#### [扩展]如何实现服务端渲染（SSR）？服务端渲染的好处有哪些？

### 工具使用

必须有webpack了，当然gulp、gurant也有他们的空间，文档工具（jsdoc）、lint工具（eslint）、测试工具（jest、selenium、puppeteer）也都是工具，还有TypeScript这种比较特别的，他们有的优化你的结果，有的优化你的生产过程，当然了都不会用也一样可以写代码

#### [webpack]webpack都做了哪些事情？

#### [webpack]简述webpack与gulp/gruant的异同

#### [webpack]如何配置一个loader，例如让它支持scss？

#### [webpack]有没有使用过webpack的webpack的包分析工具？

#### [gulp、gruant]你使用gulp/gruant自动化了哪些工作？

#### [测试]做过哪种（单元、功能、snapshot）测试？使用的什么工具？

#### [调试]某个手机上出现错误，如何定位错误原因？

#### [文档]是否有使用过自动化文档工具？

#### [lint]是否有使用过代码规范检查工具？能否举例说明比较有意义的规则，意义在哪？

#### [TypeScript]是否有使用TypeScript？请定义一个接口，和一个实现该接口的类

### 浏览器工作原理

这个话题和许多其范畴都有重叠，比如html不使用table布局、CSS的GPU加速、MVVM的虚拟DOM/shadowdom，但我觉得这个重叠区域不必较真到底归谁，只是从不同的维度来看同一个事情而已，比较专属的还是有的，比如资源加载流程、js并发限制、资源管道限制、webkit/v8的一些优化等

#### [整体理解]简述从输入网址，到页面加载完成的过程

面试官可在回答过程中选择略过不关注的点和深挖关注的知识点

#### [优化]给你一个网站，你会去做哪些优化，原因是什么？

#### [优化]我们为什么要做资源合并？并发请求（如迅雷）不应该更快？

#### [优化]JS和CSS标签应该放在页面中什么位置？为什么？

#### [优化]如何避免reflow？

#### [优化]Shadow Dom和Virtual Dom的异同？

#### [优化]load和DOMContentLoaded的区别？

DOM层次结构完全构建后，DOMContentLoaded事件将立即触发，当所有image和subframe完成加载时，load事件将触发。

【DOMContentLoaded 来自MDN】

> 当初始的 HTML 文档被完全加载和解析完成之后，DOMContentLoaded 事件被触发，而无需等待样式表、图像和子框架的完成加载。另一个不同的事件 load 应该仅用于检测一个完全加载的页面。 在使用 DOMContentLoaded 更加合适的情况下使用 load 是一个令人难以置信的流行的错误，所以要谨慎。注意：DOMContentLoaded 事件必须等待其所属script之前的样式表加载解析完成才会触发。

DOMContentLoaded可以在大多数现代浏览器上运行，但IE支持不好。 有一些变通方法可以在旧版本的IE上模仿这个事件，比如在jQuery库上使用它们，它们附加了IE特定的onreadystatechange事件。

查看DOMContentLoaded支持情况 https://caniuse.com/domcontentloaded

【load 来自MDN】

> 当一个资源及其依赖资源已完成加载时，将触发load事件。

当初jQuery流行起来也有一部分原因是它使用了DOMContentLoaded，比其他框架使用load事件的效率要高一些

### 安全

虽然网络安全大多是后端同学的事情，但信息安全的重要性是大家都懂的，尤其是当面试官本身不是前端专业的情况，在网站开发中后台开发做团队领导是绝大多数的情况，而他们对JS、浏览器之类了解较少更关注安全、网络协议、算法结构、周边能力等

#### 简述SQL注入的攻击的原理与防御

#### 简述XSS攻击的原理与防御

#### 简述CSRF攻击的原理与防御

#### 简述CRLF攻击的原理与防御

#### 简述中间人攻击的原理与防御

#### 简述验证码的作用是什么？如何实现？

#### 没有禁用目录列表有哪些安全隐患？

### 网络与协议栈

有线无线、3g4g、TCP/IP、UDP、http、https、http2、websocket、webrtc，虽然很少有候选人能答几个，答总是会问这么一道题

#### 移动网络与宽带网络有哪些不同？手机开着网络就是连着互联网的么？

#### TCP与UDP的不同点，各自的使用场景举例？

#### TCP的三次握手流程是怎样的？

#### HTTP协议的结构是怎样的？

#### HTTPS为什么比HTTP安全？

#### HTTP2与HTTPS的异同？

#### 是否使用websocket、webrtc？使用场景是什么？

#### 假设让你实现邮箱客户端的一个细节，当用户登录后的一段时间内没有关闭页面并且刚好收到了新邮件，如何设计逻辑能够使得客户端脚本及时知道有新邮件到了？还知道多少种其他方式？


### 周边能力：后端、测试、项目、交互

简单的说不懂就容易觉得都是别人的事，最要命的是做Web前端没有美感

#### 是否有后端语言经验？实现一个接口返回一个mysql表的前10条记录，尽可能直接使用框架的方法

#### 列举敏捷项目管理的几个实践方法？你之前的项目都是如何管理的？

#### 举例说明在交互上或美术方面对原设计的改进

#### 是否有测试相关的经验？如何设计测试用例？

### 算法结构与设计模式

这个不解释了

#### 实现一个冒泡算法

#### 实现一个先进先出的队列

#### 实现树的先深搜索

#### 实现一个二元搜索树

#### 输入一个集合（如1,3,6,7），找到所有和为指定数字（如10）的子集（1,3,6和3,7）

#### 举例说明单例模式、工厂模式、观察者模式

## 通用能力

### 学习能力

#### 举例说明最近学习的技术和学习它的原因

### 沟通能力

通过面试过程的问答来判断

### 追求卓越

#### 举例说明那个项目比较有成就感

### 诚实

通过对问题的深挖来判断候选人是否有夸大或虚构
