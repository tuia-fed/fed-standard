# JS/JSX 文件注释规范

> 程序员最讨厌是四件事情：写注释、写文档、看到别人不写注释、看到别人不写文档。

注释是代码中不影响代码运行的一段提示。它的作用是，以少量的时间成本换来后期大量的可维护性上的收益。
****
## Jsdoc

基本的注释有`//`或者`/* */`，这是最基本的。此处要讲的注释类型叫做`jsdoc`，它是一种**文档注释**。比起普通注释，它能通过类似`@param`这样的 tag，告诉其它人和编辑器(`Vscode, sublime, atom, WebStorm等等`)你的代码中的额外信息，比如显式地指定一个变量的类型。而且，它的注释是支持 Markdown 格式的。在 Vscode 中，键入`/**`并按下回车，便能生成 Jsdoc 的注释块。

## Jsdoc 标签入门

### @type：类型系统

类型的标签以`@type开头`，例子如下

```jsx
// 基本类型
/**
@type {boolean} 布尔类型
@type {number} 数字类型
@type {string} 字符串类型
@type {null} null类型(一般不会单独作为类型，与其它类型组合)
@type {undefined} undefined类型，同上
@type {never} 表示代码永远不会返回，通常是代码必定会抛出一个错误或者死循环
// 组合类型
@type {number[]} 数字组成的数组，在类型后加`[]`即可表示这个类型的数组
@type {Array<number>} 数字组成数组的另一种形式
@type {[number, string, boolean]} 元祖，这个类型的第一个元素是number，第二个是string, 第三个是boolean
@type {Object<string, number>} 对象的values为number的对象，一般是一个字典
@type {?number} 可为null的数字
@type {!number} 不可为null的数字
@param {number} [foo] 可选参数
@param {number} [foo=123] 带默认值的可选参数

// 定义一个新的类型，新的类型是通过上面的法则组合的。
/**
  @typedef PropertiesHash
  @type {object}
  @property {string} id an ID.
  @property {string} name your name.
  @property {number} age your age.
 */

// 函数类型
/**
 * @type {(value: number) => void} 接收一个名为value类型为number的参数，并且没有返回值的函数
 * @type {(value: number, item: PropertiesHash) => number} 接收一个名为value类型为number，和一个名为item类型为PropertiesHash的参数，返回一个number的参数
 */

// 类型的使用非常简单，在声明语句的上一行使用 `@type {类型}`即可使用改类型
/** @type {PropertiesHash}  props会有`id`、`name`，`age`三个字段 */
var props;
```

#### 其它常用的类型

| 类型                | 含义                                                    | 备注                                                                |
| ------------------- | ------------------------------------------------------- | ------------------------------------------------------------------- |
| Promise<T>          | Promise 对象，尖括号中的 T 为 Promise fullfilled 后内容 |                                                                     |
| React.CSSProperties | React 组件中的 style 属性                               | 指定该属性可获得 jsx 中 style 属性的自动补全，写 React 时性价比极高 |
| React.ReactNode     | React 节点                                              | 一般用作参数渲染                                                    |
| React.ReactElement  | React 的 element                                        |                                                                     |

| React.MouseEvent | 鼠标事件的 event | |
| React.OnChangeEvent | input 等组件改变时的事件 | 可以获得 e.target.value 的引用 |

### @param

`@param`专门用于注解参数的类型，这个注解编辑器基本上都提供了非常完备的支持

#### 只有参数名的 param（不推荐这样写，因为没有指定参数的类型）

```js
/**
 * @param somebody
 */
function sayHello(somebody) {
  alert('Hello ' + somebody);
}
```

#### 附带类型的 param

下面的例子中，告诉了编辑器 somebody 的类型是 string，那么调用`somebody`的一些方法时，编辑器会根据`string`的类型，自动给出一些方法和属性的自动补全

```js
/**
 * @param {string} somebody
 */
function sayHello(somebody) {
  alert('Hello ' + somebody);
}

// 还可以给参数一个描述，像这样
/**
 * @param {string} somebody 某人的名字
 */
function sayHello(somebody) {
  alert('Hello ' + somebody);
}
```

#### 带属性的 param（即传入的参数是一个对象）

```jsx
/**
 * 江一个项目分配给一个雇员
 * @param {Object} employee 需要对项目负责的雇员
 * @param {string} employee.name 雇员的名字
 * @param {string} employee.department 雇员的住址
 */
Project.prototype.assign = function(employee) {
  // ...
};

// 如果用ES6的参数解构，你甚至还能这么写。这种写法在后面写React的函数式组件时尤其有用
/**
 * 江一个项目分配给一个雇员
 * @param {Object} employee 需要对项目负责的雇员
 * @param {string} employee.name 雇员的名字
 * @param {string} employee.department 雇员的住址
 */
Project.prototype.assign = function({ name, department }) {
  // ...
};

// 如果参数的类型是employee的数组，便需要这样注解
/**
 * 将一个项目分配给一系列雇员
 * @param {Object[]} employees 需要对项目负责的雇员们
 * @param {string} employees[].name 雇员的名字
 * @param {string} employees[].department 雇员的住址
 */
Project.prototype.assign = function(employees) {
  // ...
};
```

#### 可选参数

```jsx
/**
 * @param {string} [somebody] 某人的名字
 */
function sayHello(somebody = 'John Doe') {
  alert('Hello ' + somebody);
}

// 可选参数可以注解上默认值，像下面这样
/**
 * @param {string} [somebody='John Doe'] 某人的名字
 */
function sayHello(somebody = 'John Doe') {
  alert('Hello ' + somebody);
}
```

#### 多类型参数

```jsx
/**
 * @param {(string|string[])} [somebody='John Doe'] 某人的名字，或者名字组成的数组
 */
function sayHello(somebody) {
  if (!somebody) {
    somebody = 'John Doe';
  } else if (Array.isArray(somebody)) {
    somebody = somebody.join(', ');
  }
  alert('Hello ' + somebody);
}

// 任意类型

/**
 * @param {*} somebody 任何你想要的
 */
function sayHello(somebody) {
  console.log('Hello ' + JSON.stringify(somebody));
}
```

### 返回值`@returns`

并不是所有函数都需要注解返回值。建议当函数返回值类型不能确定时，使用`@returns`注解他的返回值。
下面这个例子中，注解返回值为`Promise<WLSX.WorkBook>`用`xlsx`库的`.d.ts`文件提供的定义补全了 Promise 中包含的类型，带来的收益是，提供了复杂的 `WLSX.WorkBook` 对象的代码提示。

```js
/**
 * 读取Excel文件，返回一个WorkBook对象。
 * **只支持xlsx格式的Excel文件！**
 * @param {File} file 文件
 * @returns {Promise<XLSX.WorkBook>} XLSX文件对象 @see https://github.com/SheetJS/js-xlsx#interface
 */
export default function readExcelFile(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = e => {
      const { result: data } = e.target;
      resolve(XLSX.read(data, { type: 'binary' }));
    };
    reader.onerror = e => {
      reject(e);
    };

    reader.readAsBinaryString(file);
  });
}
```

### 作者 @author

这个注解用于署上你的大名，~~让你的名字合法地留在公司的代码中~~

下面代码来自于上面这个 readExcelFile 工具方法文件头部的注释

```jsx
/**
 * 读取Excel文件的方法
 * @author Iron <lujianwei@duiba.com.cn>
 */
```

### 详见 @see

用于在代码中贴一段提示性的 url。

例子见返回值的代码中的`@returns`后的参数

### 例子 @example

提供一个例子 🌰。这个例子一般用于被广泛引用的公共组件、公共方法、公共函数中。

**显然需要使用 markdown 的代码块来提供更好的代码高亮支持**

````jsx
/**
 * 用于展示图表旁总计数据的组件
 * @description 按照echarts的Series。 **数据左侧小圆点颜色与Echarts图表中相同**
 * @param {Object} props
 * @param {string[]} props.totalList 每个总计项目的列表，**请确保传入前数据已格式化**
 * @param {string[]} props.nameList 每个总计项目名字的列表
 * @param {React.CSSProperties} props.style
 * @param {string} props.className
 * @example
 * ``` jsx
 * <TotalDataPanel totalList={[10, 20, 30, 40, 50]} nameList={['今日', '昨日', '过去七天', '额外日期']} />
 * ```
 */
function TotalDataPanel({ totalList, style, className, nameList }) {
  return (
    <div className={classNames('total-data-panel', className)} style={style}>
      {zip(totalList, nameList).map(([total, name], index) => (
        <div className='item' key={index}>
          <div className='title'>
            <i
              className='dot'
              style={{ background: colors[index % colors.length] }}
            />
            {name}：
          </div>
          <div className='value'>{total}</div>
        </div>
      ))}
    </div>
  );
}

export default TotalDataPanel;
````

## 实际规则与例子

### 公共方法（必须）

基本要求

- 公共方法功能
- 参数类型以及含义
- 业务需求使人迷惑的地方
- 返回值类型（在返回值类型不确定时）
- 作者

可选要求

- 例子（建议复杂的公共组件使用）
- 返回值说明

```jsx
/**
 * 读取Excel文件的方法
 * @author Iron <lujianwei@duiba.com.cn>
 */

/* global FileReader */
import XLSX from 'xlsx';

/**
 * 读取Excel文件，返回一个WorkBook对象。
 * **只支持xlsx格式的Excel文件！**
 * @param {File} file 文件
 * @returns {Promise<XLSX.WorkBook>} XLSX文件对象 @see https://github.com/SheetJS/js-xlsx#interface
 */
export default function readExcelFile(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = e => {
      const { result: data } = e.target;
      resolve(XLSX.read(data, { type: 'binary' }));
    };
    reader.onerror = e => {
      reject(e);
    };

    reader.readAsBinaryString(file);
  });
}
```

### 公共组件（必须）

必须的注释
- 组件的功能
- 组件的参数
- 作者

可选的
- 组件的例子


``` jsx
/**
 * 多选按钮组
 * @author Iron <lujianwei@duiba.com.cn>
 */
import * as React from 'react';
import { Button } from 'antd';
import classNames from 'classnames';

import './style.less';
const { Group: ButtonGroup } = Button;

/**
 * @typedef Option
 * @property {string} label
 * @property {*} value
 * @property {boolean} [disabled]
 */

/**
 * 按钮样式的多选按钮组
 * @param {Object} props
 * @param {string} [props.className]
 * @param {React.CSSProperties} [props.style]
 * @param {React.CSSProperties} [props.buttonStyle]
 * @param {boolean} [props.disabled] 禁用所有选择器
 * @param {'large'|'default'|'small'} [props.size='default'] 按钮大小
 * @param {Option[]} props.options 配置项
 * @param {Function} props.onChange 选项变化时的回调函数
 * @param {Array<any>} props.value 当前选中的值
 */
function SelectButtonGroup({
  className,
  style,
  buttonStyle,
  disabled,
  size = 'default',
  options,
  onChange,
  value
}) {
  /**
   * 选项点击时的回调
   * @param {*} current 当前点击的选项值
   * @param {boolean} hasChecked 是否选中
   */
  const handleCheck = (current, hasChecked) => () => {
    const originValueSet = new Set(value);
    hasChecked ? originValueSet.add(current) : originValueSet.delete(current);
    onChange(
      options
        .filter(option => originValueSet.has(option.value))
        .map(option => option.value)
    );
  };

  const checkedOptionSet = new Set(value);

  return (
    <ButtonGroup
      className={classNames('ant-select-button-group', className)}
      style={style}
    >
      {options.map(option => {
        const hasChecked = checkedOptionSet.has(option.value);
        return (
          <Button
            style={buttonStyle}
            key={option.value}
            disabled={disabled || option.disabled}
            ghost={hasChecked}
            type={hasChecked ? 'primary' : 'default'}
            size={size}
            onClick={handleCheck(option.value, !hasChecked)}
          >
            {option.label}
          </Button>
        );
      })}
    </ButtonGroup>
  );
}

export default SelectButtonGroup;


```
### 业务组件（必须）

``` jsx

/**
 * 规则集编辑弹层组件
 * @param {object} props
 * @param {boolean} props.visible 弹窗可见性
 * @param {boolean} props.commitLoading
 * @param {object[]} props.ruleList 规则列表
 * @param {AlertRuleCombinationType} props.ruleCombination 规则组合方式
 * @param {Function} props.onOk 确定点击时的回调
 * @param {Function} props.onCancel 取消时的回调
 * @param {boolean} props.validateLoading 校验是否在加载中
 * @param {Function} props.onValidateRuleSet 校验规则集时的回调
 * @param {Function} props.onAppendRule 添加新规则时的回调
 * @param {Function} props.onRuleChange 规则改变时的回调
 * @param {Function} props.onRuleDelete 规则被删除时的回调
 * @param {Function} props.onCombinationChange 组合条件改变时的回调
 */
function RuleSetEditModal({
  visible,
  ruleList,
  commitLoading,
  ruleCombination,
  onOk,
  validateLoading,
  onValidateRuleSet,
  onCancel,
  onAppendRule,
  onRuleChange,
  onRuleDelete,
  onCombinationChange
}) {
  const footer = (
    <div style={{ display: 'flex', justifyContent: 'space-between' }}>
      <div>
        <Button
          type="primary"
          loading={validateLoading}
          onClick={onValidateRuleSet}
        >
          合理性校验
        </Button>
      </div>
      <div>
        <Button onClick={onCancel}>取消</Button>
        <Button type="primary" loading={commitLoading} onClick={onOk}>
          确定
        </Button>
      </div>
    </div>
  );

  return (
    <Modal
      visible={visible}
      title="报警规则修改"
      width="824px"
      onCancel={onCancel}
      maskClosable={false}
      footer={footer}
    >
      <RuleEditList
        size="small"
        ruleList={ruleList}
        combination={ruleCombination}
        onAppendRule={onAppendRule}
        onRuleDelete={onRuleDelete}
        onRuleChange={onRuleChange}
        onCombinationChange={onCombinationChange}
        zIndex={1001}
      />
    </Modal>
  );
}

export default RuleSetEditModal;
```
### 生命周期内方法、回调（推荐）
可选的注释
- 参数类型
- 功能
- 
``` jsx
class MediaRealtimeContainer extends React.Component {
    /**
   * 等待编辑的待添加规则改变时的回调
   * @param {number} index 发生改变的规则下标
   * @param {'index'|'condition'|'value'} field 发生改变的字段
   * @param {string|number} value 变化的值
   */
  handlEditingRuleChange = (index, field, value) => {
    this.setAlertConfig(({ editingRuleList }) => ({
      editingRuleList: [
        ...editingRuleList.slice(0, index),
        {
          ...editingRuleList[index],
          [field]: value
        },
        ...editingRuleList.slice(index + 1)
      ]
    }));
  };
}
```