_
RxJS通过使用可观察序列，为组合了**_异步_**和**_基于事件_**的程序提供了一个库。它提供了一个核心类型(_可观察对象_)、卫星类型(_观察者_、_调度者_、_主题_)和能把异步事件当做集合来处理的、通过数组方法(map,filter,reduce,every等等)产生的_操作符_。
>把RxJS当做一个针对事件的Lodash(译者注：一个JS库)。

ReactiveX将观察者模式与迭代器模式和使用集合的函数式编程组合在一起，来满足这种管理事件序列的理想的方式

RxJS中解决异步事件的基本概念如下：
* 可观察对象：表示一个可调用的未来值或者事件的集合。
* 观察者：是一个知道如何监听被可观察对象传递的值的回调函数集合
* 订阅： 表示一个可观察对象的执行，主要用于取消执行。
* 操作符： 一些单纯的函数，支持使用map,filter,contact,flatmap等操作符以函数式编程的方式处理处理集合。
* Subject(主题?)：等同于一个事件驱动器，是将一个值或者事件广播到多个观察者的唯一途径。
* Schedulers(调度者?)： 用来控制并发，当计算发生的时候允许我们协调，比如setTimeout,requestAnimationFrame。


# 第一个例子
通常你这样注册事件监听：

```
var button = document.querySelector('button');
button.addEventListener('click', () => console.log('Clicked!'));
```
使用RxJS创建一个可观察对象：
```
var button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
.subscribe(() => console.log('Clicked!'));
```

#Purity纯粹的
RxJX能够使用纯函数的方式生产值的能力使得它强大无比。这意味着你的代码不再那么频繁的出现错误提示。

通常情况下你会创造一个非纯正的函数，然后你的代码的其他部分可能搞乱你的程序状态。
```
var count = 0;
var button = document.querySelector('button');
button.addEventListener('click', () => console.log(`Clicked ${++count} times`));
```
使用RxJS来隔离你的状态

```
var button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
.scan(count => count + 1, 0)
.subscribe(count => console.log(`Clicked ${count} times`));

```
scan操作符和数组中reduce方法的类似， 它需要一个传递给回调函数的参数值。 回调函数的返回值将成为下一次回调函数运行时要传递的下一个参数值。
#Flow 流

RxJS有着众多的操作符，可以帮助您控制事件如何流入可观察对象observables。


这是你每秒最多只能点击一次的实现，使用单纯的JavaScript：


```
var count = 0;
var rate = 1000;
var lastClick = Date.now() - rate;
var button = document.querySelector('button');
button.addEventListener('click', () => {
if (Date.now() - lastClick >= rate) {
console.log(`Clicked ${++count} times`);
lastClick = Date.now();
}
});

```
使用RxJS


```
var button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
.throttleTime(1000)
.scan(count => count + 1, 0)
.subscribe(count => console.log(`Clicked ${count} times`));
```

其他的流操作符是e filter, delay, debounceTime, take, takeUntil, distinct, distinctUntilChanged 等等。
#Values值
你可以通过可观察对象来转化值

下面的程序可以在每次点击鼠标时获取X坐标位置

单纯的JS实现



```

var count = 0;
var rate = 1000;
var lastClick = Date.now() - rate;
var button = document.querySelector('button');
button.addEventListener('click', (event) => {
if (Date.now() - lastClick >= rate) {
console.log(++count + event.clientX)
lastClick = Date.now();
}
});
```

RxJS实现

```
var button = document.querySelector('button');
Rx.Observable.fromEvent(button, 'click')
.throttleTime(1000)
.map(event => event.clientX)
.scan((count, clientX) => count + clientX, 0)
.subscribe(count => console.log(count));
```
其他的值生产者还有 pluck, pairwise, sample 等等.












