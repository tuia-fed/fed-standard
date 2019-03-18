# 命名规范

> 计算机科学只存在两个难题：缓存失效和命名。
> -- Phil Karlton

## 如何避免单词拼错

很简单，在 Vscode 中安装 [Spell check](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) 插件。错误的单词拼写下方会有绿色的线提示。并且可以用`command` + `,`进行快速更正

## 缩写

### **永远**不要为了少打字而去使用缩写，除非这个缩写人尽皆知。

> 非人尽皆知的缩写会带来沟通成本

```js
// bad analy这个缩写在火眼中经常出现，但没有缩写的必要
analyData = 'someThing'; // .....

// good 现在编译器能通过单词的一部分来自动提示。长的命名并不会带来任何的坏处
// 而且所有的变量都会在代码压缩后变成无意义的字母
analyzeData = 'Nerf this';
```

## 文件名

文件夹的命名规则与文件夹的内容有关。

### `export default`出的公共方法

Camel Case，小驼峰。保证文件与默认导出的函数名一致

### React 组件

Pascal Case 大驼峰。保证与文件的默认导出名一致。因为 React 的组件名必须大写。所以文件名用大驼峰。

并且，使用 jsx 而不是 js 作为文件名。这样能让人一看就知道是组件。

### Vue 组件

Pascal Case 大驼峰。Vue 的组件最后默认导出的是一个逻辑上的单例配置项，按照惯例，单例对象是需要大写的。因此使用大驼峰

### 样式文件

kebab case，短横线命名法。

### 定义常量和枚举的文件

统一用`constant.js`

### 文件夹

#### 如果文件夹有`index.js`和`index.jsx`

请保持命名与这两个文件默认导出的类名、方法名、函数名、常量名一致。

#### 其它情况

使用短横线命名法。

## JavaScript

### 避免用一个字母命名，让你的命名可描述。（来自 Airbnb 的代码规范）

> 除了 map, reduce, filter 等高阶函数。
> 命名长度在现代的 JS 引擎下对性能的影响几乎可以忽略不计

```javascript
// bad
function q() {
  // ...
}

// good
function query() {
  // ...
}
```

### 用小驼峰式命名你的对象、函数、实例。（来自 Airbnb 的代码规范）

    ```javascript
    // bad
    const OBJEcttsssss = {};
    const this_is_my_object = {};
    function c() {}

    // good
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
    ```

### 用大驼峰式命名类。（来自 Airbnb 的代码规范）

```javascript
// bad
function user(options) {
  this.name = options.name;
}

const bad = new user({
  name: 'nope'
});

// good
class User {
  constructor(options) {
    this.name = options.name;
  }
}

const good = new User({
  name: 'yup'
});
```

### 不要用前置或后置下划线。（来自 Airbnb 的代码规范）

> Why? JavaScript 没有私有属性或私有方法的概念。尽管前置下划线通常的概念上意味着“private”，事实上，这些属性是完全公有的，因此这部分也是你的 API 的内容。这一概念可能会导致开发者误以为更改这个不会导致崩溃或者不需要测试。 如果你想要什么东西变成“private”，那就不要让它在这里出现。
> 总结一句就是这么做没有任何效果，那为什么要做呢。在火眼中甚至能看到公共方法都这么命名的。

```javascript
// bad
this.__firstName__ = 'Panda';
this.firstName_ = 'Panda';
this._firstName = 'Panda';

// good
this.firstName = 'Panda';
```

### 不要保存引用`this`， 用箭头函数或[函数绑定——Function#bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)（Airbnb）

> 保存 this 在 es5 时代的代码中很常见，但现在有了 ES6，请使用更短更易读的实现。

```javascript
// bad
function foo() {
  const self = this;
  return function() {
    console.log(self);
  };
}

// bad
function foo() {
  const that = this;
  return function() {
    console.log(that);
  };
}

// good
function foo() {
  return () => {
    console.log(this);
  };
}
```

### export default 导出模块 A，则这个文件名也叫 A.\*， import 时候的参数也叫 A。 大小写完全一致。（这也是上面的文件命名的原因（Airbnb））

```javascript
// file 1 contents
class CheckBox {
// ...
}
export default CheckBox;

// file 2 contents
export default function fortyTwo() { return 42; }

// file 3 contents
export default function insideDirectory() {}

// in some other file
// bad
import CheckBox from './checkBox'; // PascalCase import/export, camelCase filename
import FortyTwo from './FortyTwo'; // PascalCase import/filename, camelCase export
import InsideDirectory from './InsideDirectory'; // PascalCase import/filename, camelCase export

// bad
import CheckBox from './check_box'; // PascalCase import/export, snake_case filename
import forty_two from './forty_two'; // snake_case import/filename, camelCase export
import inside_directory from './inside_directory'; // snake_case import, camelCase export
import index from './inside_directory/index'; // requiring the index file explicitly
import insideDirectory from './insideDirectory/index'; // requiring the index file explicitly

// good
import CheckBox from './CheckBox'; // PascalCase export/import/filename
import fortyTwo from './fortyTwo'; // camelCase export/import/filename
import insideDirectory from './insideDirectory'; // camelCase export/import/directory name/implicit "index"
// ^ supports both insideDirectory.js and insideDirectory/index.js
```

### 当你 export-default 一个函数时，函数名用小驼峰，文件名需要和函数名一致（Airbnb）。

```javascript
function makeStyleGuide() {
  // ...
}

export default makeStyleGuide;
```

### 当你 export 一个结构体/类/单例/函数库/对象 时用大驼峰（Airbnb）。

**特别地，vue 文件视为一个单例或类**

```javascript
const AirbnbStyleGuide = {
  es6: {}
};

export default AirbnbStyleGuide;
```

### 简称和缩写应该全部大写（Airbnb）。

> Why? 名字都是给人读的，不是为了适应电脑的算法的。

```javascript
// bad
import SmsContainer from './containers/SmsContainer';

// bad
const HttpRequests = [
  // ...
];

// good
import SMSContainer from './containers/SMSContainer';

// good
const HTTPRequests = [
  // ...
];
```

### 你可以用全大写字母设置静态变量，他需要满足三个条件（Airbnb）。

    1. 导出变量
    2. 是 `const` 定义的， 保证不能被改变
    3. 这个变量是可信的，他的子属性都是不能被改变的

    > Why? 这是一个附加工具，帮助开发者去辨识一个变量是不是不可变的。
    - 对于所有的 `const` 变量呢？ —— 这个是不必要的。大写变量不应该在同一个文件里定义并使用， 它只能用来作为导出变量。 赞同！
    - 那导出的对象呢？ —— 大写变量处在export的最高级(e.g. `EXPORTED_OBJECT.key`) 并且他包含的所有子属性都是不可变的。

```javascript
// bad
const PRIVATE_VARIABLE = 'should not be unnecessarily uppercased within a file';

// bad
export const THING_TO_BE_CHANGED = 'should obviously not be uppercased';

// bad
export let REASSIGNABLE_VARIABLE = 'do not use let with uppercase variables';

// ---

// allowed but does not supply semantic value
export const apiKey = 'SOMEKEY';

// better in most cases
export const API_KEY = 'SOMEKEY';

// ---

// bad - unnecessarily uppercases key while adding no semantic value
export const MAPPING = {
KEY: 'value'
};

// good
export const MAPPING = {
key: 'value'
};
```

### 如果属性/方法是`boolean`， 用 `isVal()` 或 `hasVal()（Airbnb）`

> 这样做更符合方法所做的事情

```javascript
// bad
if (!dragon.age()) {
  return false;
}

// good
if (!dragon.hasAge()) {
  return false;
}
```

### 数组以`s`或者`List`结尾

英语中的`s`代表某名词的复数形式。
另外`xxxxList`比起`xxxxArray`更符合语义。

```jsx
// Very Bad Arr这个缩写在火眼代码中出现了无数次
const userArr = [];

// good
const users = [];
const userList = [];
```

### jQuer（Airbnb）y

#### jQuery 对象用`$`变量表示（Airbnb）。

```javascript
// bad
const sidebar = $('.sidebar');

// good
const $sidebar = $('.sidebar');

// good
const $sidebarBtn = $('.sidebar-btn');
```

## CSS

### 全局 CSS 类名

使用 kebab 命名法（下划线分割单词）

```css
.foo-bar {
  margin-left: 10px;
}
```

#### cssModules

使用 camel 命名法（小驼峰）

> camel 命名法的 CSS 类名能使用`.`从 css modules 中访问到。而 kebab 命名则不行

```jsx
import styles from './style.less';
const foo = () => {
  // bad
  return <div className={styles['are-you-ok']} />;

  // good
  return <div className={styles.areYouOk} />;
};
```

### Vue style 块中的类名

kebab 命名法。同 css 全局属性

## 常量命名

全大写，单词间用下划线隔开

```jsx
export const PRIMARY_COLOR = '#b6782b';
```

## 枚举命名

枚举对象使用大驼峰命名，枚举内容的 key 使用全大写的命名。

**特别地，还需要在枚举的上面用 jsdoc 写上他的意思和枚举中元素的类型。**

如果枚举中的类型名字容易让人疑惑。请在名字上方也用 jsdoc 标注含义。

```js
/**
 * 报警类型
 * @enum {number}
 */
const AlertType = {
  /** 钉钉提醒 */
  DINGTALK: 1,

  /** 邮件提醒 */
  MAIL: 2
};
```

## 事件回调

### 在组件自身内部的事件回调的实现

以`handle`为前缀

```jsx
handleSummaryExport = () => {};
```

### 由父组件传入的事件回调

以`on`开头

```jsx
<input onChange={onNameChange} />
```

## 发起 Ajax 请求方法名

### 获取列表

`fetch` + 请求资源名 + `List`

```js
@action fetchUserList() {

}
```

### 获取资源详情

`fetch` + 资源名 + `Detail`

```js
@action fetchUserDetail() {

}
```

### 获取数据可视化中图的数据（此场景在火眼中出现较多）

`fetch` + 资源名 + `Chart`

```js
@action fetchRealTimeData() {

}
```

### 创建资源

`create` + 资源名

```js
@action createUser() {

}
```

### 修改资源

`update` + 资源吗

```js
@action updateUser() {

}
```

### 删除资源

`remove` + 资源名

```js
@action removeUser() {

}
```
