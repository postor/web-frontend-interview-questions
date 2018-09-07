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

#### [异步逻辑]我们为什么需要Promise？如何定义一个Promise？

1) 我们为什么需要Promise？首先是避免回调地狱的问题

#### [异步逻辑]请实现一个函数，返回一个会延迟若干毫秒后resolve的Promise

#### [数组]列举数组操作相关的函数，他们的参数返回值分别是什么？是否会改变原数组？

```
function delayMiliseconds(ms){
  // your code here
  
  
}

delayMiliseconds(5000).then(()=>console.log('5 seconds past'))

```

#### [异步逻辑]假设我有5个服务端JSON接口，当所有接口的值都获取到时触发一个函数，该如何实现？如果改成只要有其中任意一个返回了就触发，该如何实现？

#### [接口]代码实现DOM绑定屏幕触摸开始事件，简述冒泡和捕获的区别

#### [接口]是否了解LocalStorage、IndexedDB、WebSocket、WebGL、WebRTC、WebAssembly等？

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
