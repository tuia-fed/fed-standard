# 常用功能的 ES6 写法

## 解构

### 对象解构

**使用三次对象属性访问符`.`的表达式，同时出现两次以上，就必须使用数组结构来简化属性的访问链，而不是放任重复的访问链不管**

**不建议使用多层嵌套的解构，这会使得代码的理解成本变高**

```js
// 坏的例子
fetchSomeData(this.state.id, this.state.name);

// 使用解构后的例子
const { id, name } = this.state;
fetchSomeData(id, name);

// 对于解构出的变量名与之前生命的变量冲突的，可以为解构出的变量指定别名
const { id: anotherId, name: anotherName } = this.state;
fetchSomeData(anotherId, anotherName);

// 当然也可以为解构出的变量指定默认值，提供一个fallback的方案。
const { id = '', name = '' } = this.state;
fetchSomeData(id, name);
```

### 数组结构

数组解构不如对象解构使用得多，通常业务代码里和 lodash 一起使用或者与将来的`React Hooks`结合使用。

```jsx
// 在这个例子中，使用了lodash的partition将用户列表分割为2部分，在左侧直接接数组的解构，得到每个分组。
// 类似的函数还有lodash的groupBy等。
// 文档见 @see https://lodash.com/docs/4.17.10#partition
const [activeUser, normalUser] = _.partition(userList, user => user.isActive);
```

### 参数解构

参数解构一般用于传递进来的参数是个对象的情况，使用参数解构能使函数内部能直接拿到对象中的某些字段，减少了中间变量。

#### React 中函数式组件的用法

```jsx
function ExportPopover({ onSummaryExport, onTimePhasedExport }) {
  return (
    <>
      <Button onClick={onSummaryExport}>分时段导出</Button>
      <Button onClick={onTimePhasedExport}>汇总导出</Button>
    </>
  );
}
```

#### Vuex 中 action 的第一个参数为`context`，可以从中抽取`commit`, `dispatch`, `state`, `rootState`四个字段

```js
const actions = {
  submitForm({ commit }, formData) {
    // 省略ajax请求
  }
};
```

## const 与 let

除非你知晓你定义的变量在之后会被重新赋值，否则请使用`const`。这会使代码更好维护。因为你不能保证之后维护代码的其它人覆盖你的变量。而且`const`在性能上略好于`let`。因为垃圾回收被简化了。

当你需要重新赋值变量时，用`let`，如在`for`循环中。

```js
const requestParams = {};
requestParams.foo = 'bar'; // 可以操作的
requestParams = {}; // 报错，因为不能改变const声明变量的引用。
```

## 字符串

### 模板字符串

大于 2 个字符串的拼接，或者需要换行的，请使用模板字符串。使用加号拼接的多个字符串会使得代码可读性降低不少。

```jsx
// 反例
const htmlText = '<div>' + name + '：' + carer + '<div/>'; // 如果是更多层的html字符串拼接，代码会更加难读。

// 正确的写法
const htmlText = `<div>${name}：${carer}</div>`; // cool,很容易明白是把name和carer作为插值放进字符串里了
```

### includes, startWith, endWith

当我们需要判断字符串中是否存在某个子串时，使用 includes 会更加直观

```js
// 以前的写法，现在有更直观易懂的为什么不用呢？
if (starBucksCupSize.indexOf('large') !== -1) {
  // 做一些奇♂怪的事情
}

// 现在这样写就可以了，简洁明了
if (starBucksCupSize.include('large')) {
  // 做一些奇♂怪的事情
}
```

`startWith`和`endWith`同理。此处就不举例了

### `padStart`和`padEnd`

这两个方法是用来填充字符串的首部或者尾部的。

```js
// 此处是一个填充2位的小时的例子
// 过去我们得这么写，如果是更多位数的填充需要写循环，代码非常不直观。
const hourString = hour >= 10 ? `0${hour}` : hour.toString();

// 现在用padStart可以非常轻松地在头部填充0
const hourString = hour.toString().padStart(2, '0'); // 不足2位时，填充0到2位
```

`padEnd`在尾部填充，同理。

PS: lodash 中也有`padStart`和`padEnd`方法，功能相同。
PPS: 请在有 babel 的情况下使用这个方法，因为比较老的浏览器不支持这个方法。

## 函数的`rest`参数

rest 参数语法用于取代`arguments`关键字，这个语法在不定参数的方法中特别有用。

```js
function foo(param1, ...restParams) {
  console.log(param1, restParams);
}

foo(1, 2, 3, 3); // 调用之后restParams会变成[2, 3, 3]
```

## 展开运算符

### 复制、合并数组、对象。

以后请不要使用`Object.assign`和`Array.prototype.concat`。建议少用`Array.prototype.push`

```js
const list1 = [1, 2, 3];
const list2 = [4, 5, 6];
const list1Copy = [...list1];

const list3 = [...list1, ...list2, 7, 8, 9];

const commonParams = { name, age };
const specificParams = { job, gender };

const finalParams = { ...commonParams, ...specificParams };
const paramsCopy = { ...finalParams };
```

### 转换 Map, Set 对象到数组

```js
const activeNameSet = new Set();
activeName.add('Iron');
activeName.add('Finmy');

const activeNameList = [...activeNameSet]; // ['Iron', 'Finmy']
```

## `Array.from`

使用这个方法从 DOMList 等类数组对象转换为真正的数组，便于使用数组的一些方法。

```js
const elements = Array.from(document.querySelectAll('.finmy'));
```

### 函数默认值

在业务代码中，一般用于给函数式组件的参数指定默认参数。
极少情况下，给公共方法的参数指定默认值

```js
const Component = ({
  name = 'Iron'
 }) => return <div>{name}</div>
```

### 箭头函数

见 [http://es6.ruanyifeng.com/?search=%E8%A7%A3%E6%9E%84&x=0&y=0#docs/function#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0](http://es6.ruanyifeng.com/?search=%E8%A7%A3%E6%9E%84&x=0&y=0#docs/function#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0)

### `Object.keys`和`Object.values`

提取对象中的键和键值。

```js
// 提取坐标的x轴上的点，在火眼的业务中很常见
const xAxisData = Object.keys(response.data);
```

## `Promise`

见[此处](http://es6.ruanyifeng.com/?search=%E8%A7%A3%E6%9E%84&x=0&y=0#docs/promise)，建议每个同学从头到尾看一遍。不要写出 Promise 里套 Promise 的垃圾代码。

理解 Promise 是使用更加高级的`async/awit`的基础

下面展示业务代码里 Promise 的误用。

```js
// Promise中套Promise，造成回调地狱

// 已有Promise对象的情况下去new 一个新的Promise然后resolve旧的Promise的结果
```

## Async/await

见本文档的[异步处理](./asynchronous.md)部分

## 参考资料

[ECMAScript 6 入门](http://es6.ruanyifeng.com/?search=%E8%A7%A3%E6%9E%84&x=0&y=0)
