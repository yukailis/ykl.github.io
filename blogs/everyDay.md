---
title: 每日一题
date: 2021-07-06
---

```
1、下面代码会执行什么？
try {
    (async function() { a().b().c() })()
} catch (e) {
    console.log(`执行出错：${e.message}`)
}
考察三点：
1. 执行一个没有定义的函数会发生什么
2. 在 async 内部发生报错会发生什么
3. try catch 只能捕获同步代码的异常

因为我们执行了一个未定义的函数，所以会报错 a is not defind，又因为是在 async 中，所以报错信息会显示 in promise。
最后 try cathch 只能捕获同步代码的抛错，因为是 async，所以走不到 catch 里面。

如果我们把代码这样改一下就可以了：
try {
    await (async function() { a().b().c() })()
} catch (e) {
    console.log(`执行出错：${e.message}`)
}
```


```
2、以下代码输出什么，为什么？ 
    try {
      let a = 0;
      (async function() {
        a += 1;
        console.log("inner", a);
        throw new Error("123");
        // a()
      })();
      console.log("outer", a);
    } catch (e) {
      console.warn("Error", e);
    }
    
inner 1
outer 1
Uncaught (in promise) Error: 123

++ async修饰的函数同步代码执行异步操作++
async定义了一个会返回promise的异步函数，此时作为宏任务执行，内部也是同步执行，直到抛出了异常被reject了，可以看做异步函数的回调，进入了微任务队列中，因为是微任务，
这时宏任务队列里还有console.log('out' ,a)未执行，执行完后再去执行微任务里的回调即throw new Error('123')

inner的a输出1很好理解，outer的a输出1是因为，在打印的时候 已经执行过a+=1了，异步方法内部形成了闭包，导致a能访问到外部变量a
```


```
3.有如下 dom 元素，如何绑定事件，能够使使点击每个 p 标签时，显示它在 div 中的顺 序？能否用下面列举的两种方法实现？
    <div>
        <p>点击我时，会显示 1！</p >
        <p>点击我时，会显示 2！</p >
        <p>点击我时，会显示 3！</p > 
    </div> 
    
    
第一种方式：
    document.querySelector('div').onclick = function(e) { 
      var $target = e.target
      if($target.tagName.toLowerCase() === 'p'){
        var index = [...this.children].findIndex(target =>{
            return target == $target 
        })
        console.log(index)
      }
    }
    
第二种方式：    
    var $pList = document.querySelectorAll("div p")
    var onClickEvent = function(i){
      return function(){
        console.log(i)
      }
    }
    for(let i=0;i<$pList.length;i++){
      $pList[i].onclick = onClickEvent(i)
    }

```


```
4.渲染图层和复合图层是什么？
html的渲染流程包括了
构建dom->解析css->生成布局树->分层->绘制->合成->显示
浏览器的页面实际上被渲染引擎分成了很多图层，这些图层按顺序叠加在一后合成了最终的页面。
这些图层分成了两大类：渲染图层和复合图层
渲染图层是页面普通的文档流，单个图层操作；
复合图层是一个独立的，脱离文档流的图形层，将一些图层合并到一起的操作；
合成操作是在合成线程上完成的，不会影响到主线程，解析完css直接合成显示达到硬件加速。
```


```
5.setCookie为什么支持域共享，浏览器底层是怎么实现的？
跨域共享cookie的方法：设置cookie.setDomain(".xxx.com");
通常，cookie不能跨越域传递，只有那些创建它的域才能访问，同一根域名下的二级域名，三级域名可以直接共享。但你可以利用重定向来间接的获取cookies。
```


```
6.vue编译的时候，怎么操作AST?
```


```
7.编译器和解释器：V8是如何执行一段JavaScript代码的？
js是解释型语言，通过解释器动态解释和执行。
//编译器、解释器、抽象语法树、字节码、机器码、及时编译器。

词法分析：将源码拆解成一个个token,不能再分，最小的单次或字符串
//var:关键词, name:标识符, =:赋值, '每日一题'：字符串
var name = '每日一题'

语法分析：将token数据，根据语法规则生成AST

语义分析：根据 AST 生成字节码，并解释执行字节码

源代码通过词法分析和语法分析生成AST抽象语法树并生成执行上下文，然后语义分析生成字节码，通过解释器转化成机器码，边解释边执行

```


```
8.watcher、computed源码做了哪些优化？
```


```
9.vdom什么情况下不如原生dom操作？
```


```
10.为什么微任务比宏任务快，浏览器底层是怎么做的？
页面活起来主要是靠消息队列和事件循环机制，我们把消息队列中的任务称为宏任务，遵循先进先出的原则。
页面的渲染事件和各种用户交互事件等随时可能被添加到消息队列中，这种优先级高的任务我们没办法控制执行的时间和顺序，就有异步回调的概念。
把优先级高的任务放主函数执行结束之后、当前宏任务结束之前，这种任务可以有多个，形成一个微任务队列，微任务执行完毕后再去执行下个宏任务。

//微任务和宏任务是绑定的，每个宏任务在执行时，会创建自己的微任务队列。
//微任务的执行时长会影响到当前宏任务的时长。 
//在一个宏任务中，分别创建一个用于回调的宏任务和微任务，微任务都早于宏任务执行。
```


```
11.CSP 和 CORS 之间的关系：

同源策略就是说同源页面随你瞎搞，但是不同源之间想瞎搞只能通过浏览器提供的手段来搞

比如说
1. 读取数据和操作 DOM 要用跨文档机制
2. 跨域请求要用 CORS 机制
3. 引用第三方资源要用 CSP
```

```
12.XSS攻击？
在用户的页面中注入恶意脚本，然后再通过恶意脚本将用户页面的数据上传到黑客的服务器上，最后黑客再利用这些数据进行一些恶意操作

- 存储型：将恶意脚本存储在服务器，访问含有恶意脚本的页面，数据上传到恶意方服务器上。（表单输入）
- 反射性：恶意脚本当做服务请求的一部分，然后把脚本返回到页面页面，利用脚本做恶意操作。（恶意链接）
- 基于 DOM：在 Web 资源传输过程或者在用户使用页面的过程中修改 Web 页面的数据。

阻止XSS攻击：
服务器对输入脚本进行过滤或转码；
限制加载其他域下的资源文件；
禁止向第三方域提交数据；
禁止执行内联脚本和未授权的脚本；
使用 HttpOnly 属性，Cookie 只能使用在 HTTP 请求过程中；
```


```
13.CSRF 攻击？
CSRF 攻击就是黑客利用了用户的登录状态，并通过第三方的站点来做一些坏事。
发起 CSRF 攻击需要具备三个条件：目标站点存在漏洞、用户要登录过目标站点和黑客需要通过第三方站点发起攻击。
防止 CSRF 攻击：充分利用好 Cookie 的 SameSite 属性、验证请求的来源站点和使用 CSRF Token
```


```
14.HTTPS 的握手过程?
对称加密传授数据+非对称加密交换秘钥

1. 首先是tcp的三次握手建立连接
2. client发送random1+支持的加密算法集合（clientHello）
3. server收到信息，返回选择一个加密算法+random2（serverHello）+ 证书+ 确认
4. clent验证证书有效性，并用random1+random2生成pre-master通过服务器公钥加密 发送给server
5. server收到premaster，根据约定的加密算法对random1+random2+premaster（解密）生成master-secret，然后发送预定成功
6. client收到生成同样的master-secert，对称加密秘钥传输完毕

今日总结
浏览器安全主要包括页面安全、系统安全、传输安全三个部分。
https主要保证传输过程的安全，从防止中间人窃取修改伪造的角度循序渐进的介绍了https的实现过程。
1. 对称加密传输（协商秘钥的过程容易被窃取）
2. 非对称加密传输（服务端用私钥加密的内容，可以通过它的公钥进行解密）
3. 非对称加密交换秘钥、对称加密传输内容（DNS劫持 如何保证服务器是可信的）
4. 引入CA权威机构保证服务器可信性。
数字证书的申请过程：服务器生成一对公钥和私钥，私钥自己保存，通过公钥+企业+网站信息去CA机构申请证书
。CA机构通过全方位的验证给这个网站颁发证书，证书内容包括企业信息、证书有效期、证书编号，以及自己私钥加密上述信息的摘要、网站的公钥。
服务器就获得了CA的认证。
浏览器认证证书过程：浏览器从服务器拿到网站的证书，通过CA的公钥解密证书信息的摘要跟使用摘要算法计算企业信息等的摘要对比，如果一致则证明证书有效。
如果证书CA是可靠的呢，通过给CA颁发证书的根CA验证，通常操作系统中包括顶级CA证书（它们自己给自己签名称为自签名证书，我们自己生成证书也是自签名证书 只是它不是操作系统内置的）
```



```
15./*
 * 对一个很长的名字数组，做分片更新名字请求：
 * 1. 分片里的更新是并行的，执行 changeName
 * 2. 各个分片间是串行的，执行 sleep
 * 这个函数接受三个参数，名字列表、分片数量，每次分片后的等待时间
 * 比如：
 * slicePostTask(['aa', 'bb', 'cc', 'dd', 'ee', 'ff', 'gg', 'hh'], 2, 2000)
 * // => ['aa', 'bb']
 * waiting 2s
 * // => ['cc', 'dd']
 * waiting 2s
 * // => ['ee', 'ff']
 * waiting 2s
 * // => ['gg', 'hh']
 */

const changeName = (name) => new Promise((resolve, reject) => {
  setTimeout(() => resolve(name), 1000)
})

const sleep = time => new Promise((resolve, reject) => {
  setTimeout(resolve, time)
})

const slicePostTask = async (names, chunkSize, time) => {
  // todo
}



// splice会改变原始数组,返回被删除的项目
const changeName = (name) => new Promise((resolve, reject) => {
    setTimeout(() => resolve(name), 1000)
})

const sleep = time => new Promise((resolve, reject) => {
    setTimeout(resolve, time)
})

const slicePostTask = async (names, chunkSize, time) => {
    // todo
    while(names.length){
        const current = names.splice(0, chunkSize)
        console.log(current);
        await changeName(current)
        if(names.length){
          await sleep(time)
        }
    }
}
slicePostTask(['aa', 'bb', 'cc', 'dd', 'ee', 'ff', 'gg', 'hh'], 2, 2000)
```


```
16.给定一个任意数组，实现一个通用函数，让数组中的数据根据 key 排重：
const dedup = (data, getKey = () => {} ) => {
  // todo
}
let data = [
  { id: 1, v: 1 },
  { id: 2, v: 2 },
  { id: 1, v: 1 },
];

// 以 id 作为排重 key，执行函数得到结果
// data = [
//   { id: 1, v: 1 },
//   { id: 2, v: 2 },
// ];

let data1 = [
  { id: 1, v: 1, id1: 1 },
  { id: 2, v: 2, id1: 2 },
  { id: 1, v: 1, id1: 1 },
]

// 以 id 和 id1 作为排重 key，执行函数得到结果
// data1 = [
//   { id: 1, v: 1, id1: 1 },
//   { id: 2, v: 2, id1: 2 },
// ];


const dedup = (data, getKey = () => { }) => {
    const result = []
    const map = new Map()
    data.forEach(item => {
        const key = getKey(item)
        if (!map.has(key)) {
          result.push(item)
          map.set(key, 1)
        }
    })
    return result
}
// console.log(dedup(data, item => {return item.id}))


//es6中的map是新增数据结构，类似于对象，key可以为任何数据类型，用法和普通对象基本一致。
//Map 对象构造函数生成实例，迭代出新的对象 
//属性和方法
//size：获取成员的数量
//set：设置成员 key 和 value
//get：获取成员属性值
//has：判断成员是否存在
//delete：删除成员
//clear：清空所有

//遍历方法
//keys()：返回键名的遍历器。
//values()：返回键值的遍历器。
//entries()：返回所有成员的遍历器。
//forEach()：遍历 Map 的所有成员。




```


```
17.有一个嵌套层次很深的对象，key 都是 a_b 形式 ，需要改成 ab 的形式，注意不能用递归。
const a = {
  a_y: {
    a_z: {
      y_x: 6
    },
    b_c: 1
  }
}
// {
//   ay: {
//     az: {
//       yx: 6
//     },
//     bc: 1
//   }
// }

// replace(正则，回调函数) 匹配到一个结果，回调函数执行一次
// JSON.stringify 把对象转成字符串
let parseKey = (obj) => {
   try {
        const reg = /(\w+)_(\w+)":/g
        return JSON.parse(JSON.stringify(obj).replace(reg, (match) => match.replace('_', '')))
    } catch (error) {
        return obj
    }
}

```


```
18.以下代码输出什么？为什么？
;(function b() {
  b = 123
  console.log(b)
})()
<!--()里面不能包含语句（只能是表达式）所以解析器在解析到function关键字的时候，会把它们当作function表达式，而不是正常的函数声明。-->

打印function b
1.函数执行会创建全局上下文：
{//  全局上下文
  argument: {}
}
2.自执行函数创建块级作用域，b=123变量提升到全局，创建函数上下文：
{ //  全局上下文
  argument: {}
  b: undefined
}

{// 自执行函数上下文
  argument: {}
  b: reference to function b(),
}
3.执行自执行函数，window.b = 123给全局b赋值

{ //  全局上下文
  argument: {}
  b: undefined
}

{// 自执行函数上下文
  argument: {}
  b: reference to function b(),
}
4.函数最后执行console.log(b);根据作用域链，在当前作用域查，所以打印function b()
```


```
19.instanceof能否判断基本数据类型？
function myInstanceof(left,right){
    // 如果是基本数据类型 返回false
    if(typeof left == 'object' || left === null) return false;
    //getProtypeOf是Object对象自带的一个方法，能够拿到参数的原型对象
    left proto = Object.getPrototypeOf(left);
    while(true){
        // 查找到尽头 还没找到
        if(left == null) return false;
        // 找到相同的原型对象
        if(proto == right.prototype) return true;
        proto =  Object.getPrototypeOf(proto);
   }
}
console.log(myInstanceof("111", String)); //false
console.log(myInstanceof(new String("111"), String));//true
```



```
20.let const var的变量提升
let、const 的「创建」过程被提升了，但是初始化没有提升。
var 的「创建」和「初始化」都被提升了。
function 的「创建」「初始化」和「赋值」都被提升了。
```


```
21、引起内存泄漏的操作有哪些？
垃圾回收机制：
栈：当ESP指针下移，也就是上下文切换之后，栈顶的空间会自动被回收
堆：新生代内存和老生代内存
- 新生代内存：分为两部分，from和to；
    把from中的对象检查一遍，存活的放进to,其他的回收；
    from和to进行对调，如此循环；
- 老生代内存：经过多次回收后依然存在就会被放进老生代内存，称为晋升；
    标记-清除：遍历堆中的所有对象，对它们做上标记。对于代码环境中使用的变量以及被强引用的变量取消标记，剩下的就会被删除
    增量标记：JS的单线程机制，V8在垃圾回收时，会阻塞业务逻辑的执行，如果老生代的垃圾回收任务重，将严重影响性能。增量标记将一口气完成的标记任务分为很多小的部分完成，每做完一个小的部分就"歇"一下，就js应用逻辑执行一会儿，然后再执行下面的部分
    
s1. 全局变量未被释放
2. 闭包
3. 未被销毁的定时器
4. dom删除，事件未被清空
```


```
22、数组扁平化
var arr = [1, [2, [3, 4]]]; 
console.log(flatten(arr)) // [1, 2, 3, 4]

1. 直接调用
    arr.float(Infinity)
    
2. 递归
    function flatten(arr){
      let result = []
      for(let i = 0; i < arr.length; i++) {
        if(Array.isArray(arr[i])){
          flatten(arr[i])
        }else{
          result.push(arr[i])
        }
      }
      return result
    }
    
3. toString // 方法返回一个表示该对象的字符串
    function flatten(arr) {
        return arr.toString().split(',')
    }
    
4. reduce // map映射 和 reduce数组元素进行组合
// reduce(初始值或上一次调用的返回值，当前值，当前索引，调用reduce的数组)
    function flatten(arr) {
        return arr.reduce((init,current)=>{
            return init.concat(Array.isArray(current) ? flatten(current) : current)
        },[])
    }
    
5. es6扩展运算符 ...
    function flatten(arr) {
        while (arr.some(item => Array.isArray(item))) {
            arr = [].concat(...arr);
        }  //ES6新方法
        return arr;
    }
```


```
23.如何实现ajax请求
- 实例化XMLHttpRequest对象得到实例，调用open(http方法,地址,是否异步)
- send发送请求组
- 监听readystatechange事件判断请求状态，分为0,1,2,3,4这四种 状态，4为数据接收完成
- 通过实例的status属性判断请求是否成功

var xhr = new XmlHttpReauest()
xhr.open('get','url',true || false)
xhr.send()
xhr.onreadystatechange = function(){
    if(xhr.readyState==4) {
        if(xhr.status==200) {
          console.log(xhr.responseText);
        }
     }
}
```


```
24.简要介绍ES6
ES6是javascript的新一代标准，需要通过babel进行编译为ES5代码执行。
1. 在变量声明方面增加了let const，有局部变量的概念；
2. 增加了字符串、 数组、正则、对象、函数的一些扩展和方法，常用的解构赋值、模板字符串、函数的默认参数；
3. 同时引入了一种新的数据类型symbol，可用typeof进行检测；
4. 为了解决异步，引入了promise和generator；
5. 实现Class和模块，通过Class可以更好的面向对象编程，使用模块加载方便模块化编程；
```


```
25.对js原型的理解
function Person() {
}
var person = new Person();
person.name = 'ykl';
console.log(person.name) // ykl
Person 就是一个构造函数，我们使用 new 创建了一个实例对象 person

js没有类和继承的概念，js是通过原型来实现继承的；
构造函数Person默认有一个prototype属性，属性值是一个对象，指向函数的原型对象Person.prototype。
Person.prototype对象又有一个contructor属性，指向构造函数Person。
new调用时返回一个新的实例对象person，有一个__proto__属性指向Person.prototype对象，将这个叫做隐式原型；
原型链：
我们在使用一个实例的方法的时候，会先检查 这个实例中是否有这个方法，没有则会继续向父类对象查找，直到指向Object对象为止,
这样就形成了一个原型指向的链条, 即原型链
```

```
26.对js模块化的理解
// webpack实际上会将ES导入转换为CommonJS规范
在ES6出现之前，js没有标准的模块化概念，这也就造成了多人开发容易造成全局污染的情况，为了解决这个问题陆续提出了AMD规范和CMD规范；
1. Nodejs发布的commonJS模块存在硬盘中，加载和读取几乎不需要时间；浏览器的加载取决于网速，需要异步加载；
2. AMD依赖前置，等到所有依赖加载完成之后才会执行回调函数。requireJS使用define来定义一个模块，使用require方法来加载一个模；
3. CMD依赖就近，需要的时候再去加载。seaJS使用define定义模块，require加载模块，exports暴露变量；
4. ES6模块化，通过export和import来导出和导入模块；
```


```
27.宏任务和微任务
js是单线层的，一些高耗时操作会带来阻塞问题。为了解决这个问题，js有两种任务执行模式：同步模式和异步模式。同步任务是由宿主（浏览器、node)发起的，异步任务是js引擎发起的。
宏任务：script整体代码块、setTimeout、setInterval、事件队列
微任务：Promise.[ then/catch/finally ]、process.nextTick

```


```
28.使用new操作符实例化一个对象的具体步骤
    function Person(name, age) {
        this.name = name;
        this.age = age;
    }
    console.log(new Person('蒙娜丽莎', 18));
    
    function createPerson(name, age) {
      // 1.生成一个新的对象
        var obj = {};
     // 2.将Person中的this指向obj（将构造函数的作用域赋给新对象也就是说this指向了新的对象）
        // Person.call(obj,name,age);
        Person.apply(obj, arguments);
     // 3.原型链继承，一旦实例化，实例应该拥有原来的函数的原型属性
        obj.__proto__ = Person.prototype;
     // 4.返回新对象 obj
        return obj;
    }
    console.log(createPerson('蒙娜丽莎', 18));
```


```
29.递归和迭代的区别是什么，各有什么优缺点？
程序调用自身称为递归，利用变量的原值推出新值称为迭代；
递归
- 优点:大问题转化为小问题，可以减少代码量，代码精简，可读性好； 
- 缺点:调用浪费了空间，递归太深容易造成堆栈的溢出。
迭代
- 优点：代码运行效率好，因为时间只因循环次数增加而增加，没有额外的空间开销
- 缺点：代码不如递归简洁

```


```
30.什么是事件循环？
js引擎是单线程的，除了这个单线程，还有一个任务队列，
在执行js代码的过程中，执行引擎遇到异步的延时方法，如定时器，DOM事件，
会将这些方法交给相应的浏览器模块处理，延时方法触发的时候会被添加至任务队列，而这些任务队列中的方法只有js的主线程空闲了才会执行。
这也就是说我们常常用的定时器定的时间参数只是一个触发条件，具体多少时间后执行其实还需要看 js主线程空闲与否
```


```
31.原生JS操作DOM的方法有哪些？
获取节点：getElementById、getElementsByClassName、getElementsByTagName、 getElementsByName、querySelector、querySelectorAll
操作元素属性：getAttribute、setAttribute、removeAttribute
节点增删改：appendChild、insertBefore、replaceChild、removeChild、createElement
```


```
32.typeof操作符返回值有哪些，对undefined、null、NaN使用这个操作符分别返回什么
number、string、boolean、undefined、object、function、symbol
undefined 使用返回undefined
null使用返回object
NaN使用返回number
```


```
33.javascript做类型判断的方法有哪些？
typeof、instanceOf、Object.prototype.toString()
```

```
34.同源策略指什么？
同源：协议+域名+端口，
指只有具有相同源的页面才能够共享数据，比如cookie，能够保证web网页的安全性
```


```
35.ES6之前JavaScript如何实现继承？
ES6之前的继承是通过原型来实现的，也就是每一个构造函数都会有一个prototype属性，
然后如果我们调用一个实例的方法或者属性，首先会在自身寻找，
然后在 构造函数的prototype上寻找，
而prototype本质上就是一个实例，因此如果prototype上还没有则会往prototype上的构造函数的prototype寻找，因此实现继承。
可以让构造函数的prototype是父级的一个实例就是以实现继承。
```


```
36、如何阻止事件冒泡和默认事件？
阻止冒泡
IE8以下：cancelBubble为true
其他浏览器：stopPropagation()
阻止默认事件
IE8以下：returnValue为false
其他浏览器：stopPropagation()

```


```
37.addEventListener有哪些参数？
有三个参数，第一个是事件的类型，第二个是事件的回调函数，第三个是一个表示事件是冒泡阶段还是捕获阶段捕获的布尔值，true表示捕获，false表示冒泡
```


```
38.深拷贝和浅拷贝
浅拷贝：仅复制对象的引用，增加了一个指针指向被复制的内存地址
深拷贝：增加了一个指针并且申请了一个新的内存，使这个增加的指针指向这个新的内存
```


```
39.原生js字符串方法有哪些？
indexof lastIndexof search match
substring substr slice
replace split toLowerCase toUpperCase
```


```
40.ES6的暂时性死区和块级作用域
块级作用域：代码存在{}内，变量绑定在这个区域内，不受外部的影响
暂时性死区：在代码块内，未声明之前不可调用

```


```
41.页面从输入URL到展现发生了什么
我觉得可以分为两个阶段 一个是网络请求，一个是解析渲染。
输入url，会根据输入的域名找到对应ip，因为数据包是通过ip地址来传输的。当然如果已经解析过了，会走缓存，不需要解析ip地址。然后建立tcp连接，三次握手确认连接，数据包校验保证数据到达接收方，通过四次挥手断开连接。然后通过请求头+行+体发送http请求,网络响应后通过请求头+行+体把数据传给客户端。接下来是浏览器的解析和渲染工作，一构建dom树，将字节流转换为一种有意义并且方便操作的数据结构；二样式计算，将CSS 文本转化为styleSheets层叠样式表；三生成布局树，遍历生成的dom树节点，计算他们的坐标位置，将他们添加到布局树中；四五建立图层树，节点的图层会默认属于父节点的图层（合成层）。特定节点单独合成层，比如拥有层叠上下文的节点（根元素，opactity,filter,position,出现滚动条），层叠等级低的节点被提升为单独的图层之后，所有层叠等级比它高的节点都会成为一个单独的图层化；五绘制；
```


```
42.vue响应式数据原理
核心点：object.defineProperty()
默认初始化数据时，给data中的所有属性使用object.defineProperty做一个数据的读写监听，当页面使用对应属性时（get），会进行依赖收集（watcher)，如果属性发生变化会通知相关依赖进行更新。

```

```
43.数组是如何检测数组变化
函数劫持，重写数组的方法 
 <!--'push','pop','shift','unshift','splice','sort','reverse'-->
将data中的数组，进行函数劫持，改写原型链，改写方法中调用原生方法后 通知视图更新。如果数组中包含对象，会对数组中的对象再次进行遍历监控。
```
44.为何vue采用异步渲染
```
vue是组件级更新，为了性能考虑，在本轮数据更新后再异步更新视图
核心方法：nextTick
- 通知watcher进行更新操作 (dep.notify)
- 依次调用watcher的update 
- 根据wathcer id将watcher去重放到队列里
- 异步清空watcher队列

```


