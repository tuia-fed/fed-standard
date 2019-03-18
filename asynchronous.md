# Ajax 请求处理

Ajax 的请求异步默认使用`async/await`写法。

`async/await`语法比起`Promise.prototype.then`，能用同步代码的书写方式，来处理异步的流程。这能显著地提高代码的可读性。

目前在各条业务线的系统中都有它的 polyfill。所以也没有使用的成本。甚至连旧版火眼的代码中都可以使用它。所以对于业务中的异步请求，`async/await`是最佳的选择。

`await`关键字后只要跟着 promise，就能在 promise fullfilled 后，返回 promise 包含的值。继续执行后续的代码

**注意，async 函数会返回一个 Promise 对象。其中通过`return`返回的值，会成为`then`方法接收回调函数的参数**

**async 函数内部抛出错误，会将 Promise 设置为`reject`状态。await 之后的 Promise 如果被 reject,它将会抛出一个错误，被 `catch` 捕获**

**综上所述，请务必保证 await 后的 Promise 若抛出异常，会被正确地`catch`后处理**
我们来看一个简单的例子：

```js
function timeout(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello world', 50);
```

显然，在前端，异步使用最多的场景便是 Ajax 请求。目前通过脚手架搭建的项目中，能通过 common.fetch 来获取那个操作 Ajax 的 Promise.

## 单个请求的 Ajax

```js
async function fetchAuthList() {
  try {
    // 请善用解构
    const { authList } = await common.fetch('/auth/getAuthList');
    this.authList = authList; // 将数据设置到Observable中。请根据业务场景灵活使用数据
  } catch (e) {
    message.error(e.message); // 使用Antd的`message.error`来展示错误信息，请结合业务场景灵活变换
  }
}
```

## 多个请求并发

```js
try {
  // 请自行处理res1和res2
  const [res1, res2] = await Promise.all([
    common.fetch('path1'),
    common.fetch('path2')
  ]);
} catch (e) {
  // 省略错误的实现。请注意，Promise.all中任何一个请求reject都会导致错误被catch，另一个Promise会被cancel。
}
```

## 多个请求顺序发送

```js
try {
  // 拿第一个请求的数据
  const { data: firstData } = await common.fetch('path1');

  // 拿第二个请求的数据，使用第一个请求到的数据中的一部分
  const { data: secondData } = await common.fetch('path2', {
    foo: firstData.someProperty
  });
} catch (e) {
  // 此处省略
}
```

## 参考资料

[ECMAScript 6 入门中 async/await 的部分](http://es6.ruanyifeng.com/#docs/async)
