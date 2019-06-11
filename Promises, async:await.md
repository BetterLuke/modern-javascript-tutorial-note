## Promise

`Promise` 类有 4 中静态方法：

1. `Promise.resolve(value)` —— 根据给定值返回 resolved promise，
2. `Promise.reject(error)` —— 根据给定错误返回 rejected promise，
3. `Promise.all(promises)` —— 等待所有的 promise 为 resolve 时返回存放它们结果的数组。如果任意给定的 promise 为 reject，那么它就会变成 `Promise.all` 的错误结果，所以所有的其他结果都会被忽略。
4. `Promise.race(promises)` —— 等待第一个 promise 被解决，其结果/错误即为结果。

## `then/catch` **的处理器总是异步的**

当 `.then/catch` 处理器应该执行时，它会首先进入内部队列。JavaScript 引擎从队列中提取处理器，并在当前代码完成时执行 `setTimeout(..., 0)`。

换句话说，`.then(handler)` 会被触发，会执行类似于 `setTimeout(handler, 0)` 的动作。

在下述示例中，promise 被立即 resolved，因此 `.then(alert)` 被立即触发：`alert` 会进入队列，在代码完成之后立即执行。

```javascript
// an immediately resolved promise
let promise = new Promise(resolve => resolve("done!"));

promise.then(alert); // 完成！（在当前代码完成之后）

alert("code finished"); // 这个 alert 会最先显示
```

`.then` 之后的代码总是在处理器之前被执行（即使实在预先解决 promise 的情况下）。通常这并不重要，只会在特定情况下才会重要。



## 练习

1. 容错机制 Promise.all

```javascript
let urls = [
  'https://api.github.com/users/iliakan',
  'https://api.github.com/users/remy',
  'https://api.github.com/users/jeresig'
];

Promise.all(urls.map(url => fetch(url)))
  // for each response show its status
  .then(responses => { // (*)
    for(let response of responses) {
      alert(`${response.url}: ${response.status}`);
    }
  ));
  
 // 可改写为
  
Promise.all(
  urls.map(url => fetch(url).catch(err => err))
)
```



## Async, await

1. 总是返回 promise。
2. 允许在其中使用 `await`。

在 promise 之前的 `await` 关键字，使 JavaScript 等待 promise 被处理，然后：

1. 如果有 error，就会产生异常，就像在那个地方调用了 `throw error` 一样。
2. 否则，就会返回值，我们可以给它分配一个值。



简化一个promise链的写法：

```javascript
function loadJson(url) {
  return fetch(url)
    .then(response => {
      if (response.status == 200) {
        return response.json();
      } else {
        throw new Error(response.status);
      }
    })
}

loadJson('no-such-user.json') // (3)
  .catch(alert); // Error: 404


// 可以改为

async function loadJson(url) { // (1)
  let response = await fetch(url); // (2)

  if (response.status == 200) {
    let json = await response.json(); // (3)
    return json;
  }

  throw new Error(response.status);
}

loadJson('no-such-user.json')
  .catch(alert); // Error: 404 (4)
```