## 递归

执行递归的堆栈：

一个函数运行的信息被存储在它的**执行上下文**里。

执行上下文是一个内部数据结构，它包含一个函数执行时的细节：当前工作流在哪里，当前的变量，`this` 的值（这里我们不使用它），以及其它一些内部细节。

每个函数调用都有与其相关联的执行上下文。

当一个函数有嵌套调用时，下面的事情会发生：

- 当前函数被暂停；
- 与它关联的执行上下文被一个叫做**执行上下文堆栈**的特殊数据结构保存；
- 执行嵌套调用；
- 嵌套调用结束后，之前的执行上下文从堆栈中恢复，外部函数从停止的地方继续执行。



### 练习

1. 计算阶乘

```javascript
const factorial = (n) => (n ? n * factorial(n - 1) : 1)

console.log( factorial(5) ); // 120
```

2. 输出一个单链表

```javascript
let list = {
  value: 1,
  next: {
    value: 2,
    next: {
      value: 3,
      next: {
        value: 4,
        next: null
      }
    }
  }
};



const printList = (list) => {
  console.log(list.value); // 输出当前元素
  list.next && printList(list.next)
}

printList(list);
```

# 函数对象，NFE

在 JavaScript 里，函数是对象。

- `name` – 函数的名字。不仅仅在函数定义指定时存在，而且在赋值或者对象属性中也会有。
- `length` – 函数定义时的入参个数。余参不参与计数。

任意多个参数括号求和的例子：

```javascript
sum(1)(2) == 3; // 1 + 2
sum(1)(2)(3) == 6; // 1 + 2 + 3
sum(5)(-1)(2) == 6
sum(6)(-1)(-2)(-3) == 0
sum(0)(1)(2)(3)(4)(5) == 15

// 实现：

function sum(a) {

  let currentSum = a;

  function f(b) {
    currentSum += b;
    return f;
  }

  f.toString = function() {
    return currentSum;
  };

  return f;
}

alert( sum(1)(2) ); // 3
alert( sum(5)(-1)(2) ); // 6
alert( sum(6)(-1)(-2)(-3) ); // 0
alert( sum(0)(1)(2)(3)(4)(5) ); // 15

/* sum 函数只工作一次，它返回了函数 f。

然后，接下来的每一次调用，f 都会把自己的参数加到求和 currentSum 上，然后返回自己。

在 f 的最后一行没有递归。*/
```

# 装饰和转发，call/apply

1. 延迟装饰器

```javascript
function delay(f, ms) {

  return function() {
    setTimeout(() => f.apply(this, arguments), ms);
  };

}
```

2.  去抖装饰器:

```javascript
function debounce(f, ms) {

  let isCooldown = false;

  return function() {
    if (isCooldown) return;

    f.apply(this, arguments);

    isCooldown = true;

    setTimeout(() => isCooldown = false, ms);
  };

}
```

3. 节流装饰器

```javascript
function throttle(func, ms) {

  let isThrottled = false,
    savedArgs,
    savedThis;

  function wrapper() {

    if (isThrottled) { // (2)
      savedArgs = arguments;
      savedThis = this;
      return;
    }

    func.apply(this, arguments); // (1)

    isThrottled = true;

    setTimeout(function() {
      isThrottled = false; // (3)
      if (savedArgs) {
        wrapper.apply(savedThis, savedArgs);
        savedArgs = savedThis = null;
      }
    }, ms);
  }

  return wrapper;
}

/* 在第一次调用期间，wrapper 只运行 func 并设置冷却状态 （isThrottled = true）。
在这种状态下，所有调用都记忆在 savedArgs/savedThis 中。请注意，上下文和参数都同样重要，应该记住。我们需要他们同时重现这个调用。
…然后在 ms 毫秒过后，setTimeout 触发。冷却状态被删除 （isThrottled = false）。如果我们忽略了调用，则使用最后记忆的参数和上下文执行 wrapper
第3步不是 func，而是 wrapper，因为我们不仅需要执行 func，而是再次进入冷却状态并设置超时以重置它。*/
```

4. 间谍装饰器

```javascript
/*
装饰器 spy(func)，它返回一个包装器，它在 calls 属性中保存所有函数调用。

每个调用都保存为一个参数数组。

*/
function spy(func) {
  
    function wrapper(...args) {
      wrapper.calls.push(args);
      return func.apply(this, arguments);
    }
  
    wrapper.calls = [];
  
    return wrapper;
}


function work(a, b) {
  alert( a + b ); // work 是一种任意的函数或方法
}

work = spy(work);

work(1, 2); // 3
work(4, 5); // 9

for (let args of work.calls) {
  alert( 'call:' + args.join() ); // "call:1,2", "call:4,5"
}
```

# 柯里化和偏函数

待完成