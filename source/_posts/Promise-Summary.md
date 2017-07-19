title: 一起来学Promise
date: 2017-04-28 15:50:54
tags: [javascript,Node.js,前端]
categories: JavaScript
---
>注意，本文主要针对ES6标准实现的Promise语法进行阐述，实例代码也都使用ES6语法，快速入门ES6请参见[ECMAScript 6 扫盲](http://www.barretlee.com/blog/2016/07/09/a-kickstarter-guide-to-writing-es6/)。

## 一分钟快速入门
被回调地狱整怕了？快试Promise吧！。Promise的核心思想其实很简单，就是将异步操作结果处理交给Promise对象的方法注册，然后等到异步操作完了再去取用这些处理操作。至于取用哪个处理操作，就得看Promise对象状态了。Promise对象一共有三种状态：Pending（初始状态）、Fulfilled（异步操作成功）、Rejected（异步操作失败）。而三者间的转换只有两种情况：Pending—>Fulfilled、Pending—>Rejected；详见下图：

![prmoise-sates](http://ww1.sinaimg.cn/mw690/85ad0d9cgy1ff9jzqcxxxj208u04u74i.jpg)

了解了状态及其转换后，我们就可以来使用Promise对象了：

```javascript
let promise = new Promise((resolve, reject)=> {
    // 异步操作
    // 异步操作成功时调用
    resolve(value)
    // 异步操作失败时调用
    reject(error)
    });
```

上述代码中传给Promise构造函数的两个函数resolve, reject，分别用于触发Promise对象的Fullfilled和Rejected状态。当处于Fullfilled状态时Promise会调用`then`方法，而处于Rejected状态时则会调用`catch`方法，这两个方法都会返回Promise对象，所以我们可以采用链式写法：

```javascript
promise.then((value)=> {...})
    .catch((error)=> {...});
```

上面的方法链中，`then`方法里注册了Fullfilled状态的处理函数、`catch`方法则注册了Rejected状态的处理函数。这种简单明了的写法把异步操作的结果处理函数分离了出来，如果这些处理本身又是异步操作，那我们自然也就把层层异步回调也从回调地狱中剥离了，代码瞬间清爽有木有！

## 深入Promise调用链
前面我们只是将一层处理操作分离到`then`方法中（其中`catch`方法只是`then`方法的一个语法糖，后面会再作讲解）；但在实际应用中多个异步操作往往会以串行或并行的方式连续出现，比如下面这个预定房间的流程：

![order-room](http://ww1.sinaimg.cn/mw690/85ad0d9cgy1ff9k0gycctj207t0d60tc.jpg)

其中数据校验、向API发送请求、往数据库插入数据都是异步操作，一种用回调的写法大概长这样：

```javascript
validate(data, (err)=> {
    if (err) return errorHandler(err);
    request(apiUrl, (err, apiResponse)=> {
            if (err) return errorHandler(err);
            if (apiResponse.isSuccessful) insertToDB(data, (err)=> {
                    if (err) return errorHandler(err);
                    successHandler();
                });
            else errorHandler(new Error('API error'));
        });
    });
```

根据前面我们了解的Promise用法，我们已经能将`validate`这个异步操作写成Promise形式了：

```javascript
let promiseValidate = new Promise((resolve, reject)=> {
    validate(data, (err)=> {
        if (err) return reject(err);
        resolve();
        });
    });

promiseValidate(data)
    .then(()=> {
        request(apiUrl, (err, apiResponse)=> {
                if (err) return errorHandler(err);
                if (apiResponse.isSuccessful) insertToDB(data, (err)=> {
                        if (err) return errorHandler(err);
                        successHandler();
                    });
                else errorHandler(new Error('API error'));
            });
        })
    .catch((err)=> errorHandler(err));
```

但要改就改到底，上面这种Promise和回调写法混合得就不伦不类，除了仍存在回调嵌套的问题，多次出现的错误判断和处理也有点违反DRY。所以接下来我们会深入研究下Promise调用链的行为，重点探讨`then`方法里注册的回调对调用链上数据传递和Promise对象状态变化的影响，以及如何在调用链上对错误进行统一的处理。

### Promise.resolve和Promise.reject
我们先来看下一种“快速”生成Promise对象的方法：直接调用`Promise.resolve(value)`或`Promise.reject(err)`。这种方法和`new`一个Promise对象的区别在于，Promise对象在生成的时候状态就已经确定，要么是Fullfilled（使用`Promise.resolve()`）、要么是Rejected（使用`Promise.reject()`），不会和`new`实例化一样等要异步操作完了再发生变化。

此外，如果传给`Promise.resolve`方法的是一个具有`then`方法的对象（即所谓的`Thenable`对象），比如jQuery的`$.ajax()`，那么返回的Promise对象，后续调用的`then`就是原对象`then`方法的同一形式（参见下面的代码）。简单来讲，就是`Promise.resolve`会将`Thenable`对象转为ES6的Promise对象，这一特性常被用来将Promise的不同实现转换为ES6实现。

```javascript
$.ajax('https://httpbin.org/ip').then((value)=> {
    /* 输出223.65.191.59 */
    console.log(value.origin)
    });

Promise.resolve($.ajax('https://httpbin.org/ip'))
    .then((value)=> {
        /* 输出223.65.191.59 */
        console.log(value.origin)
        });
```

### 详解Promise.prototype.then
有了前面知识的铺垫，我们终于可以来详细讲一下Promise对象的`then`方法了。

#### 参数
如前面所提到的，`catch`方法只是`then`方法的一个语法糖，
原因就在于`then`方法的参数为实际上是“两个”回调函数，分别用于处理调用它的Promise对象的Fullfilled和Rejected状态，而`catch`方法就等价于`then(undefined, Rejected状态处理函数)`。

关于这两个回调函数，首先要注意它们是异步调用的：
```javascript
var v = 1;
/* 输出result: 2 */
Promise.resolve().then(()=> {console.log('result: ' + v)});
/* 输出result: 2 */
Promise.reject().then(undefined, ()=> {console.log('result: ' + v)});
v++;
```

而两个回调函数的参数，则是通过调用`then`方法的Promise对象指定的：
    
* `new Promise()`产生的Promise对象，会分别用内部`resolve()`、`reject()`函数的参数
* `Promise.resolve()`或`Promise.reject()`产生的Promise对象，则分别用`Promise.resolve()`、`Promise.reject()`的参数

而两个回调函数的返回值，会用`Promise.resolve(第一个回调返回值)`或`Promise.reject(第二个回调返回值)`的形式作包装，用来“替换”then方法返回的Promise对象。结合上面提到的`then`回调函数参数指定方式，回调返回值会这样影响下一个`then`的回调函数：

* 返回的是普通数据，会传给下一级调用的`then`方法作为回调函数的参数
* 返回的是Promise对象或Thenable对象，会被拿来“替换”then方法返回的Promise对象，具体`then`的回调函数怎么调用和传参就得看其内部实现了

#### 返回值
一个新的Promise对象，状态看执行哪个回调函数决定。注意这是一个新对象，不是简单把调用`then`的Promise对象拿来改装后返回：
```javascript
var aPromise = new Promise((resolve)=> resolve(100));
var thenPromise = aPromise.then((value)=> console.log(value));
var catchPromise = thenPromise.catch((error)=> console.error(error));
/* true */
console.log(aPromise !== thenPromise);
/* true */
console.log(thenPromise !== catchPromise);
```

#### 链式调用
知道了`then`方法的具体细节后，我们就能明白Promise调用链上：

* 传递数据的方法：利用上面提到的`then`回调的参数传递形式——不论是在Promise对象产生过程中直接传递、还是在`then`回调返回值中间接传递——就能实现将每一级异步操作的结果传递给后续`then`中注册的处理函数处理。
* Promise对象状态传递和改变的方法：利用`then`回调的返回值，可以控制某个操作后`then`方法返回的Promise对象及其状态。

现在我们把所有异步操作改为Promise语法，再利用在Promise调用链传递数据和控制状态的方法，就能把本节开始提到的预定房间操作中的回调嵌套都展开来了：

```javascript
let promiseValidate = new Promise((resolve, reject)=> {
    validate(data, (err)=> {
        if (err) return reject(err);
        resolve();
        });
    });

let promiseRequest = new Promise((resolve, reject)=> {
    request(data, (err, apiResponse)=> {
        if (err) return reject(err);
        // 在Promise对象产生过程中直接传递异步操作的结果
        resolve(apiResponse);
        });
    }
);

let promiseInsertToDB = new Promise((resolve, reject)=> {
    insertToDB(data, (err)=> {
        if (err) return reject(err);
        resolve();
        });
    }
);

promiseValidate(data)
    .then(()=> promiseRequest(apiUrl))
    .then((apiResponse)=> {
        // 控制then回调的返回值，来改变then方法返回的新Promise对象的状态
        if (apiResponse.isSuccessful) return insertToDB(data);
        else errorHandler(new Error('API error'));
        })
    .then(()=> successHandler())
    .catch((err)=> return errorHandler(err));
```

上面的代码不仅将嵌套的代码展开，让我们挣脱了“回调地狱”；而且可以对异步操作的错误直接利用统一的Promise错误处理方法，避免写一堆重复的代码。如果要进一步DRY，可以抽象出一个将典型的Node.js回调接口封装为Promise接口的函数：

```javascript
/* 处理形如 receiver.fn(...args, (err, res)=> {}) 的接口 */
let promisify = (fn, receiver) => {
  return (...args) => { // 返回重新封装的Promise接口
    return new Promise((resolve, reject) => {
      fn.apply(receiver, [...args, (err, res) => { // 重新绑定this
        return err ? reject(err) : resolve(res);
      }]);
    });
  };
};

/* 用例 */
let promiseValidate = promisify(validate, global);
let promiseRequest = promisify(request, global);
let promiseInsertToDB = promisify(insertToDB, global);
```

>注意，由于resolve和reject方法只能接收一个参数，所上面这个函数处理的回调里只能有err和一个数据参数。

### Promise调用链上的错误处理
在Promise调用链上的处理错误的思路，就是去触发Promise对象的Rejected状态，利用状态的传递特性实现对错误的捕获，再在`catch`或`then`回调里处理这些错误。下面我们就来进行相关的探讨：

#### 错误的捕获
首先我们有必要详细了解下Promise对象的Rejected状态的产生和传递过程。

Rejected状态的产生有两种情况：

* 调用了reject函数：Promise对象实例化的回调调用了`reject()`，或者直接调用了`Promise.reject()`
* 通过`throw`抛出错误

而只要产生了Rejected状态，就会在调用链上持续传递，直到遇见Rejected状态的处理回调（`catch`的回调或`then`的第二个回调）。再结合之前提到的Promise调用链上的数据传递方法，错误就能在调用链上作为参数被相应的回调“捕获”了。这个过程可以参见下图：

![promise-reject-flow](http://ww1.sinaimg.cn/mw690/85ad0d9cgy1ff9k158856j20az0eygmb.jpg)

这里要注意，通过`throw`抛出错时，如果错误是在`setTimeout`等的回调中抛出，是不会让Promise对象产生Rejected状态的，这也以为着Promise调用链上捕获不了这个错误。举个例子，下面这段代码就不会有任何输出：

```javascript
Promise.resolve()
    .then(()=> setTimeout(100, ()=> {throw new Error('hi')}))
    .catch((err)=> console.log(err));
```

究其原因，是因为`setTimeout`的异步操作和Promise的异步操作不属于同一种任务队列，`setTimeout`回调里的错误会直接抛到全局变成`Uncaught Error`，而不会作用到Promise对象及其调用链上。这就也意味着，想要保证在调用链上产生的错误能被捕获，就必须始终使用调用reject函数的方式来产生和传递错误。

#### 错误处理
错误处理可以在`catch`的回调或`then`的第二个回调里进行。虽然前面提到`catch`方法等价于`then(undefined, Rejected状态处理函数)`，但推荐始终使用`catch`来处理错误，原因有两个：

* 代码的可读性
* 对于`then(Fullfilled状处理函数, Rejected状态的处理函数)`这种写法，如果Fullfilled状态的处理函数里出错了，那错误只会继续向下传递，同级的Rejected状态处理函数没办法捕获该错误

#### 优化房间预订例子的错误处理
了解完了Promise调用链上的错误处理，我们再来回顾一开始提到的房间预订例子。之前我们的代码里只是对异步操作中的可能出现错误进行了统一的处理，但是其中的`API error`等别的执行错误并未使用在Promise调用链上捕获和处理错误的方式。为了进一步DRY，我们可以通过调用`Promise.reject`，强制将返回的Promise对象变为Rejected状态，共用统一的Promise错误处理：

```javascript
(apiResponse)=> {
        if (apiResponse.isSuccessful) return insertToDB(data);
        // 返回的Promise对象为Rejected状态，共用统一的Promise错误处理
        else return Promise.reject(new Error('API error'));
        }
```

### Promise.all和Promise.race
前面研究的多个异步操作间往往具有前后依赖关系，或者说它们是“串行”进行的，只有前一个完成了才能进行后一个。但有时我们处理的异步操作间可能并不具有依赖关系，比如处理多张图片，这时再使用上面的调用链写法，就只能等处理完一张图片、对应的Promise对象状态变化了，才能再去处理下一张，就显得很低效了。所以，我们需要一种能在调用链中同时处理多个Promise对象的方法，`Promise.all`和`Promise.race`就是这样应运而生的。

这两个方法的相同点是会接受一个Promise对象组成的数组作为参数，包装返回成一个新的Promise实例。而它们的区别就在于返回的这个Promise实例状态如何变化：

* `Promise.all`：
    *  所有传入的Promise对象状态都变成Fullfilled，最终状态才会变成Fullfilled；此时便会调用`Promise.resolve(各Promise对象resolve参数组成的数组)`，生成新状态的Promise对象返回
    *  各个Promise对象若有一个被reject，最终状态就变成Rejected；此时便会调用Promise.reject(第一个被reject的实例的reject参数)，生成新状态的Promise对象返回
* `Promise.race`：只要传入的各个Promise对象中有一个率先改变状态（Fullfilled或Rejected），返回的Promise对象状态就会改变为相应状态

有了这两个方法，我们就能在Promise调用链上“并行”等待某些异步操作了，还是用前面提到的客房例子来举例，如果我们在预定房间时需要请求的API不止一个，调用链可以这么写：

```javascript
promiseValidate(data)
    /* 请求多个API */
    .then(()=> Promise.all([promiseRequest(apiUrl1), promiseRequest(apiUrl2)]))
    .then((apiResponse)=> {
        /* 传给下个then回调的是一个resolve参数组成的数组 */
        if (apiResponse[0].isSuccessful && apiResponse[1].isSuccessful) return insertToDB(data);
        else return Promise.reject(new Error('API error'));
        })
    .then(()=> successHandler())
    .catch((err)=> return errorHandler(err));
```

## Promise的应用
Promise是一种异步调用的写法，自然是用来写出清晰的异步代码、让我们摆脱回调写法带来的种种弊端，本文一直使用的预定房间例子就是一个佐证。不过考虑实际的应用场景，还是有一些需要注意的地方：

### 前端异步处理
前端的浏览器兼容性是阻碍新技术运用的一大难题，虽然使目前浏览器对于ES6的支持越来越完善了，但除非你不考虑IE（[兼容性表](http://caniuse.com/#feat=promises)），否则在前端代码里直接使用的原生的Promise实现并不太现实。对于这种情况，我们可以用一些[Polyfill或拓展类库](http://liubin.org/promises-book/#promise-library)来让我们能写Promise代码。

### Node的异步处理：
Node.js环境下对ES6的Promise支持，在零点几版开始就有了，所以我们在编写服务器代码、或者写一些跑在Node上的模块时可以直接上Promise语法。不过要注意的是，Node上的大部分模块开放的API，还是默认使用回调风格，这是为了方便用户在不了解Promise语法时快速上手；所以一般自己写的模块API也会遵循这个惯例，至于模块内部实现那就随你的意愿使用了。

还有一个要值得注意的是，最近Node实现了更优雅的异步写法--[async函数](https://medium.com/@tsmith18256/async-await-in-node-js-v7-a9386b44e115)，不过新的写法是基于Promise实现的，所以虽然async函数的出现让Promise有种高不成低不就的感觉，但了解Promise的用法还是很有必要的，希望本文能帮你做到这点:D。

## 参考
[JavaScript Promise迷你书](http://liubin.org/promises-book/)
[Promise 的链式调用与中止](https://segmentfault.com/a/1190000007598894)
[如何把 Callback 接口包装成 Promise 接口](https://75team.com/post/how-to-convert-callback-to-promise.html)
