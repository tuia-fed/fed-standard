# 代码审查

## 流程

![image](//yun.dui88.com/qiho-h5/images/codereview.png)

## MR 规范

严禁一个 MR 里面有多个需求，除非它们是紧密关联的

**提交 MR 时候有一个描述框 请用简短但是足够说明问题的语言（理想是控制在 3 句话之内）来描述： 你改动了什么，解决了什么问题，需要代码审查的人留意那些影响比较大的改动。特别需要留意，如果对基础、公共的组件进行了改动，一定要另起一行特别说明。**

MR 应该在 1 小时内被响应，Reviewer 与代码的作者沟通好 Review 完成的期限，一般不要超过一天。

发起 Merge Request 以后, 钉钉的 Gitlab 机器人会自动发布一条发起 Marge request 的信息，如果比较着急，代码的作者可以在业务线的前端群（如火眼前端 team）中直接 at reviewer。（Bugfix 类 Review 必须当日解决，重大需求 Review 建议不要超过 2 天）

## Checklist

原则上保证每一行都经过 code review。Code review 中漏掉的 bad case，最终可能影响到组内所有成员（包括写 bad case 的提交者)。

**千万不要因为需要改动点太多影响上线而放过明显会影响他人开发体验的代码**

### 1. 降低 bug 率

- 健壮性检查  
  代码是否采取措施避免运行时错误 如使用正则时候是否对 null undefined 做兼容、JSON.parse 有没有 catch 处理，禁止隐式转换(`==`)等

#### 业务逻辑（根据线上问题、测试常见问题整理出核心点）

**等待补充**

### 2. 代码可维护性

#### 如果出现下列情况，必须打回 Review 要求修正

- MR 标题意义不明，请使用中文标题，说明这个 MR 做了什么
- 变量命名完全让人无法理解，参考变量命名规范以及每周 Bad case。可以用 codelf 或者 google translate 作为参考意见。
- 新的页面中没有使用枚举，使用 1, 2, 3, 4 等无意义的数字。对于旧项目上的迭代需求，可暂时放宽，使用注释代替。
- 明显的逻辑重复，代码复制粘贴，这种情况要求拆分逻辑到独立的函数
- 对于诡异的业务逻辑在上面写上注释
- 单组件代码量过长（500 行)
- 页面主要数据的 fetch 操作没有 catch 错误，没有对错误做出提示
- if 中判断字符串是否在某个集合内，使用大量 || 运算符。（应该使用 includes 代替)（此条仅限在新增的代码中）
- 公共组建泛用性太低，或者公共组件方法毫无注释
- 重复造轮子，Lodash 等库中有的
- 不遵循 React 设计模式的组件数据流（比如子组件拿到父组件 ref，或者通过 ref 拿取组件数据而不是 props）
- 多个复杂逻辑出现在一个函数中（这个时候建议拆分)
- 非正常的 JS 语法（比如 Promise 中套 Promise，已有 Promise 的情况又重新生成 Promise 等)
- 无意义地暂时屏蔽 eslint 的规则
- 被注释掉并且没写明原因的代码
- 无意义的 npm 包

可以给出建议的情形

- 更好的变量名，比原来表意更加明确的变量名（比如 Notice 到 DataSourceExplain)
- 更好的逻辑拆分，功能拆分
- 可以使用 map 替代 forEach 的情形
- 可以提取出封装成公共组件的
- 本开发规范中的其它改进建议

## 操作

### 开发人员

1. 在提测邮件发出前发起 Merge request，添加 assignee，在钉钉上通知 Reviewer 进行 code review.
2. 开发人员当面或在钉钉中描述业务前端的操作主流程
3. 修改第一次 code review 中产生的问题和提测阶段产生的 bug，在 gitlab 上标注 resolved
4. 通知 reviewer 进行第二次 code review
5. Code review 通过后，继续 Review

### Reviewer

1. 理清开发人员描述的业务前端操作主要流程
2. 提测前的第一次 code review 中，对代码给出修改意见，并在 gitlab 上评论
3. 在 bugfix 和第一次 code review 的修正之后，进行第二次 code review
4. 验收 code review 结果，直至通过
5. 将 code review 的 MR 发到后台 CR 委员会群
6. 每周周报中简述 Code review 状况，代码质量现状和趋势

## 反馈

### 每周周会 Bad case

每周周会会抽出十分钟时间对上周发现的 Bad case 进行回顾并且给出修正后的方案

### 后台 CR 委员会 群

部分的 Merge request 的 code review 会经过 ygy 和 ljw 抽查，收集各组的代码质量现状和趋势

### 对规范的改进

在这个项目的 Github issue 区提出你的意见
